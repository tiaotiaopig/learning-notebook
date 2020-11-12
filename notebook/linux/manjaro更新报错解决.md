# error: file ‘/boot/vmlinux-4.19-x86_64’ not found （manjaro更新重启后，报错解决）
----
1. 需要（重新）安装至少一个或两个内核。因此，从实时iso引导并运行：
    sudo manjaro-chroot -a
2. 使用mhwd-kernel查找可用的内核并安装其中的一些：
	mhwd-kernel -l
	mhwd-kernel -i linux414
	mhwd-kernel -i linux419
	mhwd-kernel -i linux54
3. 如果mhwd-kernel -i linux ...不起作用，则可以使用pacman进行安装，因此与之前相同的过程，然后代替mhwd-kernel ... ::
	pacman -Syu linux414 linux414-headers
	pacman -S linux419 linux419-headers
	pacman -S linux54 linux54-headers
4. 重建内核映像：
	mkinitcpio -P
5. 最后：
	update-grub
	sync
	exit
6. 然后正常重启进入系统。