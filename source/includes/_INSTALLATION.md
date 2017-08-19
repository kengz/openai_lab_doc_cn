# <a name="installation"></a>安装

**1\.** **克隆资源库**

`git clone https://github.com/kengz/openai_lab.git`

*如果您计划提交代码，请将此资源库替换为克隆代码。*


**2\.** **安装依赖关系**

运行以下命令进行安装：

- 系统依赖于您的操作系统
- 项目依赖关系

*要在远程服务器上快速重复设置，而不是使用这些命令，请运行等效的安装脚本： `./bin/setup`*

```shell
# cd into project directory
cd openai_lab/

### MacOS System Dependencies
# Homebrew
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
# OpenAI Gym dependencies
brew install cmake boost boost-python sdl2 swig wget
# noti
(curl -L https://github.com/variadico/noti/releases/download/v2.5.0/noti2.5.0.darwin-amd64.tar.gz | tar -xz); sudo mv noti /usr/local/bin/
# Node >= v7.0
brew install node
# Python >= v3.0
brew install python3

### Linux Ubuntu System Dependencies
sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test && sudo apt-get update
sudo apt-get install -y gcc-4.9 g++-4.9 libhdf5-dev libopenblas-dev git python3-tk tk-dev
# OpenAI Gym dependencies
sudo apt-get install -y cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev swig
# noti
(curl -L https://github.com/variadico/noti/releases/download/v2.5.0/noti2.5.0.linux-amd64.tar.gz | tar -xz); sudo mv noti /usr/local/bin/
# Node >= v7.0
(curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -); sudo apt-get install -y nodejs
# Python >= v3.0
sudo apt-get -y install python3-dev python3-pip python3-setuptools


### Project Dependencies
./bin/copy-config
npm install; sudo npm i -g grunt-cli
# option 1: pip (ensure it is python3)
pip3 install -r requirements.txt
# option 2: virtualenv
virtualenv .env
source .env/bin/activate
pip3 install -r requirements.txt
# option 3: conda
conda env create -f environment.yml
source activate openai_lab
```


**3\.** **设置配置文件**

运行 `./bin/copy-config`。 这将从模板创建配置文件，需要 Lab 使用 [usage](#usage):

- `config/default.json` 用于本地开发，当 `grunt` 在没有 `-prod` 生产标志的情况下运行时使用。
- `config/production.json` 当 `grunt -prod` 与生产标志`-prod`一起运行时，用于生产 Lab 运行。


## <a name="quickstart"></a>快速入门

实验室 Lab 带有最佳解决方案的实验。在下面运行你的第一个。


### Single Trial

使用 Lab 命令运行单一最佳试验：`grunt -best`

或者，上面引用的简单python命令是：`python3 main.py -e quickstart_dqn`

<aside class="notice">
记住在使用普通的python命令时激活 virtualenv / conda。
</aside>

然后检查您的 `./data/` 文件夹中的图形和数据文件。

建议使用grunt命令，因为更容易安排和运行多个实验。 它来自 `config/default.json` ，现在应该有 `quickstart_dqn`; 更多可以添加。

```json
{
  "data_sync_destination": "~/Dropbox/openai_lab/data",
  "NOTI_SLACK_DEST": "#rl-monitor",
  "NOTI_SLACK_TOK": "GET_SLACK_BOT_TOKEN_FROM_https://my.slack.com/services/new/bot",
  "experiments": [
    "quickstart_dqn"
  ]
}
```

这个试验是 `DQN` 解决 `Cartpole-v0` 的最好的找到的解决方案代理 [the best found solution agent](https://github.com/kengz/openai_lab/pull/73)。 你应该看到实验室运行如下：

![](./images/lab_demo_dqn.gif "Timelapse of OpenAI Lab")


### Experiment with Multiple Trials

下一步是运行一个小型实验，搜索最佳的试用解决方案。

```json
{
  "quickstart_dqn": {
    "problem": "CartPole-v0",
    "Agent": "DQN",
    ...
    "param_range": {
      "lr": [0.001, 0.01],
      "hidden_layers": [
        [32],
        [64]
      ]
    }
  }
}
```

这位于 `rl/spec/classic_experiment_specs.json` 的 `quickstart_dqn` 下。 实验研究了不同学习率 `lr` 和 DQN 神经网络架构 `hidden_layers` 的影响。 如果您愿意，请更改 `param_range` 以尝试更多的值。

然后，运行：`grunt`

或者，简单的python命令是：`python3 main.py -bp -e dqn`

然后检查您的 `./data/` 文件夹中的图形和数据文件。

实验需要约15分钟（取决于您的机器）。 它将从试验中产生实验数据。参考分析 [Analysis](#analysis) 如何解释它们。


### 下一个

我们推荐：

- 继续阅读下面的可选安装步骤。
- 解决方案 [Solutions](#solutions) ，查看一些现有的解决方案来启动您的代理商，以及找到环境/高分以打败。
- 代理 [Agents](#agents) 关于如何从现有组件创建代理，然后添加您自己的。
- 用法 [Usage](#usage) 继续阅读文档。


## 更新实验室

检查实验室的最新发布版本 [release versions here](https://github.com/kengz/openai_lab/releases).

- 如果您直接克隆Lab，请用`git pull`更新
- 如果你分叉，设置远程 [setup a remote](https://help.github.com/articles/configuring-a-remote-for-a-fork/) 和 [update fork](https://help.github.com/articles/syncing-a-fork/)

```shell
# update direct clone
git pull

# update fork
git fetch upstream
git merge upstream/master
```


## 设置数据自动同步

在远程服务器上运行实验室时，我们发现数据文件同步非常有用。 这使我们可以在我们的Dropbox应用程序，计算机或智能手机上实时查看实验图和数据。

对于自动同步实验室 `data/`，我们使用[Grunt](http://gruntjs.com/)文件监视器将数据文件自动复制到Dropbox。 在你的收件箱中，设置一个共享文件夹 `~/Dropbox/openai_lab/data` 并同步到桌面。

在 `config / {default.json，production.json}` 中设置配置密钥 `data_sync_destination`。


<aside class="notice">
这一步是可选的; 仅在运行生产模式时才需要。
</aside>


## 设置自动通知

实验需要一段时间才能运行，我们发现完成后会自动通知实验。 我们使用 [noti](https://github.com/variadico/noti)，它也随 `bin/setup` 一起安装。

设置一个Slack，创建一个新的通道 `#rl_monitor`，并获得一个 [Slack bot token](https://my.slack.com/services/new/bot).

在 `config/{default.json, production.json}`中设置配置键 `NOTI_SLACK_DEST`, `NOTI_SLACK_TOK`。

<aside class="notice">
这一步是可选的; 运行生产模式时很有用。
</aside>

![](./images/noti.png "Notifications from the lab running on our remote server beast")
_Notifications from the lab running on our remote server beast._


## 硬件

为了设置自己的硬件，特别是使用GPU，谷歌搜索将帮助我们更多的可能。 另外，设置通常是不平凡的，因为有这么多移动部件。 以下是推荐的参考文献：

- [A ~$1000 PC build](https://pcpartpicker.com/list/xdbWBP) (more expensive now ~$1200; buy your parts during Black Friday/sales.)
- [The official TensorFlow installation guide, with GPU setup info](https://www.tensorflow.org/install/install_linux)
- [Getting CUDA 8 to Work With openAI Gym on AWS and Compiling Tensorflow for CUDA 8 Compatibility](http://christopher5106.github.io/nvidia/2016/12/30/commands-nvidia-install-ubuntu-16-04.html)
- [Major OpenAI issue with SSH with xvfb failing with NVIDIA Driver due to opengl files](https://github.com/openai/gym/issues/366)
- [NVIDIA cannot install due to X server running](http://askubuntu.com/questions/149206/how-to-install-nvidia-run)
- [When login fails on Ubuntu after Nvidia installation](http://askubuntu.com/questions/759641/cant-get-nvidia-drivers-working-with-16-04-logs-out-right-after-login)
