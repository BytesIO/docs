---
id: running-a-node
title: Running a node
sidebar_label: Running a node
---

## 为什么需要节点?
NEAR协议在一组公共维护的计算机（或“节点”）上运行。
您可能出于以下几个原因决定运行自己的节点：

- 为了开发和部署合约到已经连接到MainNet，TestNet或BetaNet的节点（†）
- 在本地（独立和隔离）节点（有时称为“ LocalNet”）上开发和部署合约。（††）
- 以运行“验证者节点”的验证者身份加入网络（请参见[staking](/docs/validator/staking)）

_( † ) `TestNet`旨在在每周`BetaNet`释放周期之后尽可能稳定地（类似）和`MainNet`运行一致。_

_( †† )如果您希望避免在开发过程中泄漏有关您的工作的信息，`LocalNet`会是一个对的选择。因为`TestNet`和`BetaNet`是公共网络。`LocalNet`还可以让您完全控制帐户，经济性和其他因素，以使用更高级的用例（例如，对进行更改`nearcore`）。_

## `nearup` 安装
您可以按照以下Github指令安装`nearup`
https://github.com/near/nearup.


<blockquote class="info">
<strong>请小心</strong><br><br>

`nearup`（上面链接的）的描述README文件可能是你获得一个运行的节点所需要的全部信息。
可以使用此页面来澄清一些需要它的人的困惑。

</blockquote>
本文档其余部分中的步骤将需要`nearup`
## 使用Docker运行官方节点

### 安装Docker
默认情况下我们使用Docker来运行客户端软件。
请按照以下说明在您的计算机上安装Docker：

* [MacOS](https://docs.docker.com/docker-for-mac/install/)
* [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

注意：您可以通过将`--nodocker`标志添加到`nearup`命令并指定已编译的二进制路径，可以在不使用Docker的情况下运行节点。在下一部分中查看如何执行此操作：在没有Docker的情况下编译和运行官方节点。

### 运行 `nearup`
一旦`nearup`和Docker安装完成（按照上一节中的说明进行操作），请运行：

```sh
nearup betanet
```
_（如果您更倾向于使用`TestNet`则只需在上面的命令中替换`betanet`为`testnet` ）_
然后，系统将提示您输入一个帐户ID。如果您只想运行一个节点，可以将其保留为空。验证者应使用您要抵押的账户的账户ID。如果您想成为验证者，请参见[质押](/docs/validator/staking) 。


```text
请输入您的账户ID (如果您不打算成为验证者可以留空):
```
然后，一个节点将在Docker中从后台启动。
要检查Docker内部的日志，请运行`docker logs --follow nearcore`。

![text-alt](assets/docker-logs.png)


| Legend   |                                                            |
| :------- | :--------------------------------------------------------- |
| `# 7153` | BlockHeight                                                |
| `V/1`    | `V` (validator) or  `—`  (regular node) / Total Validators |
| `0/0/40` | connected peers / up to date peers / max peers             |



## 在没有Docker的情况下编译和运行官方节点
另外，您可以通过`nearcore` 本地编译并将`nearup`指向已编译的二进制文件来构建和运行不带Docker的节点。本节中的步骤提供了有关如何执行此操作的详细信息。

For Mac OS, the `nearup` README [provides a guide](https://github.com/near/nearup#run-nearup-on-macos).

对于Mac OS，`nearup`描述README文件[提供了指南](https://github.com/near/nearup#run-nearup-on-macos)。

确保已安装开发人员工具，然后用brew安装其他工具：

```text
brew install cmake protobuf clang llvm
```

对于Linux, 请安装以下依赖:

```text
apt update
apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm
```

然后克隆这个仓库:

```text
git clone https://github.com/near/nearcore.git
cd nearcore
```
切换到您想要构建的版本:

```bash
git checkout <version>
```

然后您可以运行:

```bash
make release
```
这将为您编译切换(checked out)版本所有二进制文件，包括诸如之类的工具keypair-generator。它们将在target/release/ 生效。

如果您在生产环境中运行验证者节点，则可能会发现构建`neard`更有效。在这种情况下，请在切换版本后运行以下命令：
```bash
cargo build -p neard --release
```
注意 请确保您包括`--release` 标志。忽略这一点将导致生成未优化的二进制文件，该二进制文件对于验证程序高效运行角度而言实在太慢了。

最后：
在MacOS或Linux上。

```bash
nearup betanet --nodocker --binary-path path/to/nearcore/target/release
```

如果要运行`TestNet`而不是运行`BetaNet`，请在上面的命令中将`betanet` 替换为`testnet`。

然后，系统将提示您输入一个帐户ID。如果您只想运行一个节点，可以将其保留为空。验证者应使用您要抵押的账户的账户ID。如果您想成为验证者，请参见[质押](/docs/validator/staking) 。

```text
E请输入您的账户ID (如果您不打算成为验证者可以留空):
```

## 在GCP上运行官方节点


创建新实例，至少具有：


* 2个vCPU和3.75 GB RAM（我们建议使用n1-standard-2）。
* 选择Ubuntu 18.04 LTS或更高版本。
* 分配100GB的持久性存储。


添加防火墙规则以允许从所有IP（0.0.0.0/0）到24567端口的流量。
通过SSH进入计算机（控制台中有"SSH"按钮或使用gcloud ssh命令）。

运行:

```text
sudo apt update
sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm
```

## 运行成功返回的消息
完成使用Docker运行节点或不使用Docker进行编译的步骤之后，您将看到类似于以下内容的消息：

```text
Using local binary at path/to/nearcore/target/release
Our genesis version is up to date
Starting NEAR client...
Node is running! 
To check logs call: `nearup logs` or `nearup logs --follow`
```

或者

```text
Using local binary at path/to/nearcore/target/release
Our genesis version is up to date
Stake for user 'stakingpool.youraccount.betanet' with 'ed25519:6ftve9gm5dKL7xnFNbKDNxZXkiYL2cheTQtcEmmLLaW'
Starting NEAR client...
Node is running! 
To check logs call: `nearup logs` or `nearup logs --follow`
```

>有任何的疑问?
<a href="https://stackoverflow.com/questions/tagged/nearprotocol">
  <h8>在StackOverflow上提问!</h8></a>
