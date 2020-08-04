# Terminal

## Setup Prerequisites
Install Homebrew
##
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
brew update
brew upgrade
brew cleanup
```

## Setup Steps
### Install iTerm2
```bash
brew cask install iterm2
```

### Install oh-my-zsh
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### Install PowerLevel10K
```bash
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```
Then edit your theme
```bash
vim ~/.zshrc
ZSH_THEME="powerlevel10k/powerlevel10k"
```

close terminal and open iTerm2, will auto request to configure, else execute below command
```bash
p10k configure
```

### Configure iTerm2 with Material Design Color
1. donwload material design color theme
```bash
cd downloas
curl -O https://raw.githubusercontent.com/MartinSeeler/iterm2-material-design/master/material-design-colors.itermcolors
```

2. Open iTerm2 that we already downloaded at the first section
3. Go to iTerm2 > Preferences > Profiles > Colors Tab
4. Click Color Presets… at the bottom right
5. Click Import…
6. Select the material-design-colors.itermcolors file
7. Select the material-design-colors from Load Presets…

### Install Plugin
zsh-autosuggestions
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

zsh-history-substring-search
```bash
brew install zsh-history-substring-search
```

zsh-syntax-highlighting
```bash
brew install zsh-syntax-highlighting
```
Edit and add the code at the bottom of .zshrc file
```bash
vim ~/.zshrc
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

Add plugins into ~/.zshrc
```bash
vim ~/.zshrc
plugins=(
  brew
  git
  gradle
  ng
  npm
  yarn
  zsh-autosuggestions
  osx
  history-substring-search
)
```


## Reference
[iTerm2 + Oh My Zsh! + Powerlevel9K best terminal combination for Geeks!](https://dev.to/equiman/iterm2--oh-my-zsh--powerlevel9k-best-terminal-combination-for-geeks-58l5)

