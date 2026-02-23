# Web-Devstack-project-Openstack-

Kịch bản: **Triển khai một Web Server (Nginx) chạy trong Container trên nền tảng OpenStack**.

Dưới đây là các bước thực hiện bài lab sử dụng **DevStack** và dịch vụ **Zun** (OpenStack Container Service).

---

## Bước 1: Chuẩn bị môi trường DevStack

Bạn cần một máy ảo Ubuntu (ưu tiên 22.04) với cấu hình tốt (ít nhất 8GB RAM).

1. **Tạo user stack:**
```bash
sudo useradd -s /bin/bash -d /opt/stack -m stack
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo -u stack -i

```


2. **Tải DevStack:**
```bash
git clone https://opendev.org/openstack/devstack -b stable/2024.1
cd devstack

```



---

## Bước 2: Cấu hình cho Container (Zun)

Để chạy được Container, chúng ta cần kích hoạt dịch vụ **Zun** và **Kuryr** (kết nối mạng cho container) trong file `local.conf`.

**Tạo file `local.conf`:**

```text
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

# Kích hoạt Zun (Container Service)
enable_plugin zun https://opendev.org/openstack/zun stable/2024.1
enable_plugin zun-tempest-plugin https://opendev.org/openstack/zun-tempest-plugin stable/2024.1
enable_plugin kuryr-libnetwork https://opendev.org/openstack/kuryr-libnetwork stable/2024.1

# IP của máy bạn
HOST_IP=192.168.x.x 

```

**Chạy cài đặt:**

```bash
./stack.sh

```

---

## Bước 3: Triển khai Website bằng Container qua CLI

Sau khi cài đặt xong (mất khoảng 20-30 phút), hãy thực hiện các lệnh sau để chạy website:

1. **Thiết lập biến môi trường:**
```bash
source openrc admin admin

```


2. **Tạo một Container chạy Nginx:**
```bash
zun create --name my-website --image nginx -p 80:80 nginx

```


3. **Khởi động container:**
```bash
zun start my-website

```


4. **Kiểm tra trạng thái:**
```bash
zun list

```


* Dùng **Terraform** để viết code tự động tạo Container trên OpenStack thay vì gõ lệnh `zun create`.

**Bạn có muốn mình gửi cho bạn file mẫu Terraform để tự động hóa bước tạo Container này không?**
