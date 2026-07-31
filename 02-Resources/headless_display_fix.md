# 无头主机（无显示器）远程画面静止修复指南

## 问题原因
Ubuntu 默认使用 Wayland 显示服务器。当检测不到物理显示器时，GPU 会进入休眠状态，停止桌面渲染，导致 NoMachine/XRDP 等远程工具显示静止画面。

## 解决方案
安装 `xserver-xorg-video-dummy` 虚拟显卡驱动 + 强制切换至 X11 协议。

## 配置内容

### 1. 虚拟显示配置 (`/etc/X11/xorg.conf.d/10-dummy.conf`)
```conf
Section "Device"
    Identifier "DummyDevice"
    Driver "dummy"
    VideoRam 256000
EndSection

Section "Monitor"
    Identifier "DummyMonitor"
    HorizSync 28.0-80.0
    VertRefresh 48.0-75.0
    Modeline "1920x1080_60.00" 173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync
EndSection

Section "Screen"
    Identifier "DummyScreen"
    Device "DummyDevice"
    Monitor "DummyMonitor"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1080"
    EndSubSection
EndSection
```

### 2. 强制 X11 (`/etc/gdm3/custom.conf`)
取消注释或添加：`WaylandEnable=false`

## 应用方法
```bash
sudo apt install -y xserver-xorg-video-dummy
# 写入上述配置后重启
sudo reboot
```
重启后系统会虚拟一个 1920x1080 显示器，远程桌面即可正常渲染动态画面。
