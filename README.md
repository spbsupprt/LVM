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



Заданиe:

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

grub-mkconfig -o /boot/grub/grub.cfg

![image](https://github.com/user-attachments/assets/7a96e832-529b-4153-94fb-7df13b83cc4d)



![image](https://github.com/user-attachments/assets/d5700bee-4e84-4cdf-a278-65d313f07458)

Правим /etc/fstab:

dm-name-vg_root-lv_root

![image](https://github.com/user-attachments/assets/b6e69e83-b616-451c-a062-6286a4c023bc)


Выполняем:

update-initramfs -u


После этого ребут, при новой загрузке Вывод lsblk + lvm можно удалить:


![image](https://github.com/user-attachments/assets/87fa4222-5dc4-4b82-b2be-b5cd18a185bf)


Создаем новый VG но уже на 8ГБ и делаем все в обратном порядке:

lvcreate -n VolGroup00/LogVol00 -L 8G /dev/VolGroup00

mkfs.xfs /dev/VolGroup00/LogVol00

mount /dev/VolGroup00/LogVol00 /mnt

xfsdump -J - /dev/vg_root/lv_root | xfsrestore -J - /mnt

for i in /proc/ /sys/ /dev/ /run/ /boot/; do mount --bind $i /mnt/$i; done

chroot /mnt/

grub-mkconfig -o /boot/grub/grub.cfg (сам поменял на VolGroup00-LogVol00)

Правим /etc/fstab ( VolGroup00-LogVol00 )

update-initramfs -u

Reboot

Задание выполнено:


![image](https://github.com/user-attachments/assets/71b1449c-2a1f-4055-a3ef-63c125091808)

Следующие заданиe:

3) выделить том под /var (/var - сделать в mirror)

pvcreate /dev/sde /dev/sdf

vgcreate vg_var /dev/sde /dev/sdf

lvcreate -L 950M -m1 -n lv_var vg_var

![image](https://github.com/user-attachments/assets/993cf248-deb5-4a48-9fa7-55983b800981)


mkfs.ext4 /dev/vg_var/lv_var

mount /dev/vg_var/lv_var /mnt

cp -aR /var/* /mnt/

mkdir /tmp/oldvar && mv /var/* /tmp/oldvar

umount /mnt

mount /dev/vg_var/lv_var /var

echo "/dev/disk/by-id/dm-name-vg_var-lv_var /var ext4 defaults 0 0" >> /etc/fstab

reboot

Результат:

![image](https://github.com/user-attachments/assets/7fb47695-26e1-495e-97ac-d3185cea98fb)


Следующие задания:

2) выделить том под /home
4) для /home - сделать том для снэпшотов
5) прописать монтирование в fstab (попробовать с разными опциями и разными файловыми системами на выбор)




lvcreate -n LogVol_Home -L 2G /dev/VolGroup00

![image](https://github.com/user-attachments/assets/7590d31d-b251-407a-a4d7-4c32b9152f04)


mkfs.xfs /dev/VolGroup00/LogVol_Home

mount /dev/VolGroup00/LogVol_Home /mnt/

cp -aR /home/* /mnt/

rm -rf /home/*

umount /mnt

mount /dev/VolGroup00/LogVol_Home /home/

echo "/dev/disk/by-id/dm-name-VolGroup00-LogVol_Home /home xfs defaults 0 0" >> /etc/fstab

![image](https://github.com/user-attachments/assets/9a0f42a8-5939-4f61-a520-ebbd6ee621d8)


Следующие задания:

6) Работа со снапшотами:
7) сгенерировать файлы в /home/
8) снять снэпшот
9) удалить часть файлов
10) восстановиться со снэпшота




