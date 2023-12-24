# macOS Sonoma Setup

## System Settings

### Trackpad

- Point & Click
  - Tap to click: enable
  - Secondary click: Click or Tap with Two Fingers
- Scrool & Zoom
  - Natural scrolling: enable
  - Zoom in or out: enable
  - Rotate: enable

### Accessibility

Pointer Control -> Trackpad Options -> Dragging style: Three Finger Drag

### Desktop & Dock

- 把 dock size 拉小
- 关掉 Magnification
- dock 屏幕位置改到右边

## xcode

```bash
xcode-select --install
```

## [homebrew](https://brew.sh/)

安装 homebrew：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装完之后，根据控制台输出指引，把 homebrew 添加到到环境变量。

## [warp](https://www.warp.dev/)

```bash
brew install --cask warp
```

呼出快捷键：Settings -> Features -> Global hotkey -> `CMD + \`

修改 hostname：`sudo scutil --set HostName <new-name>`

### [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

配置 plugins：

```zsh
plugins=(git brew bundler macos asdf z)
```

### Fonts

1. [JetBrains Mono](https://www.jetbrains.com/lp/mono/)
   1. Download zip file
   2. Select all font files in the folder and double-click the “Install Font” button.
2. [GitHub monaspace](https://monaspace.githubnext.com/)
   1. `brew tap homebrew/cask-fonts`
   2. `brew install font-monaspace`
3. [Nerd Fonts](https://www.nerdfonts.com/)
   1. `brew tap homebrew/cask-fonts`
   2. `brew install font-hack-nerd-font`

## asdf

[homepage](https://asdf-vm.com/)

```bash
brew install coreutils curl git # install dependencies
brew install asdf
```

### plugins

```bash
asdf plugin add ruby https://github.com/asdf-vm/asdf-ruby.git
brew install gpg gawk # asdf-nodejs dependencies
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git
asdf install nodejs latest # or 20.10.0
```

### ruby

先安装构建依赖：[Suggested build environment](https://github.com/rbenv/ruby-build/wiki#suggested-build-environment)

```bash
# install Xcode Command Line Tools
xcode-select --install
# install dependencies with Homebrew
brew install openssl@3 readline libyaml gmp # ruby build dependencies
asdf install ruby latest # or 3.2.2
```

## 常用软件

1. 1Password 6: [download](https://app-updates.agilebits.com/)
2. Alfred 4: [download](https://www.alfredapp.com/help/v4/)
3. Yoink: download on App Store
4. Dash 4: [download](https://blog.kapeli.com/dash-4)

```bash
brew install --cask visual-studio-code
brew install rectangle
brew install google-chrome
brew install --cask dozer
```

## 参考

- [macOS Setup Guide](https://sourabhbajaj.com/mac-setup/)
- [macOS Dev Setup](https://github.com/nicolashery/mac-dev-setup)
