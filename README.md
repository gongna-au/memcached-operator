# memcached-operator


### init

```shell
operator-sdk init --domain example.com --repo github.com/memcached-operator 
```


### Create API

```shell
operator-sdk create api --group cache --version v1alpha1 --kind Memcached --resource --controller
```
operator-sdk create api 命令是用来创建新的自定义资源 (CR) API 定义的。让我们来分析一下这个命令中的每个参数：

> `--group cache`: 这个参数用于指定新创建的 CR 所属的组 (group)。在 Kubernetes 中，API 是通过组来进行分类的，这样有助于在不同的环境和领域中更好地组织 API。

> `--version v1alpha1`: 这个参数用于指定新创建的 CR 的版本。版本可以让我们更好地管理和追踪 API 的不同迭代和改进。

> `--kind Memcached`: 这个参数用于指定新创建的 CR 的种类 (kind)。种类是一个很重要的概念，它描述了我们要创建的对象的本质——比如这个例子中，我们要创建的是一个 Memcached 类型的对象。


> `--resource`: 这个标志表示我们希望为这个 API 创建一个新的资源。这将在 /api/ 目录下生成相关的文件。


> `--controller`: 这个标志表示我们希望为这个 API 创建一个新的控制器。这将在 /controllers/ 目录下生成相关的文件。


在执行这个命令之后，Operator SDK 会在项目中生成一些新的文件，包括定义你的 CRD (Custom Resource Definition) 的 Go 文件，和实现你的 CRD 控制器的 Go 文件。这些文件提供了一些基础的代码骨架，你可以根据需要进行修改和添加代码，以实现你的 Operator 的功能。

### Make 

```shell
make manifests
```

make manifests 命令用于自动生成和更新您的 Operator 项目所需的一些自定义资源定义（CRDs）和 RBAC 清单文件。这些文件主要用于定义你的自定义资源的 schema（例如，字段名称、类型、是否必须等等）和 Operator 的角色、角色绑定、服务账户等。


### Build/Push

```shell
make docker-build docker-push IMG="example.com/memcached-operator:v0.0.1"
```


是一个组合的命令，由两部分组成：make docker-build 和 make docker-push，同时指定了一个 IMG 环境变量。让我们分别解析一下：

`make docker-build`: 这个命令会使用 Docker 来构建一个 Docker 镜像。在这个命令中，Dockerfile 文件应当已经在你的项目中定义好了，包含了如何构建你的应用的所有信息。

`make docker-push`: 这个命令会将之前构建的 Docker 镜像推送到 Docker Registry。可以是 Docker Hub，也可以是私有的 Docker Registry。

`IMG="example.com/memcached-operator:v0.0.1"`: 这部分是定义了一个环境变量 IMG，它的值就是我们将要构建并推送的 Docker 镜像的名字。这个名字包含了 Docker Registry 的地址（example.com）、镜像的名字（memcached-operator）以及镜像的版本标签（v0.0.1）。

这个命令整体的意义就是：构建一个 Docker 镜像，并将其推送到指定的 Docker Registry 中。这个镜像包含了你的 Operator 的所有代码和其运行所需的环境。你可以通过 Kubernetes 在目标集群上运行这个 Docker 镜像来部署你的 Operator。

你需要将这个命令中的 Docker 镜像地址修改为你的 Docker Hub 账号名。Docker Hub 使用用户名作为镜像的命名空间，所以你需要将 example.com/memcached-operator:v0.0.1 更改为 yourname/memcached-operator:v0.0.1。所以，你的命令应该修改为：


```shell
make docker-build docker-push IMG="yourname/memcached-operator:v0.0.1"
```
在运行这个命令之前，你需要确保你已经在本地机器上使用 docker login 命令登录了你的 Docker Hub 账号。这样，你才能推送镜像到 Docker Hub。如果你还没有登录，你可以使用下面的命令登录：

```shell
docker login -u yourname
```

然后输入你的 Docker Hub 密码即可。这样你就能成功推送镜像到你的 Docker Hub 账号下了。


### Olm

```shell
operator-sdk olm install
```
operator-sdk olm install 是一个用于安装 Operator Lifecycle Manager (OLM) 到集群的命令。Operator Lifecycle Manager 是一个在 Kubernetes 集群上管理 Operator 的生命周期的工具。这个命令会将 OLM 部署到你当前上下文指向的 Kubernetes 集群。

简单来说，operator-sdk olm install 这个命令的工作是将 Operator Lifecycle Manager 部署到你的 Kubernetes 集群中。OLM 是一个帮助管理 Kubernetes 集群中的 Operators 的工具，它可以帮助你安装、更新和管理你的 Operators。

OLM 通过定义一种新的定制资源 (Custom Resource Definition，CRD) —— ClusterServiceVersion (CSV)，来描述和管理一个 Operator 的生命周期。CSV 不仅记录了一个 Operator 的所有特性，还包括了如何部署、如何配置 Operator，以及如何处理 Operator 版本升级等等生命周期相关的信息。

因此，如果你正在开发一个 Operator，或者你想在你的集群中使用 Operator，那么你可能需要使用 OLM 来帮助你管理它们。为了使用 OLM，你需要首先将其安装到你的集群中，而 operator-sdk olm install 命令就是用于这个目的。

请注意，如果你的集群已经安装了 OLM，那么你就不需要再运行这个命令。如果你不确定你的集群是否已经安装了 OLM，你可以运行 operator-sdk olm status 命令来检查。如果这个命令返回 "OLM has been installed"，那么就说明你的集群已经安装了 OLM，否则你就需要运行 operator-sdk olm install 来安装它。

### 部署

构建并推送 Operator 镜像：在你的 Operator 项目目录下，使用 
`make docker-build docker-push IMG=<your-image>` 命令构建并推送你的 Operator 镜像到镜像仓库。你需要将 `<your-image>` 替换成你的镜像仓库地址。

例如:

```bash
make docker-build docker-push IMG=yourname/memcached-operat
```

更新你的 Operator 部署配置：更新`config/default/manager_image_patch.yaml` 文件，将其中的镜像地址替换成你在上一步构建并推送的 Operator 镜像地址。

部署 Operator：在 Operator 项目目录下，运行如下命令以将 Operator 部署到集群上：

```bash
make deploy IMG=yourname/memcached-operator:v0.0.1
```
同样，你需要将` yourname/memcached-operator:v0.0.1` 替换成你的 Operator 镜像地址。

验证 Operator 是否部署成功：运行以下命令，查看 Operator 的 pod 是否正常运行：

```bash
kubectl get pods -n <operator-namespace>
```
将 `<operator-namespace>` 替换为你的 Operator 被部署的命名空间。

一旦 Operator 成功部署，你就可以开始创建并管理你的 CRD 实例了。

如果你想要通过 OLM 来管理你的 Operator，那么你需要创建一份 ClusterServiceVersion (CSV) 文件，并使用 operator-sdk run bundle 命令来运行你的 Operator。CSV 文件描述了 Operator 的详细信息，包括如何部署 Operator、Operator 的功能以及如何升级 Operator 等。创建 CSV 文件的过程较为复杂，具体操作可以参考官方文档或者找一份已经创建好的 CSV 文件作为参考。


