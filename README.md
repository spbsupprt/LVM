# LVM
Файловые системы и LVM - 1  - 2

1) уменьшить том под / до 8G
2) выделить том под /home
3) выделить том под /var (/var - сделать в mirror)
4) для /home - сделать том для снэпшотов
5) прописать монтирование в fstab (попробовать с разными опциями и разными файловыми системами на выбор)
6) Работа со снапшотами:
7) сгенерировать файлы в /home/
8) снять снэпшот
9) удалить часть файлов
10) восстановиться со снэпшота

Дано:

![image](https://github.com/user-attachments/assets/a3bf5d43-bd78-4a56-8efc-3eae6e019d2f)



Пункт.1
1) уменьшить том под / до 8G
   Команды:
   
pvcreate /dev/sdc
vgcreate vg_root /dev/sdc
lvcreate -n lv_root -l +100%FREE /dev/vg_root
mkfs.xfs /dev/vg_root/lv_root
mount /dev/vg_root/lv_root /mnt
xfsdump -J - /dev/VolGroup00/LogVol00 | xfsrestore -J - /mnt
for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done
chroot /mnt/

