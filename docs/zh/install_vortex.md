# 安装和设置 Vortex 环境 (Installing and Setting Up the Vortex Environment)

## Ubuntu 18.04, 20.04

1. 安装以下依赖项 (Install the following dependencies):

   ```
   sudo apt-get install build-essential zlib1g-dev libtinfo-dev libncurses5 uuid-dev libboost-serialization-dev libpng-dev libhwloc-dev
   ```

2. 升级 GCC 到 11 (Upgrade GCC to 11):

   ```
   sudo apt-get install gcc-11 g++-11
   ```

   Ubuntu 上的多个 gcc 版本可以使用 update-alternatives 进管理，例如 (Multiple gcc versions on Ubuntu can be managed with update-alternatives, e.g.):

   ```
   sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9
   sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 9
   sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 11
   sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 11
   ```

3. 下载 Vortex 代码库 (Download the Vortex codebase):

   ```
   git clone --depth=1 --recursive https://github.com/vortexgpgpu/vortex.git
   ```
4. 构建 Vortex (Build Vortex)

   ```
   $ cd vortex
   $ mkdir -p build
   $ cd build
   $ ../configure --xlen=32 --tooldir=$HOME/tools
   $ ./ci/toolchain_install.sh --all
   $ source ./ci/toolchain_env.sh
   $ make -s
   ```


## RHEL 8
注意：根据系统的不同，某些工具链可能需要针对非 Ubuntu Linux 重新编译。工具的源代码可以在[此处](https://github.com/vortexgpgpu/)找到。

1. 安装以下依赖项 (Install the following dependencies):

   ```
   sudo yum install libpng-devel boost boost-devel boost-serialization libuuid-devel opencl-headers hwloc hwloc-devel gmp-devel compat-hwloc1
   ```

2. 升级 GCC 到 11 (Upgrade GCC to 11):

   ```
   sudo yum install gcc-toolset-11
   ```

   Red Hat 上的多个 gcc 版本可以使用 scl 进行管理 (Multiple gcc versions on Red Hat can be managed with scl)

3. 安装 MPFR 4.2.0 (Install MPFR 4.2.0):

   下载[源代码](https://ftp.gnu.org/gnu/mpfr/)并按照[安装文档](https://www.mpfr.org/mpfr-current/mpfr.html#How-to-Install)进行操作。

4. 下载 Vortex 代码库 (Download the Vortex codebase):

   ```
   git clone --depth=1 --recursive https://github.com/vortexgpgpu/vortex.git
   ```

5. 构建 Vortex (Build Vortex)

   ```
   $ cd vortex
   $ mkdir -p build
   $ cd build
   $ ../configure --xlen=32 --tooldir=$HOME/tools
   $ ./ci/toolchain_install.sh --all
   $ source ./ci/toolchain_env.sh
   $ make -s
   ```