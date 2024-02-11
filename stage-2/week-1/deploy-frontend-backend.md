# Appserver
## 1. Gunakan nodejs versi 14.x
### 1.1 pertama install nodejs versi 14.x
![Screenshot_8](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/13fa1228-d199-4a09-b5b3-1dc64073e66f)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```
![Screenshot_9](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/5e96818b-96ec-417b-ae7f-99df1bc4a237)
```
exec bash
```
```
nvm install 14
```
## 2. Clone repository Wayshub frontend & backend lalu deploy aplikasinya menggunakan PM2
### 2.1 Clone repository Wayshub frontend & backend
![Screenshot_7](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/5df0edce-4b85-4571-9b03-257ae8e24887)
```
git clone https://github.com/dumbwaysdev/wayshub-frontend
```
```
git clone https://github.com/dumbwaysdev/wayshub-backend
```
### 2.2 install PM2
![Screenshot_10](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/e014fc54-681c-4eb6-be05-3468599739cc)
```
npm install -g pm2
```
### 2.3 deploy aplikasinya menggunakan PM2
![Screenshot_11](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/5f2d63e6-ff7e-4726-89c1-9e8409dd8f0f)
```
cd wayshub-frontend/
```
```
npm install
```
![Screenshot_12](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/79adb24d-bba7-4c41-9619-0d71c1b0fa4a)
```
pm2 ecosystem simple
```
```
nano ecosystem.config.js
```
![Screenshot_13](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/71386a7f-702c-4746-9e14-fd4be27dac87)
```
module.exports = {
  apps : [{
    name   : "frontend",
    script : "npm start"
  }]
}
```
![Screenshot_14](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/bab61aaa-534c-453e-aa8d-80d649a8d4ae)
```
pm2 start
```
![Screenshot_15](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/4b5315c2-c69f-4240-ba64-9ef7dc23bdbc)
lalukan hal yang sama pada direktori wayshub-backend
![Screenshot_16](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/b16aa1d4-b0b9-43b3-a85b-5415fb19f84c)
```
module.exports = {
  apps : [{
    name   : "backend",
    script : "npm start"
  }]
}
```
![Screenshot_17](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/12bfde8e-989f-4e90-bef1-f2174a93b0d7)
```
pm2 start
```
## 3. Install dan konfigurasi database MySQL (mysql_secure_installation)
### 3.1 sebelum konfigurasi apt update terlebih dahulu
![Screenshot_18](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/dbf9cf0d-5d31-4006-ac61-7eb7aa07a932)
```
exec bash
```
```
sudo apt update
```
### 3.2 masuk pada sources.list lalu ganti mirrors.idcloudhost.com menjadi archive.ubuntu.com
![Screenshot_19](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/363fe602-5079-4088-b563-598b54f30844)
```
sudo nano /etc/apt/sources.list
```
replace dengan menekan ctrl + \
![Screenshot_20](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/623be78f-2ab8-4564-83b7-6bf7f31a68a2)
```
mirrors.idcloudhost.com
```
![Screenshot_21](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/bea60a71-e579-4649-93f6-a92d24fc3805)
```
archive.ubuntu.com
```
lalu enter dan replace all dengan menekan a
![Screenshot_22](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/f161cf8f-19bf-4287-9a5f-1bb75d20969f)
### 3.3 kemudian Install database MySQL dan cek status MySQL
![Screenshot_23](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/672e0bfd-def4-4aa1-a18e-9609b4d82829)
```
sudo apt install mysql-server
```
![Screenshot_24](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/3bd438b9-663b-4369-867a-cc3304d0c6ce)
```
sudo systemctl status mysql.service
```
### 3.4 kemudian buat password untuk user root dan tambah user baru sesuai nama
![Screenshot_25](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/de350f79-4d81-4aef-be69-68502106643e)
```
sudo mysql -u root
```
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'Wilson123';
```
```
CREATE USER 'wilson'@'%' IDENTIFIED WITH mysql_native_password BY 'Wilson123';
```
### 3.5 setting mysql_secure_installation dan berikan hak akses pada user yang kita buat
jalankan sekuritas mysql sesuai step by step
![Screenshot_26](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/6406bddb-6301-45e9-baf0-e733aec684cf)
```
sudo mysql_secure_installation
```
![Screenshot_27](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/893ff124-9ac2-41ee-93fd-ecfc4ef42620)
```
sudo mysql -u root -p
```
```
GRANT ALL PRIVILEGES ON *.* TO 'wilson'@'%';
```
atau
```
GRANT ALL PRIVILEGES ON *.* TO 'wilson'@'%' WITH GRANT OPTION;
```
## 4. Di wayshub-frontend, rubah isi BaseURL file src/config/api.js menggunakan domain yang sudah disediakan (api.<nama>.studentdumbways.my.id)
### 4.1 kemudian konfigurasi api.js pada direktori wayshub-frontend
![Screenshot_30](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/2ef67eba-5ee5-4cd1-8079-4e858e73b001)
```
cd wayshub-frontend/
```
```
cd src/
```
```
cd config/
```
```
nano api.js
```
![Screenshot_62](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/806380ad-d0c9-48d0-b510-7ff4e5989277)
pada bagiaon ini ganti baseURL dengan domain kita masing-masing
## 5. Di wayshub-backend, rubah isi konfigurasi database MySQL di config/config.json sesuai dengan user pass kalian, dengan nama database "db-wayshub"
### 5.1 kemudian konfigurasi config.json pada direktori wayshub-backend
![Screenshot_28](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/8766e05d-61af-440f-81ec-7249fd93dac5)
```
cd wayshub-backend/
```
```
cd config/
```
```
nano config.json
```
![Screenshot_29](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/b5b38e94-bd81-4f49-ae5c-3c4aad812063)
pada bagian ini ganti username password sesuai user yang sudah kita buat dan berikan nama database nya
### 5.2 menggabungkan konfigurasi database yang kita buat
![Screenshot_32](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/b94281a3-a409-4674-8092-12530d37d3d4)
```
cd wayshub-backend/
```
```
npm install -g sequelize-cli
```
```
sequelize db:create
```
```
sequelize db:migrate
```
### 5.3 jalankan ulang aplikasinya menggunakan PM2
![Screenshot_33](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/9cfe980e-fe2f-4c22-b5a7-841d903ac5c8)
```
pm2 start
```
![Screenshot_64](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/ccaf3680-34eb-4038-9ad4-830ebfe6aec5)
registrasi user pada fontend wayshub
![Screenshot_65](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/03cc8131-b31d-4c89-b128-9d4850280522)
registrasi user berhasil
![Screenshot_66](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/b910cbbe-8ba4-43af-88a7-b93340508c36)
test login menggunakan user yang telah di buat
![Screenshot_67](https://github.com/wilsonakbar/devops18-dumbways-WilsonAkbar/assets/132327628/9a5d3a58-0209-4e38-aa67-3cdcf3fa9515)
sukses masuk dengan user yang telah di buat
