# Ubuntu Development Setup Guide

## Table of Contents
- [System Update](#system-update)
- [Essential Packages](#essential-packages)
- [Shell Configuration](#shell-configuration)
- [Git Configuration](#git-configuration)
- [SSH Setup](#ssh-setup)
- [Development Tools](#development-tools)
- [Python Environment](#python-environment)
- [Node.js Environment](#nodejs-environment)
- [Docker Setup](#docker-setup)
- [VS Code Extensions](#vs-code-extensions)

## System Update

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git build-essential software-properties-common apt-transport-https ca-certificates gnupg lsb-release
```

## Essential Packages

```bash
sudo apt install -y \
    vim nano \
    htop btop \
    tree \
    unzip zip \
    jq \
    neofetch \
    zsh \
    tmux \
    fzf \
    ripgrep \
    bat \
    exa
```

## Shell Configuration

### Bash Aliases and Functions

Edit your bash configuration:
```bash
nano ~/.bashrc
```

Add these aliases and functions:
```bash
# System aliases
alias cls='clear'
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias ..='cd ..'
alias ...='cd ../..'
alias grep='grep --color=auto'
alias fgrep='fgrep --color=auto'
alias egrep='egrep --color=auto'

# Navigation aliases
alias down='cd ~/Downloads'
alias docs='cd ~/Documents'
alias proj='cd ~/Projects'

# System maintenance
alias update='sudo apt update && sudo apt upgrade -y'
alias cleanup='sudo apt autoremove -y && sudo apt clean'
alias fixbroken='sudo apt --fix-broken install'
alias diskusage='du -h --max-depth=1 | sort -hr'
alias meminfo='free -h'
alias cpuinfo='lscpu'

# Development aliases
alias pipall='pip install -r requirements.txt'
alias venv='source .venv/bin/activate'
alias serve='python -m http.server 8000'
alias myip='curl -s https://ipinfo.io/ip'

# Git shortcuts
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gl='git log --oneline'
alias gd='git diff'

# Enhanced tools (if installed)
alias cat='batcat'
alias ls='exa --icons'
alias find='fd'

# Functions
function mkcd() {
    mkdir -p "$1" && cd "$1"
}

function extract() {
    if [ -f "$1" ]; then
        case "$1" in
            *.tar.bz2)   tar xjf "$1"     ;;
            *.tar.gz)    tar xzf "$1"     ;;
            *.bz2)       bunzip2 "$1"     ;;
            *.rar)       unrar x "$1"     ;;
            *.gz)        gunzip "$1"      ;;
            *.tar)       tar xf "$1"      ;;
            *.tbz2)      tar xjf "$1"     ;;
            *.tgz)       tar xzf "$1"     ;;
            *.zip)       unzip "$1"       ;;
            *.Z)         uncompress "$1"  ;;
            *.7z)        7z x "$1"        ;;
            *)           echo "'$1' cannot be extracted via extract()" ;;
        esac
    else
        echo "'$1' is not a valid file"
    fi
}

function gitupdate() {
    if [ -z "$1" ] || [ -z "$2" ]; then
        echo "Usage: gitupdate \"commit message\" branch-name"
        return 1
    fi
    git add .
    git commit -m "$1"
    git push -u origin "$2"
}

function gitquick() {
    if [ -z "$1" ]; then
        echo "Usage: gitquick \"commit message\""
        return 1
    fi
    git add .
    git commit -m "$1"
    git push
}

# History settings
export HISTSIZE=10000
export HISTFILESIZE=20000
export HISTCONTROL=ignoredups:erasedups
shopt -s histappend

# Better prompt
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

Apply changes:
```bash
source ~/.bashrc
```

### Zsh Setup (Optional but Recommended)

Install Oh My Zsh:
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Install useful plugins:
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

## Git Configuration

### Basic Configuration
```bash
git config --global user.name "HimLamTech"
git config --global user.email "hailam04.work@gmail.com"
git config --global core.editor "code --wait"
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global color.ui auto
git config --global credential.helper store
```

### Git Aliases
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
git config --global alias.graph 'log --oneline --graph --decorate --all'
git config --global alias.amend 'commit --amend --no-edit'
```

## SSH Setup

Generate SSH key:
```bash
ssh-keygen -t ed25519 -C "hailam04.work@gmail.com"
```

Start SSH agent and add key:
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

Copy public key to clipboard:
```bash
cat ~/.ssh/id_ed25519.pub
```

## Development Tools

### VS Code Installation
```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

### Vim Configuration
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

## Python Environment

### Install Python and pip
```bash
sudo apt install -y python3 python3-pip python3-venv python3-dev
```

### Install pipx for global Python tools
```bash
python3 -m pip install --user pipx
python3 -m pipx ensurepath
```

### Useful Python tools
```bash
pipx install black
pipx install flake8
pipx install mypy
pipx install poetry
pipx install httpie
pipx install youtube-dl
```

## Node.js Environment

### Install Node Version Manager (nvm)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
```

### Install latest Node.js
```bash
nvm install node
nvm use node
nvm alias default node
```

### Global npm packages
```bash
npm install -g \
    yarn \
    pnpm \
    typescript \
    ts-node \
    nodemon \
    live-server \
    http-server \
    create-react-app \
    @vue/cli \
    @angular/cli
```

## Docker Setup

### Install Docker
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Add user to docker group
```bash
sudo usermod -aG docker $USER
newgrp docker
```

### Install Docker Compose
```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## VS Code Extensions

Install essential extensions:
```bash
code --install-extension ms-python.python
code --install-extension ms-vscode.vscode-typescript-next
code --install-extension bradlc.vscode-tailwindcss
code --install-extension esbenp.prettier-vscode
code --install-extension ms-vscode.vscode-eslint
code --install-extension ms-vscode-remote.remote-ssh
code --install-extension ms-vscode.live-server
code --install-extension ritwickdey.liveserver
code --install-extension formulahendry.auto-rename-tag
code --install-extension christian-kohler.path-intellisense
code --install-extension ms-vscode.vscode-json
code --install-extension redhat.vscode-yaml
code --install-extension ms-vscode.theme-monokai-dimmed
```

## Additional Configurations

### Tmux Configuration
Create `~/.tmux.conf`:
```bash
cat > ~/.tmux.conf << 'EOF'
# Remap prefix key
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# Enable mouse mode
set -g mouse on

# Start windows and panes at 1, not 0
set -g base-index 1
setw -g pane-base-index 1

# Reload config file
bind r source-file ~/.tmux.conf \; display-message "Config reloaded!"
EOF
```

### Git Global .gitignore
```bash
cat > ~/.gitignore_global << 'EOF'
# OS generated files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# IDE files
.vscode/
.idea/
*.swp
*.swo
*~

# Logs
*.log
logs/

# Runtime data
pids/
*.pid
*.seed

# Dependency directories
node_modules/
.venv/
venv/
__pycache__/
*.pyc

# Build outputs
dist/
build/
*.egg-info/
EOF

git config --global core.excludesfile ~/.gitignore_global
```

### System Monitoring Aliases
Add to `~/.bashrc`:
```bash
# System monitoring
alias ports='netstat -tulanp'
alias listening='lsof -i -P -n | grep LISTEN'
alias processes='ps aux | grep -v grep | grep'
alias diskspace='df -h'
alias foldersize='du -sh'
alias temp='sensors'
```

## Final Steps

1. Reboot your system to ensure all changes take effect
2. Test your setup by running some commands
3. Customize further based on your specific development needs

```bash
sudo reboot
```