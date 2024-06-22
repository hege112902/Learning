- [如何从零组装一台电脑](#如何从零组装一台电脑)
  - [组成](#组成)
  - [装机](#装机)
  - [装系统](#装系统)
- [拿到一台，如何评估和装环境](#拿到一台如何评估和装环境)
  - [Add or remove user](#add-or-remove-user)
  - [关于用户 sudo 权限](#关于用户-sudo-权限)
    - [赋予用户 sudo 权限](#赋予用户-sudo-权限)
        - [1. 直接修改 'sudoers' 文件](#1-直接修改-sudoers-文件)
        - [2. 添加到 sudo 组](#2-添加到-sudo-组)
        - [两种方法的比较](#两种方法的比较)
    - [移除用户 sudo 权限](#移除用户-sudo-权限)
  - [保存ssh密码](#保存ssh密码)
  - [装通用环境](#装通用环境)
  - [评估](#评估)
  - [装 pytorch 环境](#装-pytorch-环境)
  - [Reference](#reference)

# 如何从零组装一台电脑
## 组成
- 主板
- cpu
- 内存
- 硬盘
- 显卡
- 电源 + 机箱

系统装在硬盘里hhh

## 装机

## 装系统
一个空的U盘
烧写软件：[Etcher](https://etcher.balena.io/) 
下载 ubuntu： https://releases.ubuntu.com/jammy/

1. 下载Ubuntu ISO文件
2. 下载并安装Etcher
3. 制作启动U盘：
   1. 插入U盘（确保U盘中的数据已备份，因为这个过程会清空U盘）。
   2. 打开Etcher。
   3. 点击“Flash from file”，选择你下载的Ubuntu ISO文件。
   4. 点击“Select target”，选择你的U盘。
   5. 点击“Flash”开始制作启动U盘。
   6. 等待制作完成，Etcher会显示“Flash Complete”消息。
4. 在新电脑上启动并安装Ubuntu
    1. 插入启动U盘：
      将制作好的Ubuntu启动U盘插入目标电脑的USB端口。
    2. 启动电脑并进入BIOS/UEFI设置：
      重新启动电脑并按下相应的键（通常是F2, F12, Del, Esc等），进入BIOS/UEFI设置。
    3. 设置从U盘启动：
      在BIOS/UEFI设置中，找到启动顺序设置，将U盘设置为第一启动项。
      保存设置并退出BIOS/UEFI。
    4. 启动Ubuntu安装程序：
      电脑将从U盘启动并显示Ubuntu安装程序界面。
      选择“Install Ubuntu”并按照屏幕上的指示完成安装。

# 拿到一台，如何评估和装环境

## Add or remove user
- 添加一个名为 newuser 的用户账号：
```bash
sudo adduser newuser  # 添加一个名为 newuser 的用户账号
sudo usermod -aG sudo newuser  # 添加到 sudo 组
```
- 验证新用户是否具有sudo权限:
```bash
su - newuser  # 切换到新用户
sudo apt update  # 尝试运行一个需要sudo权限的命令，例如更新软件包列表

groups xyz  # 查看特定用户所属的组，若包含 sudo，则已经加入 sudo 组。
# 输出类似于：
# xyz : xyz sudo
```
- 查看特定组中的用户
```sh
getent group group_name
# for example
getent group sudo  # 查看 sudo 组有哪些用户
```
- 查看 'sudoers' 文件
```sh
sudo visudo
```
```plaintext
xyz     ALL=(ALL:ALL) ALL
```
- 查看系统所有的账户
```sh
cat /etc/passwd  # 所有账户

# 通常，UID（用户ID）小于1000的是系统用户，而大于或等于1000的是普通用户。
awk -F: '$3 >= 1000 {print $1}' /etc/passwd  # 所有普通账户。
```
- 删除一个名为 newuser 的用户账号：
```sh
sudo userdel username  # 删除用户账户但保留其主目录和文件

sudo userdel -r username  # 删除用户账户以及其主目录和文件，可以使用 -r 选项

# 在某些情况下，如果用户正在使用，userdel 命令可能无法正常工作。此时可以使用 -f 选项强制删除用户账户
sudo userdel -f username  # 强制删除用户账户
sudo userdel -rf username  # 强制删除用户的主目录和文件
```
- 切换用户
```sh
su - newuser
```
- 验证当前用户是否是 xyz
```sh
whoami  # xyz
pwd  # /home/xyz
```

- 修改账户密码
如果需要修改账户A的密码，换到另一个拥有 'sudo' 权限的账户B下操作。
```sh
sudo passwd descfly  
```
然后输入当前账户的密码。
系统会提示输入新的 descfly 用户的密码：
```plaintext
Enter new UNIX password:  [输入新密码]
Retype new UNIX password:  [再次输入新密码]
passwd: password updated successfully
```

- 其他相关命令:
```bash
groups newuser  # 查看当前用户所属组
sudo apt update  # 尝试运行一个需要sudo权限的命令，例如更新软件包列表
# 输出类似于：
# sudo:x:27:user1,user2
```
- 如果希望在用户执行sudo命令时不需要输入密码，可以编辑 /etc/sudoers 文件，在文件末尾添加以下行：
```plaintext
newuser ALL=(ALL) NOPASSWD:ALL
```
请注意，不建议在生产环境中这样配置，因为这会降低系统的安全性。

## 关于用户 sudo 权限
### 赋予用户 sudo 权限
有两种独立的操作。假设用户名为 xyz。
##### 1. 直接修改 'sudoers' 文件
```sh
sudo visudo  # 使用 visudo 命令安全地编辑 sudoers 文件
# 在文件中添加以下行：
xyz    ALL=(ALL:ALL) ALL
```
保存并退出编辑器（通常使用 Ctrl+X 然后按 Y 确认保存并退出）。
##### 2. 添加到 sudo 组
通常，用户被添加到 sudo 组来赋予 sudo 权限，这样管理更加方便和一致。
```sh
sudo usermod -aG sudo xyz  # 使用 usermod 命令将用户 xyz 添加到 sudo 组

groups xyz  # 确认用户 xyz 已经在 sudo 组中
# 输出应包含 sudo，表示用户 xyz 已经在 sudo 组中
```
##### 两种方法的比较
- **直接修改 sudoers 文件：**
优点：可以为用户提供特定的 sudo 权限，灵活性更高。
缺点：管理上可能比较分散，不适合大量用户。
- **通过 sudo 组赋予权限：**
优点：管理更简单和一致，适合大多数情况。
缺点：所有 sudo 组用户都有相同的 sudo 权限，灵活性较低。
- **总结**
直接在 sudoers 文件中添加用户条目会赋予用户 sudo 权限，但不会将其添加到 sudo 组。
通过将用户添加到 sudo 组，可以方便地赋予和管理 sudo 权限。

### 移除用户 sudo 权限
要将用户 A 移除出 sudo 组，可以使用 deluser 或 gpasswd 命令。以下是具体步骤：
```sh
sudo deluser A sudo
# 或者
sudo gpasswd -d A sudo

```
- **注意**
如果用户 A 仍然在 /etc/sudoers 文件中有条目（例如 A ALL=(ALL:ALL) ALL），即使将其移除出 sudo 组，该用户仍然会有 sudo 权限。你需要在 /etc/sudoers 文件中删除或注释掉该条目。

## 保存ssh密码

```sh
ssh-copy-id fz40902 # server ip and username
```


## 装通用环境

```sh
sudo apt install htop git zsh openssh-server vim tmux
```
装 oh-my-zsh and plugins：
```sh
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
python3 -c "import os;P=os.getenv('HOME')+'/.zshrc';f=open(P);L=list(f);f.close();O=open(P,'w');[O.write('plugins=(git zsh-autosuggestions zsh-syntax-highlighting)\n') if l.startswith('plugins=') else O.write(l) for l in L]"
```

## 评估

- cpu核数、内存情况、最近使用情况：
```sh
htop
```
- gpu 情况：
```sh
nvidia-smi  # CUDA version

sudo apt install python3-pip
pip install gpustat
gpustat -cupP -i  # 事实使用状态和基本信息

nvidia-smi topo -m  # gpu的拓扑信息：
```
- 硬盘信息：
```sh
df -h  # 本机（所有）硬件的基本信息

sudo apt install smartmontools 
sudo smartctl -a /dev/sda  # 某个硬盘的详细信息

lsblk  # 所有硬盘的挂载信息
```
- 全部硬件详细信息：
```sh
sudo apt-get install neofetch
sudo neofetch
```

- 最近的登录log信息
```sh
last
```

## 装 pytorch 环境

- 装 anaconda
```sh
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh  # file name
```

- 装 pytorch
去 [pytorch 官网](https://pytorch.org/get-started/locally/) 获取自己所需要的版本的下载链接，例如：
```sh
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 -c pytorch -c nvidia
```
CUDA 版本可向下兼容。

## Reference
- [LCoy/oh-my-ubuntu(J小)](https://github.com/LCoy/oh-my-ubuntu)

