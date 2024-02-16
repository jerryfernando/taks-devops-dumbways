# terraform
## 1 install terraform
```
sudo nano install_terraform.sh
```
```
#!/usr/bin/env bash

sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt-get install terraform
```
```
sudo chmod +x install_terraform.sh
```
```
./install_terraform.sh
```
## 2 insatll aws cli
```
sudo nano install_awscli.sh
```

```
#!/usr/bin/env bash

sudo apt-get install unzip -y
sudo apt-get install python3.10-venv -y
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
sudo /usr/bin/python3.10 awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```

```
sudo chmod +x install_awscli.sh
```

```
./install_awscli.sh
```

#### 1.3 Konfigurasi Access Keys
<img width="1440" alt="Screenshot 2023-10-07 at 14 05 30" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/bbdb3471-788d-4e56-bb1b-5899c23d969b">

Pertama, kita masuk ke Console AWS-nya. Lalu kita klik nama profile kita pada ujung kanan setelah itu akan muncul menu dropdown lalu pilih *Security credentials*
<br>
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 14 05 54" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/757bf0c4-a4c0-463d-a5be-ff894990fa0d">

Setelah itu scroll kebawah hingga menemukan *Access keys* setelah itu klik *Create access key*
<br>
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 14 06 06" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/ed169c23-7a86-4f97-a029-8cdc29644425">

*Access keys* yang sudah terbuat disimpan untuk kita gunakan konfigurasi environment AWS-nya

#### 1.4 Membuat Infrastruktur AWS menggunakan Terraform
 <img width="1440" alt="Screenshot 2023-10-07 at 14 07 03" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/0f7d3aa1-f99c-4a2f-a532-c9423e494a4a">

Pertama kita konfigurasi Access Keys yang sudah kita buat tadi agar Terraform memiliki akses untuk membuat Resources di AWS dengan menjalankan perintah:

```terraform
aws configure
```

Dengan spesifikasi sebagai berikut:

> AWS Access Key ID: (access-key)
>
> AWS Secret Access Key: (secret-access-key)
>
> Default region name: ap-southeast-1 (Singapore)
> 
> Default output format: (blank)

Lalu saya buat folder pada home bernamakan `terraform-aws` setelah saya membuat file bernama `main.tf` lalu saya isikan script berikut:

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region  = "ap-southeast-1"
}

resource "aws_instance" "app_server" {
  ami           = "ami-002843b0a9e09324a"
  instance_type = "t2.micro"

  tags = {
    Name = "ec2-as1-1a-d-terraform"
  }
}
```

Dari script diatas saya melakukan beberapa penyesuaian dengan merubah beberapa parameter, dan berikut parameter yang saya ubah:

> region = "ap-southeast-1" (Singapore)
> 
> ami = "ami-002843b0a9e09324a" (Ubuntu 20.04)
>
> Name = "ec2-as1-1b-d-terraform"

Setelah file ter-save, Saya meng-inisialisasi direktori yang terdapat script seperti diatas dengan menjalankan perintah:

```terraform
terraform init
```

<img width="1440" alt="Screenshot 2023-10-07 at 14 16 24" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/7219e9af-a9ee-4c20-b377-cf827a1ced2c">

Lalu saya mem-verifikasi apakah script konfigurasi Terraform-nya sudah benar dengan menjalankan perintah:

```terraform
terraform validate
```

Jika konfigurasi benar maka akan muncul keterangan valid seperti gambar diatas
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 14 16 38" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/8bc7eae1-a213-4f82-8d0c-a483bb9ec822">

Lalu saya terapkan script konfigurasi Terraform yang sudah saya buat dengan menjalankan perintah:

```terraform
terraform apply
```

<img width="1440" alt="Screenshot 2023-10-07 at 14 16 48" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/c87b354f-9d67-43d2-bd25-ba74edd0066d">

Disini ketikan `yes`
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 14 17 38" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/63cea694-3434-4cd8-8ae4-0b69c7b8596d">
<img width="1440" alt="Screenshot 2023-10-07 at 14 19 26" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/bd8c6e00-40c7-4960-80d5-5d7331b5692e">

Berikut bukti bahwa pembuatan infrastruktur AWS menggunakan Terraform dapat berjalan dengan baik

### 2. Buatlah Terraform untuk men-deploy Docker Image wayshub-fe & Praktekkan penggunaan Terraform dari pembuatan hingga Resource di hancurkan

#### 2.1 Men-deploy Wayshub-fe on Top Docker menggunakan Terraformn
Untuk men-deploy Docker pada Linux, terdapat prasyarat yang harus dipenuhi sebagai berikut:

> 1. Docker Engine sudah ter-install.

<img width="1440" alt="Screenshot 2023-10-07 at 15 34 30" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/41c3bfbb-d983-4bde-88b8-16a18980882f">

Disini saya membuat direktori bernamakan `wayshub-fe` lalu saya masuk ke dalam direktori tersebut setelah itu saya membuat file bernamakan `main.tf` dengan memasukan perintah:

```terraform
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}

provider "docker" {}

resource "docker_image" "wayshub-fe" {
  name         = "calvinnr/wayshub-fe:latest"
  keep_locally = false
}

resource "docker_container" "wayshub-fe" {
  image = docker_image.wayshub-fe.image_id
  name  = "wayshub-fe"

  ports {
    internal = 3000
    external = 3000
  }
}
```

Dari script diatas saya melakukan beberapa penyesuaian dengan merubah beberapa parameter, dan berikut parameter yang saya ubah:

> resource "docker_image" "wayshub-fe"
>
> name = "calvinnr/wayshub-fe:latest"
>
> resource "docker_container" "wayshub-fe"
>
> image = docker_image.wayshub-fe.image_id
>
> name = "wayshub-fe"
>
> internal = 3000
>
> external = 3000

<img width="1440" alt="Screenshot 2023-10-07 at 15 35 31" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/44a43a25-9441-496f-8dff-b857ceedbe32">
Setelah file ter-save, Saya meng-inisialisasi direktori yang terdapat script seperti diatas dengan menjalankan perintah:

```terraform
terraform init
```

<img width="1440" alt="Screenshot 2023-10-07 at 15 35 36" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/22cc6b43-a567-4b73-9f56-69a8b1ae08b4">

Lalu saya mem-verifikasi apakah script konfigurasi Terraform-nya sudah benar dengan menjalankan perintah:

```terraform
terraform validate
```

Jika konfigurasi benar maka akan muncul keterangan valid seperti gambar diatas
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 15 35 42" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/6ef85924-e565-4a1f-a7d7-85b18a4be6ef">

Lalu saya terapkan script konfigurasi Terraform yang sudah saya buat dengan menjalankan perintah:

```terraform
terraform apply
```

<img width="1440" alt="Screenshot 2023-10-07 at 14 16 48" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/c87b354f-9d67-43d2-bd25-ba74edd0066d">

Disini ketikan `yes`
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 15 36 07" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/271a964c-5ccb-4232-89f9-fc281963e478">
<img width="1440" alt="Screenshot 2023-10-07 at 15 40 01" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/51bf9c23-8319-4e1c-becf-4a0b5c90ba5b">

Berikut bukti bahwa pembuatan infrastruktur AWS menggunakan Terraform dapat berjalan dengan baik

<img width="1440" alt="Screenshot 2023-10-07 at 15 42 23" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/36a8bb28-daa9-4366-a101-01cf511f250d">

Lalu saya stop Container yang berjalan dengan menjalan perintah:

```terraform
terraform destroy
```

<img width="1440" alt="Screenshot 2023-10-07 at 15 42 32" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/1b3d2c11-e269-4b5d-8808-bddbd8f3dfcc">

Disini ketikan `yes`
<br>

<img width="1440" alt="Screenshot 2023-10-07 at 15 42 37" src="https://github.com/calvinnr/devops18-dw-calvinnr/assets/101310300/bf253e7f-44b0-4016-8dc7-0b94f50707b0">

Berikut bukti bahwa container yang menjalankan wayshub-fe sudah diberhentikan/dihancurkan.


