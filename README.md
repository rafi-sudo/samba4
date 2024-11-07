# Instalasi
1. Pastikan sudah melakukan `apt update & upgrade` dengan repository yang benar , repository untuk pengguna ubuntu 18 ada [disini](https://github.com/rafi-sudo/samba4/blob/94820741274920a2ed6c1c9a5f0ca4ff5bb8ef8a/sources.list) , untuk repo lokal ada [disini](https://www.linuxsec.org/2018/08/repository-ubuntu-bionic-beaver.html).
2. Lalu install
<pre>
<code id="code-block">
apt install samba -y
</code>
</pre>
buat dengan -y supaya terunduh dengan daftar depedencys yang diperlukan samba seperti `samba-common, libacl1, libattr, libcups, libssl-dev, samba-libs, samba-utils, libjson, perl`

3. Buat folder yang ingin di jadikan tempat naruh file share samba, jikalau pengen di hdd eksternal/ storage selain sistem root sendiri, harus bikin mount poin terlebih dahulu di `sudo cat /etc/fstab` atau seperti contoh konfigurasi fstab seperit di server saya [ini](https://github.com/rafi-sudo/samba4/blob/639aab897587c89c3830a19bb23f3e957f690888/fstab) , dengan pertama bikin folder di direktori `/mnt` `sudo mkdir -p /mnt/samples` lalu konfigurasi di fstab, sesuai dengan nama UUID dan nama partisi (jikalau sudah dipartisi) storage eskternal kalian, lalu buatlah folder untuk samba di direktori tadi seprti `sudo mkdir -p /mnt/samples/samba`
4. lalu berikan hak ases ke user nobody / nogroup supaya bisa diakses semua PC/hape , serta izin penulisan seperti ini : 
![](https://github.com/rafi-sudo/samba4/blob/main/Screenshot_2024-11-07_06-58-49.png)

5. jikalau pengen pakai pengguna bukan anonimus , anda harus set up dulu user di ubuntu 18 kalian dengan `sudo adduser Andi` lalu `sudo passwd 123andi` lalu `sudo smbpasswd -a Andi & sudo smbpasswd -e Andi`
6. Serta konfigurasi ulang hak ases ke user root / Andi , serta izin penulisan read & write semua , jikalau pakai CLI
<pre>
<code id="code-block">
chmod -R 0777 /mnt/samples/samba dan chown -R root:Andi /mnt/samples/samba
</code>
</pre>

# Konfigurasi
1. Akses file smb.conf biasanya ada di `sudo nano /etc/samba/smb.conf` edit pakai nano CLI editor seperti [ini](https://github.com/rafi-sudo/samba4/blob/1e015cfcaa47b70b2d2924c09a755093d310fb1f/smb.conf) file konfigurasinya.
2. Masukkan script
<pre>
<code id="code-block">
[Samba]
   path = /mnt/samples/Samba
   valid users = nobody
   guest ok = yes
   browsable = yes
   writable = yes
   read only = no
</code>
</pre>

di barisan share definition biasanya ada konfigurasi [printers] dibawahnya

3. Untuk anonimus lakukan konfig guest OK = yes , valid users = nobody, untuk user yang telah kita buat sebelumnya :
<pre>
<code id="code-block">
[Samba]
   path = /mnt/samples/samba
   valid users = root:Andi
   guest ok = no
   browsable = yes
   writable = yes
   read only = no
</code>
</pre>

4. Setelah itu aktifkan samba saat booting otomatis `systemctl enable smbd` kalau belum bisa di enable pastikan depedencys terinstal lengkap dengan `-y` tadi atau install manual atau tidak kita buat folder konfigurasi `sudo nano /etc/systemd/system/smbd.services` nantinya untuk tempat file systemd samba untuk berjalan otomatis tanpa `systemctl start smbd` , setelah itu restart samba `systemctl restart smbd` atau restart Ubuntunya.

# Noted
Jikalau pengen melihat direktori partisian dan UUID terdeteksi di mana cukup ketik `df -h` di CLI Ubuntu, dengan nama partisian yang anda sudah buat, kalau belum buatlah partisiannya, liat storage eksternal kalian terbaca tidak di `lsblk` nanti keluar 
![](https://github.com/rafi-sudo/samba4/blob/65a43d17cd23ce27eefebc6ed56cf43b7b011d1d/Screenshot_2024-11-07_07-49-44.png)
jika sudah di partisi akan ada direktori turunannya seperti di atas, dan kalau sudah di mun point ada nama direktori path nya di keterangan mount point.
