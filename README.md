การติดตั้ง **Joomla** บน Kali Linux จะต้องสร้างสภาพแวดล้อมที่เรียกว่า **LAMP Stack** (Linux, Apache, MySQL/MariaDB, PHP) ขึ้นมาก่อน เนื่องจาก Joomla เป็นระบบจัดการเนื้อหา (CMS) ที่ต้องรันบนเว็บเซิร์ฟเวอร์และมีฐานข้อมูลในการเก็บข้อมูลครับ

นี่คือขั้นตอนการติดตั้งทีละสเต็ปที่สามารถทำตามได้ทันที:

## ขั้นตอนการติดตั้ง Joomla บน Kali

1. **ติดตั้งระบบ LAMP Stack:** เตรียม Web Server และ PHP.
อัปเดตฐานข้อมูลแพ็กเกจ และติดตั้ง Apache2, MariaDB พร้อมกับ PHP Extensions ทั้งหมดที่ Joomla จำเป็นต้องใช้ในการทำงาน:

```bash
sudo apt update
sudo apt install apache2 mariadb-server php php-mysql php-xml php-gd php-curl php-zip php-intl php-mbstring unzip -y

```


2. **เปิดบริการและตั้งค่าฐานข้อมูล:** สร้าง Database และ User.
สั่งเปิดใช้งานเว็บเซิร์ฟเวอร์และระบบฐานข้อมูลก่อน:

```bash
sudo systemctl start apache2 mariadb
sudo systemctl enable apache2 mariadb

```

จากนั้นล็อกอินเข้า MariaDB เพื่อสร้างฐานข้อมูลสำหรับ Joomla:

```bash
sudo mysql -u root

```

เมื่อเข้าสู่หน้าจอคอลโซลของฐานข้อมูลแล้ว ให้พิมพ์คำสั่ง SQL ต่อไปนี้ (แนะนำให้เปลี่ยน `your_password` เป็นรหัสผ่านที่คุณต้องการ):

```sql
CREATE DATABASE joomla_db;
CREATE USER 'joomla_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON joomla_db.* TO 'joomla_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

```


3. **ดาวน์โหลดและแตกไฟล์ Joomla:** ดาวน์โหลดเวอร์ชันล่าสุด.
ย้ายเข้าไปที่ไดเรกทอรีหลักของเว็บเซิร์ฟเวอร์ แล้วใช้ `wget` ดึงไฟล์ติดตั้งของ Joomla (ตัวอย่างนี้เป็นเวอร์ชัน 6.x ล่าสุด) จากนั้นแตกไฟล์ลงโฟลเดอร์ชื่อ `joomla`:

```bash
cd /var/www/html
sudo wget https://github.com/joomla/joomla-cms/releases/download/6.0.3/Joomla_6.0.3-Stable-Full_Package.zip
sudo mkdir joomla
sudo unzip Joomla_6.0.3-Stable-Full_Package.zip -d joomla
sudo rm Joomla_6.0.3-Stable-Full_Package.zip

```


4. **ตั้งค่าสิทธิ์การเข้าถึงไฟล์ (Permissions):** อนุญาตสิทธิ์ให้ Apache.
เปลี่ยนสิทธิ์การเป็นเจ้าของโฟลเดอร์ให้เป็นของระบบเว็บเซิร์ฟเวอร์ (`www-data`) เพื่อให้ระบบของ Joomla สามารถติดตั้งและเขียนไฟล์ลงโฟลเดอร์ได้โดยไม่มีปัญหาเรื่อง Permission Denied:

```bash
sudo chown -R www-data:www-data /var/www/html/joomla
sudo chmod -R 755 /var/www/html/joomla

```


5. **ตั้งค่าต่อบนเว็บเบราว์เซอร์:** ขั้นตอนสุดท้าย.
เปิดเว็บเบราว์เซอร์ในตัว Kali Linux แล้วเข้าไปที่ URL นี้:

> **http://localhost/joomla**

ระบบจะแสดงหน้าต่างการตั้งค่าของ Joomla ขึ้นมา ให้คุณกรอกข้อมูลชื่อเว็บไซต์, ชื่อแอดมิน และในส่วนของ **Database Configuration** ให้กรอกข้อมูลที่เราสร้างไว้ในขั้นตอนที่ 2:

* **Database Type:** MySQLi
* **Hostname:** localhost
* **Username:** `joomla_user`
* **Password:** รหัสผ่านที่คุณตั้งแทน `your_password`
* **Database Name:** `joomla_db`


---

หลังจากกรอกข้อมูลและทำตามหน้าจอเบราว์เซอร์จนจบ คุณก็จะได้ระบบ Joomla พร้อมใช้งานในเครื่อง Kali สำหรับเอาไว้ศึกษา พัฒนาเว็บไซต์ หรือเอาไว้ทำแล็บทดสอบความปลอดภัยแล้วครับ
