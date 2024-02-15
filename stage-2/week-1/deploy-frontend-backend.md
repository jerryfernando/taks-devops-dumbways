# Deploy frontend & backend
## 1. Gunakan nodejs versi 14.x
### 1.1 pertama install nodejs versi 14.x
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```
![292940205-b98cc5fd-817d-4054-b651-f5250eb41db1](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/106dbbe6-283e-4c04-bb5a-0d457746f573)

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
# 3. frontend
```
npm install -g pm2
```
### 3.1 deploy aplikasinya menggunakan PM2

```
cd wayshub-frontend/
```
```
npm install
```
```
npm install pm2 -g

```
```
pm2 ecosystem simple
```
```
sudo nano ecosystem.config.js
```

```
module.exports = {
  apps : [{
    name   : "frontend",
    script : "npm start"
  }]
}
```
![292940234-08481e36-52d9-4f56-b848-fc45a7bfdb85](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/fbf59418-fd67-44af-9cd3-a2e36cd36ed7)

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
![292938935-6a606f9b-30e6-45cf-8e6e-f02f409f404b](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/ac1401f7-9f0b-43cc-acff-a1d276c490ee)


```
pm2 start
```


# mysql
## 4. Install dan konfigurasi database MySQL (mysql_secure_installation)
### 4.1 sebelum konfigurasi apt update terlebih dahulu
```
exec bash
```
```
sudo apt update
```
konfigurasi mysql
![292938811-5d0fec94-7cd6-4a23-a954-654aedb502de](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/2b2a22b2-a25a-4f63-acb5-88d12f818c8b)
![292938817-00145b7c-456f-4df0-9b56-786c7684850a](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/42a89cdc-9147-403c-8644-e4fcb5426858)


```
sudo apt install mysql-server
```
![292938714-9a178f86-8825-43b8-ac75-4bdbf6f5849b](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/c4428436-9c19-4cca-b4e4-be06ddb7244e)

```
sudo systemctl status mysql.service
```
### 4.2 kemudian buat password untuk user root dan tambah user baru sesuai nama

```
sudo mysql_secure_installation
```
![292938752-8d8d4573-4d43-4716-af28-f158a7c1b53e](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/462374b8-5f6b-4549-a6d1-397ce38c5a9c)

```
SELECT user, host FROM mysql.user
```
```
DROP USER 'root'@'localhost';
```
![292938758-dd98bb59-e8ab-4876-aebe-2ffc22e94aa2](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/3b6123a2-86d9-4230-948d-4360a41e35b5)
```
SELECT user, host FROM mysql.user
```
```
CREATE USER 'root'@'localhost' IDENTIFIED BY 'jeri777!'
```
```
SELECT user, host FROM mysql.user
```
![292938765-d262e549-cb1a-4d1e-ab49-a1f9146798ca](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/621acfba-114a-4cda-a4bf-6acf77f73e97)
BISA DI REMOTE
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
```
atau

TDIAK BISA DI REMOTE/HANYA DI LOCAL HOST
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION;
```
![292938772-bf932703-bdc2-48c3-9fe6-63bc3b497900](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/acce04a0-33cc-4e01-805e-a1641e0dd179)
```
FLUSH PRIVILEGES
```
buat database untuk menampung mysql
```
CREATE DATABASE wayshub;
```
```
SHOW DATABASES;
```
![292938787-45d49f6e-57e9-434e-8d74-3a905d86da90](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/cb88ca7c-ae9b-4615-bc0d-f150a9c59612)
buat user baru lagi dengan nama sendiri dan bisa di remote
```
CREATE USER 'jeri'@'%' IDENTIFIED BY 'jeri777!'
```
berikan privileges
![292938792-88c87fe9-6fd8-4be6-aa51-530b15fc2c3b](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/e5797f9a-1ed4-42f7-9e85-e978d7b7802e)
lalu masuk ke user jeri lihat sudah masuk atau belumnya databasenya
![292938803-5ec90aeb-cdcf-457b-b4c4-0edc6b6fc78b](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/4321aee0-bc7f-49e0-bf1d-17a4b29d1571)

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
![292940220-3a4b73a6-95f8-4932-b719-73c8935d910a](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/95415070-ff98-459a-9ebf-c95935f4440b)
![292940214-a0edf958-ddf3-4879-8680-24503b58045e](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/7cc1d7b4-271e-4ee4-9e55-236195421510)

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
![292938838-32ae3df0-a05a-41ad-9874-6bb30f7b6867](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/04c414b0-21b3-490e-a314-60981d7db2f2)
![292938864-9de7ffa4-e9f0-4dc3-9499-cc9ed9cb17dc](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/228667ed-de8d-45e1-b9a8-26fd24046077)

pada bagian ini ganti username password sesuai user yang sudah kita buat dan berikan nama database nya
### 5.2 menggabungkan konfigurasi database yang kita buat
```
cd wayshub-backend/
```
```
npm install
```
![292938873-121db269-d803-4cfb-a250-3cda0f342baf](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/f6e6c61e-9692-4cbe-811b-353d34a8a187)
```
nvm install
```
![292938877-fed788ea-6e5c-4683-9e44-240326226817](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/49347e26-dddc-45fa-8a4b-46b1696ff4b5)

```
npm install -g sequelize-cli
```
![292938887-807b760d-2886-4e57-a197-7430b87017cc](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/9ccf30d0-0581-420d-8a4b-6b6b9c5e3647)

```
npx sequelize db:migrate
```
![292938890-15d53d7f-ce16-4744-aaff-3c5d18c73d00](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/f0732288-f033-4796-b1e2-22c951ff44e0)


### 5.3 lihat hasil migrate mysql
![292938902-d34c2d28-0dda-4dce-92ab-0072f3494a75](https://github.com/jerryfernando/taks-devops-dumbways/assets/23428256/cca9a2b4-3dcf-47e5-a12b-dbe6c177badb)


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
