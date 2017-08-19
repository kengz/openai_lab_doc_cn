# <a name="usage"></a>用法 Usage

*了解实验室的框架和演示，跳到下一节。 [Framework and Demo, skip to the next section.](#structure)*

运行生产实验室的一般流程是：

1. 在 `rl/spec/*_experiment_specs.json` 中指定实验规范，例如 `"dqn", "lunar_dqn"`
2.指定要在 `config/production.json` 中运行的实验的名称
3.运行实验室，例如 `grunt -prod -resume`

Grunt将读取 `config/` 中的JSON文件，它看起来像：

```json
{
  "data_sync_destination": "~/Dropbox/openai_lab/data",
  "NOTI_SLACK_DEST": "#rl-monitor",
  "NOTI_SLACK_TOK": "GET_SLACK_BOT_TOKEN_FROM_https://my.slack.com/services/new/bot",
  "experiments": [
    "dqn",
    "lunar_dqn"
  ]
}
```


## 命令 Commands

我们使用 [Grunt](http://gruntjs.com/) 运行实验室 - 设置实验，暂停/恢复实验室，运行分析，同步数据，完成后通知。 在内部 `grunt` 运行 `python` 命令（更难使用），记录到 stdout为 `>> Composed command: ... python3 main.py -bp -t 5 -e dev_dqn | tee ./data/terminal.log;`

有用的grunt命令是：

```shell
# when developing experiments specified in default.json
grunt
# developing, run single best trial without param selection
grunt -best

# run real lab experiments specified in production.json
grunt -prod
# run lab over ssh on remote server
grunt -prod -remote
# resume lab (previously incomplete experiments)
grunt -prod -remote -resume


# clear data/ folder and cache files
grunt clear
```

有关完整的 [Grunt Command Reference](#grunt-cmd) 或 [Python Command Reference](#python-cmd)，请参见下文。


**development** mode:

- 所有grunt命令默认为此模式
- 在 `config / default.json` 中指定你的dev实验
- 仅在开发新算法时使用
- 文件同步处于模拟模式（模拟日志没有真正的文件复制）
- 没有自动通知


**production** mode:

- 将 `-prod` 标志附加到 `grunt` 命令
- 在 `config/production.json` 中指定你的完整实验
- 运行实验时使用
- 文件同步是真实的
- 自动通知Slack频道


## 远程运行 Run Remotely

如果您正在使用远程服务器，请在 `screen` 下运行命令。 也就是说，通过 `ssh` 登录，启动屏幕，运行，然后分离屏幕。

```shell
# enter the screen with the name "lab"
screen -S lab
# run real lab over ssh, in resume mode
grunt -prod -remote -resume
# use Cmd+A+D to detach from screen, then Cmd+D to disconnect ssh

# to resume screen next time
screen -r lab
# use Cmd+D to terminate screen when lab ends
```

由于远程服务器不在，您应该偶尔检查系统状态，以确保没有超出进程（内存增长，大进程，过热）。 使用 [`glances`](https://github.com/nicolargo/glances) （已经安装在`bin/setup`中）来监控昂贵的机器。

<aside class="notice">
To monitor your system (CPU, RAM, GPU), run <code>glances</code>
</aside>

![](./images/glances.png "Glances to monitor your system")
*Glances on remote server beast.*


## 恢复实验室 Resume Lab

实验需要很长时间才能完成，如果您的进程被终止，则恢复实验室是一个简单的 `-resume` 标志：`grunt -prod -remote -resume`。 这将使用 `config/history.json`：

```json
{
  "dqn": "dqn-2017_03_19_004714"
}
```

`config/history.json` 在最后一次运行中创建，将`experiment_name` 映射为 `experimental_id` ，并根据`experiment_id` 恢复任何不完整的实验。 当然可以手动调整文件来设置恢复目标。


## <a name="grunt-cmd"></a>Grunt Command Reference

默认情况下，`grunt` 命令（没有任务或标志）使用 `config/default.json` 在 `development` 模式下运行实验室。

基本的grunt命令模式是

```shell
grunt <task> -<flag>

# again, the useful grunt commands are:

# when developing experiments specified in default.json
grunt
# developing, run single best trial without param selection
grunt -best

# run real lab experiments specified in production.json
grunt -prod
# run lab over ssh on remote server
grunt -prod -remote
# resume lab (previously incomplete experiments)
grunt -prod -remote -resume

# clear data/ folder and cache files
grunt clear
```

The `<task>`s are:

- _(default empty)_: run the lab
- `clear`: clear the `data/` folder and cache files. **Be careful** and make sure your data is already copied to the sync location


The `<flag>`s are:

- `-prod`: production mode, use `config/production.json`
- `-resume`: resume incomplete experiments from `config/history.json`
- `-remote`: when running over SSH, supplies this to use a fake display
- `-best`: run the finalized experiments with gym rendering and live plotting; without param selection. This uses the default `param` in `experiment_specs.json` that shall be updated to the best found.
- `-debug`: verbose debug logging. This is for lab-level development only.
- `-quiet`: mute all python logging in grunt. This is for lab-level development only.


## <a name="python-cmd"></a>Python Command Reference

Python命令在 `gruntfile.js` 下的 `composeCommand` 函数下调用。 如果需要，请更改。

<aside class="notice">
记住在使用普通的python命令时激活virtualenv / conda。
</aside>

基本的 python 命令模式是：

```shell
python3 main.py -<flag>

# most common example, with piping of terminal log
python3 main.py -bp -t 5 -e dqn | tee -a ./data/terminal.log;
```

The python command <flag>s are:

- `-b`: blind mode, do not render graphics. Default: `False`
- `-d`: log debug info. Default: `False`
- `-e <experiment>`: specify which inside the `rl/spec/*_experiment_spec.json` to run. Default: `-e dev_dqn`. Can be a `experiment_name, experiment_id`.
- `-p`: run param selection. Default: `False`
- `-q`: quiet mode, log warning only. Default: `False`
- `-t <times>`: the number of sessions to run per trial. Default: `1`
- `-x <max_episodes>`: Manually specifiy max number of episodes per trial. Default: `-1` and program defaults to value in `rl/spec/problems.json`
