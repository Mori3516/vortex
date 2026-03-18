# 环境设置 (Environment Setup)

这些说明适用于使用更新工具链的开发 vortex 仓库。更新的工具链被认为是 2023 年 7 月 2 日起的任何 `master` 提交。有问题的工具链更新可以在这个 [commit](https://github.com/vortexgpgpu/vortex-dev/commit/0048496ba28d7b9a209a0e569d52d60f2b68fc04) 中查看。因此，如果你不确定是否使用新工具链，则应检查 `ci` 文件夹是否存在 `toolchain_prebuilt.sh` 脚本。此外，你应该注意到 `toolchain_install.sh` 脚本已将旧的 `llvm()` 拆分为 `llvm-vortex()` 和 `llvm-pocl()`。

## 在你自己的系统上设置 (Set Up on Your Own System)

Vortex 提供的工具链二进制文件构建在基于 Ubuntu 的系统上。要在你自己的系统上安装 Vortex，[请遵循这些说明](install_vortex.md)。

## 佐治亚理工学院学生和合作者的服务器 (Servers for Georgia Tech Students and Collaborators)

### Volvo

Volvo 是 HPArch 提供的 64 核服务器。你需要有效的凭据才能访问它。如果你还没有访问权限，你可以联系你的导师询问有关设置帐户的信息。

Volvo 上的设置：

1. 连接到佐治亚理工学院的 VPN 或 ssh 到校园内的另一台机器
2. `ssh volvo.cc.gatech.edu`
3. 将 Vortex 克隆到你的主目录：`git clone --recursive https://github.com/vortexgpgpu/vortex.git`
4. `source /nethome/software/set_vortex_env.sh` 以设置必要的环境变量。
5. 在 `vortex` 根目录中执行 `make -s`
6. 运行测试程序：`./ci/blackbox.sh --cores=2 --app=dogfood`

### Nio

Nio 是 HPArch 提供的 20 核桌面服务器。如果你有访问 Volvo 的权限，你也有访问 Nio 的权限。

Nio 上的设置：

1. 连接到佐治亚理工学院的 VPN 或 ssh 到校园内的另一台机器
2. `ssh nio.cc.gatech.edu`
3. 将 Vortex 克隆到你的主目录：`git clone --recursive https://github.com/vortexgpgpu/vortex.git`
4. `source /opt/set_vortex_env_dev.sh` 以设置必要的环境变量。
5. 在 `vortex` 根目录中执行 `make -s`
6. 运行测试程序：`./ci/blackbox.sh --cores=2 --app=dogfood`

## Docker (实验性) (Docker (Experimental))

Docker 允许创建、共享和使用隔离的预构建环境。ARM 处理器所需的仿真模式将导致性能下降。目前，dockerfile 未包含在官方 vortex 仓库中，且未积极维护或支持。

### 使用 Docker 设置 (Setup with Docker)

1. 将仓库递归克隆到你的本地机器：`git clone --recursive https://github.com/vortexgpgpu/vortex.git`
2. 从[此处](https://github.gatech.edu/gist/usubramanya3/f1bf3e953faa38a6372e1292ffd0b65c)下载 dockerfile 并将其放置在仓库的根目录中。
3. 将 Dockerfile 构建到镜像中：`docker build --platform=linux/amd64 -t vortex -f dockerfile .`
4. 运行基于该镜像的容器：`docker run --rm -v ./:/root/vortex/ -it --name vtx-dev --privileged=true --platform=linux/amd64 vortex`
5. 安装工具链 `./ci/toolchain_install.sh --all`（每个容器一次）
6. 在 `vortex` 根目录中执行 `make -s`
7. 运行测试程序：`./ci/blackbox.sh --cores=2 --app=dogfood`

你可以退出容器并恢复已退出的容器或启动第二个终端会话 `docker exec -it <container-name> bash`