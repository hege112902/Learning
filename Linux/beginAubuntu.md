- [如何从零组装一台电脑](#如何从零组装一台电脑)
  - [组成](#组成)
  - [装机](#装机)
  - [装系统](#装系统)
- [拿到一台，如何评估和装环境](#拿到一台如何评估和装环境)
  - [Add or remove user](#add-or-remove-user)
  - [保存ssh密码](#保存ssh密码)
  - [装通用环境](#装通用环境)
  - [评估](#评估)
  - [装 pytorch 环境](#装-pytorch-环境)

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


# 拿到一台，如何评估和装环境

## Add or remove user
添加一个名为 newuser 的用户账号：
```bash
sudo adduser newuser  # 添加一个名为 newuser 的用户账号
sudo usermod -aG sudo newuser  # 添加到 sudo 组
```
- 验证新用户是否具有sudo权限:
```bash
su - newuser  # 切换到新用户
sudo apt update  # 尝试运行一个需要sudo权限的命令，例如更新软件包列表
```
- 查看系统所有的账户
```sh
cat /etc/passwd  # 所有账户

# 通常，UID（用户ID）小于1000的是系统用户，而大于或等于1000的是普通用户。
awk -F: '$3 >= 1000 {print $1}' /etc/passw  # 所有普通账户。
```
- 删除一个名为 newuser 的用户账号：
```sh
sudo userdel username  # 删除用户账户但保留其主目录和文件

sudo userdel -r username  # 删除用户账户以及其主目录和文件，可以使用 -r 选项

# 在某些情况下，如果用户正在使用，userdel 命令可能无法正常工作。此时可以使用 -f 选项强制删除用户账户
sudo userdel -f username  # 强制删除用户账户
sudo userdel -rf username  # 强制删除用户的主目录和文件
```
- 其他相关命令:
```bash
groups newuser  # 查看当前用户所属组
sudo apt update  # 尝试运行一个需要sudo权限的命令，例如更新软件包列表
```
- 如果希望在用户执行sudo命令时不需要输入密码，可以编辑 /etc/sudoers 文件，在文件末尾添加以下行：
```plaintext
newuser ALL=(ALL) NOPASSWD:ALL
```
请注意，不建议在生产环境中这样配置，因为这会降低系统的安全性。


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



