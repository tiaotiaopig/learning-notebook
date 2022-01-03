# zsh 设置

## 安装

```bash
# 命令安装，就是git clone 代码库到用户根目录
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
# 或者使用这条
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

还需要修改默认的登录shell

```bash
# 查看所有shell
cat /etc/shells
# 修改默认shell
chsh -r /usr/bin/zsh
```

## 常用插件

### auto-suggestion

1. Clone this repository into `$ZSH_CUSTOM/plugins` (by default `~/.oh-my-zsh/custom/plugins`)

   ```
   git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
   ```

2. Add the plugin to the list of plugins for Oh My Zsh to load (inside `~/.zshrc`):

   ```
   plugins=( 
       # other plugins...
       zsh-autosuggestions
   )
   ```

3. Start a new terminal session.

### zsh-syntax-highlighting

1. Clone this repository in oh-my-zsh's plugins directory:

   ```bash
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
   ```

2. Activate the plugin in `~/.zshrc`:

   ```bash
   # 必须放最后
   plugins=( [plugins...] zsh-syntax-highlighting)
   ```

3. Restart zsh (such as by opening a new instance of your terminal emulator).