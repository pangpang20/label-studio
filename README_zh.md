<img src="https://user-images.githubusercontent.com/12534576/192582340-4c9e4401-1fe6-4dbb-95bb-fdbba5493f61.png"/>

![GitHub](https://img.shields.io/github/license/heartexlabs/label-studio?logo=heartex) ![label-studio:build](https://github.com/HumanSignal/label-studio/workflows/label-studio:build/badge.svg) ![GitHub release](https://img.shields.io/github/v/release/heartexlabs/label-studio?include_prereleases)

[官网](https://labelstud.io/) • [文档](https://labelstud.io/guide/) • [加入 Slack 社区 <img src="https://app.heartex.ai/docs/images/slack-mini.png" width="18px"/>](https://slack.labelstud.io/?source=github-1)


## 什么是 Label Studio？

Label Studio 是一款开源的数据标注工具。它提供了简洁直观的用户界面，支持对音频、文本、图像、视频和时间序列等多种数据类型进行标注，并可导出为多种模型格式。它可用于准备原始数据或改进现有训练数据，从而获得更准确的机器学习模型。

- [快速体验 Label Studio](#快速体验-label-studio)
- [Label Studio 的功能特性](#label-studio-的功能特性)
- [内置标注模板](#内置标注模板)
- [集成机器学习模型](#集成机器学习模型)
- [与现有工具集成](#与现有工具集成)

![Label Studio 标注不同类型数据的演示](/images/annotation_examples.gif)

有自定义数据集？您可以根据需求定制 Label Studio。阅读[入门博客文章](https://towardsdatascience.com/introducing-label-studio-a-swiss-army-knife-of-data-labeling-140c1be92881)了解更多信息。

## 快速体验 Label Studio

在本地安装 Label Studio 或部署到云端。[或者注册免费试用我们的 Starter Cloud 版本！](https://humansignal.com/platform/starter-cloud/) 您可以在[这里](https://labelstud.io/guide/label_studio_compare)了解各版本的详细信息。

- [使用 Docker 本地安装](#使用-docker-本地安装)
- [使用 Docker Compose 运行（Label Studio + Nginx + PostgreSQL）](#使用-docker-compose-运行)
- [使用 pip 本地安装](#使用-pip-本地安装)
- [使用 poetry 本地安装](#使用-poetry-本地安装)
- [使用 Anaconda 本地安装](#使用-anaconda-本地安装)
- [本地开发环境安装](#本地开发环境安装)
- [部署到云端](#部署到云端)

### 使用 Docker 本地安装

Label Studio 官方 Docker 镜像在[这里](https://hub.docker.com/r/heartexlabs/label-studio)，可以通过 `docker pull` 下载。
在 Docker 容器中运行 Label Studio，然后通过 `http://localhost:8080` 访问。

```bash
docker pull heartexlabs/label-studio:latest
docker run -it -p 8080:8080 -v $(pwd)/mydata:/label-studio/data heartexlabs/label-studio:latest
```

所有生成的资源（包括 SQLite3 数据库存储 `label_studio.sqlite3` 和上传的文件）都可以在 `./mydata` 目录中找到。

#### 覆盖默认 Docker 安装配置

您可以通过附加新参数来覆盖默认启动命令：
```bash
docker run -it -p 8080:8080 -v $(pwd)/mydata:/label-studio/data heartexlabs/label-studio:latest label-studio --log-level DEBUG
```

#### 本地构建 Docker 镜像

如果需要本地构建镜像，运行：
```bash
docker build -t heartexlabs/label-studio:latest .
```

### 使用 Docker Compose 运行

Docker Compose 脚本提供了生产级别的部署方案，包含以下组件：

- Label Studio
- [Nginx](https://www.nginx.com/) - 用于加载各种静态数据的代理 Web 服务器，包括上传的音频、图像等
- [PostgreSQL](https://www.postgresql.org/) - 生产级数据库，替代性能较低的 SQLite3

使用以下命令启动应用，通过 `http://localhost` 访问：
```bash
docker-compose up
```

### 使用 Docker Compose + MinIO 运行

您还可以配合 MinIO 服务器使用本地 S3 存储。当您想在本地系统上测试 S3 存储行为时，这非常有用。使用以下命令启动：
````bash
# 在 Linux 上，如果您不是 docker 组成员，请添加 sudo
docker compose -f docker-compose.yml -f docker-compose.minio.yml up -d
````
如果您没有静态 IP 地址，需要在 hosts 文件中添加条目，以便 Label Studio 和浏览器都能访问 MinIO 服务器。更多详细说明请参考[数据存储指南](docs/source/guide/storedata.md)。

### 使用 pip 本地安装

```bash
# 需要 Python >=3.10
pip install label-studio

# 在 http://localhost:8080 启动服务器
label-studio
```

### 使用 poetry 本地安装

```bash
# 安装 poetry
pip install poetry

# 创建 poetry 环境
poetry new my-label-studio
cd my-label-studio
poetry add label-studio

# 激活 poetry 环境
poetry shell

# 在 http://localhost:8080 启动服务器
label-studio
```

### 使用 Anaconda 本地安装

```bash
conda create --name label-studio
conda activate label-studio
conda install psycopg2
pip install label-studio
```

### 本地开发环境安装

您可以在本地运行最新版本的 Label Studio，无需从 PyPI 安装包。

```bash
# 安装所有包依赖
pip install poetry
poetry install
# 运行数据库迁移
python label_studio/manage.py migrate
python label_studio/manage.py collectstatic
# 在 http://localhost:8080 以开发模式启动服务器
python label_studio/manage.py runserver
```

### 部署到云端

您可以通过一键部署将 Label Studio 部署到 Heroku、Microsoft Azure 或 Google Cloud Platform：

<a href="https://www.heroku.com/deploy?template=https://github.com/HumanSignal/label-studio/tree/heroku-persistent-pg"><img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy" height="30px"></a>
[<img src="https://aka.ms/deploytoazurebutton" height="30px">](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhumansignal%2Flabel-studio%2Fdevelop%2Fazuredeploy.json)
[<img src="https://deploy.cloud.run/button.svg" height="30px">](https://deploy.cloud.run)

#### 应用前端更改

有关更新前端的信息，请参阅 [label-studio/web/README.md](https://github.com/HumanSignal/label-studio/blob/develop/web/README.md#installation-instructions)。

#### 在 Windows 上安装依赖

要在 Windows 上运行 Label Studio，请从 [Gohlke builds](https://www.lfd.uci.edu/~gohlke/pythonlibs) 下载并安装以下 wheel 包，确保使用正确版本的 Python：
- [lxml](https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml)

```bash
# 升级 pip
pip install -U pip

# 如果您使用 Win64 和 Python 3.8，安装从 Gohlke 下载的包：
pip install lxml‑4.5.0‑cp38‑cp38‑win_amd64.whl

# 安装 label studio
pip install label-studio
```

### 运行测试套件

将测试依赖添加到本地安装：

```bash
poetry install --with test
```

或者，可以在已安装测试依赖的 Docker 容器中运行单元测试：

```bash
make build-testing-image
make docker-testing-shell
```

无论哪种方式，运行单元测试：

```bash
cd label_studio

# sqlite3
DJANGO_DB=sqlite DJANGO_SETTINGS_MODULE=core.settings.label_studio pytest -vv

# postgres（假设使用默认的 postgres 用户、数据库和密码。
# 在 Docker 测试容器中不进行额外配置将无法工作）
DJANGO_DB=default DJANGO_SETTINGS_MODULE=core.settings.label_studio pytest -vv
```

## Label Studio 的功能特性

https://github.com/user-attachments/assets/525ad5ff-6904-4398-b507-7e8954268d69

- **多用户标注** - 注册和登录后，创建的标注与您的账户关联
- **多项目管理** - 在一个实例中处理所有数据集
- **简洁设计** - 帮助您专注于任务，而非软件操作
- **可配置的标签格式** - 自定义可视化界面以满足特定标注需求
- **多数据类型支持** - 包括图像、音频、文本、HTML、时间序列和视频
- **多种导入方式** - 支持从文件或云存储导入（Amazon AWS S3、Google Cloud Storage），以及 JSON、CSV、TSV、RAR 和 ZIP 压缩包
- **机器学习模型集成** - 可视化和比较不同模型的预测结果，支持预标注
- **嵌入数据管道** - REST API 使其易于集成到您的工作流程中

## 内置标注模板

Label Studio 包含多种模板帮助您标注数据，也可以使用专门设计的配置语言创建自定义模板。最常见的模板和标注用例包括：

<img src="/images/template-types.png" />

## 集成机器学习模型

使用 Label Studio Machine Learning SDK 连接您喜欢的机器学习模型。步骤如下：

1. 启动您自己的机器学习后端服务器。参见[详细说明](https://github.com/HumanSignal/label-studio-ml-backend)。
2. 在项目设置的模型页面中将 Label Studio 连接到服务器。

这样可以实现：

- **预标注** - 使用模型预测对数据进行预标注
- **在线学习** - 在创建新标注的同时重新训练模型
- **主动学习** - 仅标注数据中最复杂的样本

## 与现有工具集成

您可以将 Label Studio 作为机器学习工作流中的独立部分使用，也可以将前端或后端集成到现有工具中。

## 生态系统

| 项目 | 描述 |
|-|-|
| label-studio | 服务器，以 pip 包形式分发 |
| [前端库](web/libs/editor/) | Label Studio 前端库，使用 React 构建 UI，使用 mobx-state-tree 进行状态管理 |
| [数据管理器库](web/libs/datamanager/) | 数据管理器库，用于数据探索工具 |
| [label-studio-converter](https://github.com/HumanSignal/label-studio-sdk/tree/master/src/label_studio_sdk/converter) | 将标注编码为您喜欢的机器学习库的格式 |
| [label-studio-transformers](https://github.com/HumanSignal/label-studio-transformers) | 为 Label Studio 连接和配置的 Transformers 库 |

## 引用

在文章的**参考文献**部分包含 Label Studio 的引用：

```tex
@misc{Label Studio,
  title={{Label Studio}: Data labeling software},
  url={https://github.com/HumanSignal/label-studio},
  note={Open source software available from https://github.com/HumanSignal/label-studio},
  author={
    Maxim Tkachenko and
    Mikhail Malyuk and
    Andrey Holmanyuk and
    Nikolai Liubimov},
  year={2020-2025},
}
```

## 许可证

本软件基于 [Apache 2.0 许可证](/LICENSE) 授权，© [Heartex](https://www.heartex.com/)。2020-2025

<img src="https://user-images.githubusercontent.com/12534576/192582529-cf628f58-abc5-479b-a0d4-8a3542a4b35e.png" title="Hey everyone!" width="180" />
