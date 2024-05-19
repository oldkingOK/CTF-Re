[Boot ARM64 virtual machines on QEMU | Ubuntu](https://ubuntu.com/server/docs/boot-arm64-virtual-machines-on-qemu)

[使用QEMU配置龙芯新世界环境 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/626169693)

建议不要使用wsl，会有==奇怪的、无法解决==的网络问题，用Windows的就行（msys2 UCRT64）

```shell
sudo apt install qemu-system-loongarch64
# 网络不好记得换节点
```

在北大源下载镜像

[文件-北京大学开源镜像站 (pku.edu.cn)](https://mirrors.pku.edu.cn/loongarch/archlinux/images/)

- images/QEMU_EFI_8.1.fd
- iso/latest/archlinux-loong64.iso

```shell
mkdir -p ~/VMs/loongarch/
qemu-img create -f qcow2 ~/VMs/loongarch/archlinux-loongarch-test.qcow2 20G
```

```bash
qemu-system-loongarch64 \
    -m 5G \
    -cpu la464-loongarch-cpu \
    -machine virt \
    -smp 4 \
    -bios ~/VMs/loongarch/QEMU_EFI_8.1.fd \
    -serial stdio \
    -device virtio-gpu-pci \
    -net user -net nic \
    -device nec-usb-xhci,id=xhci,addr=0x1b \
    -device usb-tablet,id=tablet,bus=xhci.0,port=1 \
    -device usb-kbd,id=keyboard,bus=xhci.0,port=2 \
    -hda ~/VMs/loongarch/archlinux-loongarch-test.qcow2 \
    -cdrom ~/archlinux-2024.03.30-loong64.iso \
    -boot once=d
```

等待

1. 磁盘需要配置，建议使用ext4
2. 网络需要【手动配置】，一直默认即可

```shell
pacman -S neofetch vim sudo networkmanager zsh openssh
```

![[image-20240423132619385.png]]

## 配置 ssh

```bash
systemctl status sshd # 如果 inactive
systemctl start sshd
systemctl enable sshd # 设置自动启动
ip a # 获取ip
```

