---
id: running-a-node-windows
title: Running a Node on Windows
sidebar_label: Running a node (Windows)
---

1.  如果未启用Linux的Windows子系统，请以管理员身份打开PowerShell并运行：
    ```sh
    Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
    ```
    然后重新启动计算机。
2. 转到您的Microsoft商店并查找Ubuntu；这是Ubuntu终端实例。安装并启动它。
3. 现在可能会要求您输入用户名和密码，不要使用admin作为用户名。
4. 您的Ubuntu实例需要先进行初始化以进入后续步骤
    ```sh
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get install build-essential pkg-config libssl-dev
    ```
5. 您需要安装OpenSSL，以用来运行该节点。要下载OpenSSL，请在Ubuntu终端中运行以下命令：
    ```sh
    cd /tmp
    wget https://www.openssl.org/source/openssl-1.1.1.tar.gz
    tar xvf openssl-1.1.1.tar.gz
    ```
6. 完成下载OpenSSL后，运行以下命令进行安装：
    ```sh
    cd openssl-1.1.1
    sudo ./config -Wl,--enable-new-dtags,-rpath,'$(LIBRPATH)'
    sudo make
    sudo make install
    ```
    这些文件将位于以下目录中：/usr/local/ssl。
7. 完成此操作后，您必须确保Ubuntu将使用正确版本的OpenSSL。现在，更新手册页和二进制文件的路径。运行以下命令：
    ```sh
    cd ../..
    sudo nano /etc/manpath.config
    ```
8. 将打开一个文本文件，添加以下行：
    ```sh
    MANPATH_MAP     /usr/local/ssl/bin      /usr/local/ssl/man
    ```
    完成此操作后，按ctrl + o。它将要求您保存文件，只需按Enter。现在按ctrl + x退出。
9. 要确保已安装OpenSSL，请运行：
    ```sh
    openssl version -v
    ```
    这应该显示您已安装的版本。有关更多信息，请参见此处。（https://manpages.ubuntu.com/manpages/bionic/man1/version.1ssl.html）
10. 现在，您必须运行以下命令来安装所有必要的软件和依赖项：
    ```sh
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get install -y git jq binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ protobuf-compiler python3 python3-pip llvm clang
    ```
    安装Rustup
    ```sh
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    source $HOME/.cargo/env
    rustup default nightly
    ```
    很好! 所有可以使节点启动并运行的准备已经完成！
11. 克隆nearcore的github代码仓库
    
    首先，我们需要检查当前在testnet中工作的版本：
    ```sh
    curl -s https://rpc.testnet.near.org/status | jq .version
    ```
    您将获得类似以下内容的代码："1.13.0-rc.2"。"1.13.0"是一个分支，我们需要对其进行克隆以构建用于testnet的节点。

    ```sh
    git clone --branch 1.13.0 https://github.com/near/nearcore.git
    ```
12. 这创建了一个Nearcore目录，切换到该目录并建立一个Noce：
    ```sh
    cd nearcore
    make release
    ```
13. 安装nearup
    ```sh
    pip3 install --user nearup
    export PATH="$HOME/.local/bin:$PATH"
    ```
14. 最后: 现在运行testnet:
    ```sh
    nearup run testnet --binary-path ~/nearcore/target/release/
    ```
    您可以通过检查日志来确保节点稳定运行:
    ```sh
    nearup logs --follow
    ```

可能会要求您提供验证者ID；如果您不想验证，只需按Enter。有关验证，请参阅[验证部分](validator/staking.md).。

>有任何疑问?
<a href="https://stackoverflow.com/questions/tagged/nearprotocol">
  <h8>在StackOverflow上提问!</h8></a>
