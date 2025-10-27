# 🛡️ Cis Check list

> เครื่องมือตรวจสอบนโยบายความปลอดภัยของระบบ Linux

---

# 📦 การติดตั้ง

```bash
sudo wget https://github.com/cis-95/ciscl/releases/download/V1.0.2/ciscl
sudo chmod +x ciscl
```

---
# PAM

## 🔧 คำสั่งที่ใช้ได้

| Command                                  | คำอธิบาย                                                                           |
| ---------------------------------------- | ---------------------------------------------------------------------------------- |
| `ip`                                     | แสดงรายการ Private IP (IPv4 RFC1918 และ IPv6 ULA)                                  |
| `user` หรือ `-u`                         | แสดงรายชื่อผู้ใช้ทั้งหมดที่ไม่ใช่ root                                             |
| `expiry` หรือ `-e`                       | ตรวจสอบ Password Expiry Policy ของผู้ใช้ทุกคน                                      |
| `detail <username>` หรือ `-d <username>` | แสดงรายละเอียด Password Policy ของผู้ใช้ที่ระบุ                                    |
| `ssh` หรือ `-s`                          | ตรวจสอบการตั้งค่า SSH และ Password Quality (เช่น `PermitRootLogin`, `minlen`, ฯลฯ) |
| `login` หรือ `-l`                        | ตรวจสอบว่าผู้ใช้ login ด้วย private key หรือไม่ จาก `/var/log/auth.log`            |
| `sudosu` หรือ `-su`                      | ตรวจสอบว่า user ใดเคยใช้ `sudo su`                                                 |
| `all`                                    | รันการตรวจสอบทั้งหมดตามลำดับ (ครบทุกส่วน)                                          |
| `help` หรือ `-h`                         | แสดงวิธีใช้งานทั้งหมด                                                              |

---

## 🧰 ตัวอย่างการใช้งาน

### 1️⃣ ตรวจสอบ IP ภายในเครื่อง

```bash
./ciscl pam ip
```

**ผลลัพธ์ตัวอย่าง**

```
Private IPs:
eth0: 192.168.1.10
lo: fc00::1
```

---

### 2️⃣ แสดงรายชื่อผู้ใช้ที่ไม่ใช่ root

```bash
./ciscl pam user
```

**ผลลัพธ์ตัวอย่าง**

```
Found 3 non-root users:
   1. user1
   2. user2
   3. user3
```

---

### 3️⃣ ตรวจสอบนโยบายการหมดอายุรหัสผ่าน

```bash
sudo ./ciscl pam expiry
```

**ผลลัพธ์ตัวอย่าง**

```
Password policy (Expired) : Unsuccess
User: devops
Last password change: 2025-05-01
Password expires: 2025-08-01
...
```

> ⚠️ ต้องใช้ `sudo` เพราะต้องอ่านไฟล์ `/etc/shadow`

---

### 4️⃣ ดูรายละเอียด password policy ของผู้ใช้เฉพาะ

```bash
sudo ./ciscl pam detail devops
```

**ผลลัพธ์ตัวอย่าง**

```
User: devops
Last password change: 2025-05-01
Password expires: 2025-08-01
Password inactive: never
Account expires: never
Min days between change: 1
Max days between change: 90
Days of warning before expiry: 7
```

---

### 5️⃣ ตรวจสอบการตั้งค่า SSH / Password Quality

```bash
./ciscl pam ssh
```

**ผลลัพธ์ตัวอย่าง**

```
Permit root login checklist : Unsuccess
expected: PermitRootLogin no, found: PermitRootLogin yes
expected: PasswordAuthentication no, found: notfound
...
```

---

### 6️⃣ ตรวจสอบการ login ด้วย Private Key

```bash
sudo ./ciscl pam login
```

**ผลลัพธ์ตัวอย่าง**

```
Log Login with Private Key : Unsuccess
Missing users: devops, audit
```

---

### 7️⃣ ตรวจสอบการใช้ `sudo su`

```bash
sudo ./ciscl pam sudosu
```

**ผลลัพธ์ตัวอย่าง**

```
Log sudo su : Success
```

---

### 8️⃣ ตรวจสอบทั้งหมดในครั้งเดียว

```bash
sudo ./ciscl pam all
```

---

## ⚙️ สิทธิ์ที่จำเป็น

บางคำสั่งต้องใช้สิทธิ์ `root` (หรือ `sudo`) เช่น:

* `expiry`, `detail`, `login`, `sudosu`, `all`
  เนื่องจากต้องอ่านไฟล์ระบบ เช่น `/etc/shadow`, `/var/log/auth.log`

---

## 🧩 Dependencies

เครื่องมือนี้ใช้คำสั่งและไฟล์ระบบต่อไปนี้:

* คำสั่ง: `lslogins`, `grep`, `awk`, `sort`
* ไฟล์ระบบ:

  * `/etc/passwd`
  * `/etc/shadow`
  * `/var/log/auth.log`

---

## 🧾 ตัวอย่างผลการตรวจสอบรวม (`./ciscl pam all`)

```
PAM Full Checklist Results:

Hostname: RD-LoadTest-Ubuntu24-01
Private IPs:
enp6s18: 192.168.101.2

Found 2 non-root users:
   1. devuser
   2. test

Password policy (Expired) : Success

Permit root login checklist : Success

Log Login with Public Key : Success

Log sudo su : Success
```

---
# Nginx
## 🔧 คำสั่งที่ใช้ได้

| Command                                  | คำอธิบาย                                                                           |
| ---------------------------------------- | ---------------------------------------------------------------------------------- |
| `all`                                    | รันการตรวจสอบทั้งหมดตามลำดับ (ครบทุกส่วน)                                          |
| `help` หรือ `-h`                         | แสดงวิธีใช้งานทั้งหมด                                                              |

## 🧾 ตัวอย่างผลการตรวจสอบรวม (`./ciscl nginx all`)
```
Nginx Configuration Checklist Results:

Reverse Proxy & Load Balancer Tuning
Config checklist : Success
Service File limit checklist : Success

System Tuning
Kernal checklist : Success
OS user(www-data) Limit checklist : Success

Access Log Retention
Log 90 Days checklist : Success
```
