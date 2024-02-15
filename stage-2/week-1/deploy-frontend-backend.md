# Deploy frontend & backend
## 1. Gunakan nodejs versi 14.x
### 1.1 pertama install nodejs versi 14.x
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```

```
exec bash
```
```
nvm install 14
```
## 2. Clone repository Wayshub frontend & backend lalu deploy aplikasinya menggunakan PM2
### 2.1 Clone repository Wayshub frontend & backend
```
git clone https://github.com/dumbwaysdev/wayshub-frontend
```
```
git clone https://github.com/dumbwaysdev/wayshub-backend
```
# frontend
```
npm install -g pm2
```
### 2.3 deploy aplikasinya menggunakan PM2

```
cd wayshub-frontend/
```
```
npm install
```

```
pm2 ecosystem simple
```
```
nano ecosystem.config.js
```

```
module.exports = {
  apps : [{
    name   : "frontend",
    script : "npm start"
  }]
}
```

```
pm2 start
```

lalukan hal yang sama pada direktori wayshub-backend

```
module.exports = {
  apps : [{
    name   : "backend",
    script : "npm start"
  }]
}
```

```
pm2 start
```


# mysql
## 3. Install dan konfigurasi database MySQL (mysql_secure_installation)
### 3.1 sebelum konfigurasi apt update terlebih dahulu
```
exec bash
```
```
sudo apt update
```

```
sudo apt install mysql-server
```

```
sudo systemctl status mysql.service
```
### 3.4 kemudian buat password untuk user root dan tambah user baru sesuai nama

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

```
sudo mysql_secure_installation
```

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

pada bagiaon ini ganti baseURL dengan domain kita masing-masing
## 5. Di wayshub-backend, rubah isi konfigurasi database MySQL di config/config.json sesuai dengan user pass kalian, dengan nama database "db-wayshub"
### 5.1 kemudian konfigurasi config.json pada direktori wayshub-backend

```
cd wayshub-backend/
```
```
cd config/
```
```
nano config.json
```

pada bagian ini ganti username password sesuai user yang sudah kita buat dan berikan nama database nya
### 5.2 menggabungkan konfigurasi database yang kita buat
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
