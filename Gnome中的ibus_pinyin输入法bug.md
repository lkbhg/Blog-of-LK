# Gnome中的ibus_pinyin输入法bug.

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

## 💡 背景与问题描述

使用Ubuntu自带的输入法管理系统无法添加`ibus_pinyin`，具体表现为无法直接在`设置`中搜索到更多的输入法。
但是使用`ibus-setup`则可以索引，但无法被Gnome唤醒。
`Gnome verion 49.2`

## 尝试测试

### 使用fcitx
直接安装`fcitx`依然无法被唤醒
### 重建缓存
使用过以下方法
```
sudo apt install ibus ibus-pinyin ibus-libpinyin
im-config -n ibus
dconf reset -f /desktop/ibus/
ibus restart
reboot
```
但这些方法基本是无效的。

检视：
```
ps aux | grep ibus-daemon
ibus-daemon -drx
echo $XMODIFIERS
echo $GTK_IM_MODULE
echo $QT_IM_MODULE
gsettings get org.gnome.desktop.input-sources sources
```
以上检视暂无发现异常。

## 最终只能使用：
```
gsettings set org.gnome.desktop.input-sources sources "[('ibus', 'pinyin')]"
```
先手动强行设置为`pinyin`,再添加原有的`English`输入法。


推测：大概率是由于`Gnome`切换为`Wayland`之后，出现了不兼容的Bug。暂时不理解根本原因。
---
