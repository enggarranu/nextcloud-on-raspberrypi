# Cara aman memindahkan storage ke mounting baru

Siapkan disk/mount point baru
Misalnya disk kamu di-mount ke /mnt/nextcloud-data.
Pastikan filesystem sudah support Linux permission (ext4, xfs, btrfs; jangan pakai NTFS/FAT).

**Matikan Nextcloud snap**
```
sudo snap stop nextcloud
```

__Pindahkan data lama ke lokasi baru__

```
sudo rsync -avz /var/snap/nextcloud/common/nextcloud/data/ /data/SSD/nextcloud-data/
```

(pakai rsync biar permission & hidden file ikut terbawa).

__Ubah konfigurasi data dir
File config ada di:__
```
/var/snap/nextcloud/current/nextcloud/config/config.php
```

Cari baris:

```'datadirectory' => '/var/snap/nextcloud/common/nextcloud/data',```


Ubah jadi:

```'datadirectory' => '/mnt/nextcloud-data',```


__Set permission untuk snap
Karena snap jalan pakai user root + confinement, pastikan permission folder sesuai:
__
```
sudo chown -R root:root /data/SSD/nextcloud-data/
sudo chmod -R 750 /data/SSD/nextcloud-data/
```

(kalau kamu punya user tertentu untuk data, bisa disesuaikan; tapi default snap pakai root).

__Jalankan lagi Nextcloud__
```
sudo snap start nextcloud
```

Cek status
Via web UI Nextcloud (login user, cek file masih ada).
Atau cek error log:
```
sudo journalctl -u snap.nextcloud.apache
```
🔹 Tips tambahan

Kalau mount disk eksternal, pastikan /etc/fstab sudah di-set supaya otomatis mount di boot.

Kalau mount berubah-ubah (misalnya pakai USB), bisa bikin symbolic link:
```
sudo ln -s /mnt/nextcloud-data /var/snap/nextcloud/common/nextcloud-data
```

Hati-hati: kalau salah permission, Nextcloud akan error “cannot create data directory”.
