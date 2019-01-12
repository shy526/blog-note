# liunx

## chrome

1. `wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm`
    - 需要翻墙,下载chrome.rpm
2. `rpm -i google-chrome-stable_current_x86_64.rpm`
    - 安装chrome
    - 一般要安装依赖
        - `yum install pax*`
        - `yum install redhat-lsb*`
        - 如果还是有缺依赖
            - `yum install libXss*  -y`
            - `yum install libindicator*`
            - `yum install libappindicator-gtk3*`
3. `rpm -qa |grep chrome`
    - 检查一下是否已经安装

## xvfb

- 在内存中执行图形操作的工具

1. `yum install Xvfb`
    - 安装
2. `yum install libXfont`
    - 安装 如果你想有字的话
3. `yum install xorg-x11-fonts*`
    - 中文字体

## selenium-chrome-drive

- <https://sites.google.com/a/chromium.org/chromedriver/downloads>
  - 需要翻墙