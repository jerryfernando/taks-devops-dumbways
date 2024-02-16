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
### remote aws ke lokal
create aws IAM
![create iam](https://github.com/jerryfernando/Final-task/assets/23428256/b8f996c9-48ae-4d0e-8476-c725a6286a36)
![security](https://github.com/jerryfernando/Final-task/assets/23428256/be4bc2ed-6921-4da4-9e79-0873d73413d0)

create access-key dan secret-access-key
![create access key](https://github.com/jerryfernando/Final-task/assets/23428256/7742d74c-8c91-4509-9e4a-c4f934baf7f0)
![attach policies](https://github.com/jerryfernando/Final-task/assets/23428256/b2343df5-0557-4fe1-bd24-8693facb2e53)
![pilih cli](https://github.com/jerryfernando/Final-task/assets/23428256/e7d62350-7728-43b7-b75e-a70ea7aa19a4)
![access keys](https://github.com/jerryfernando/Final-task/assets/23428256/7e87c167-d2b7-49a4-8730-8ecc695b65aa)

create permissions
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3Access",
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```

![permmisions](https://github.com/jerryfernando/Final-task/assets/23428256/28883c4a-c97f-4c88-b353-ed84cf4499e4)

```bash

  aws configure
```
```
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: 
Default output format [None]:
```

![aws configure](https://github.com/jerryfernando/Final-task/assets/23428256/a6a7863a-2a60-4c6a-9861-86bc8f22a6b2)

aws ec2 describe-instances


buat folder `terraform-aws` lalu buat sudo nano/vim `main.tf` 
```
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

resource "aws_vpc" "main" {
  cidr_block = "10.5.0.0/16"

  tags = {
      Name = "tuts vpc"
  }
}

resource "aws_subnet" "web" {
    vpc_id = aws_vpc.main.id
    cidr_block = "10.5.0.0/16"

    tags = {
        Name = "web-subnet"
    }
}

resource "aws_instance" "jeri" {
  ami           = "ami-002843b0a9e09324a"
  instance_type = "t2.micro"
  availability_zone = "ap-southeast-1b"
  associate_public_ip_address = true

  tags = {
    Name = "terraform"
  }
}
```

setelah itu buat sudo nano/vim `terraform_command.sh` lalu jalankan
```
#!/usr/bin/env bash

terraform init
terraform plan
terraform validate
terraform apply
```
sudo chmod +x terraform_command.sh
./terraform_command.sh

hasil 

![hasil1](https://github.com/jerryfernando/Final-task/assets/23428256/cfa5fd55-f526-446a-a991-c220fed9099c)


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


