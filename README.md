# Web-Devstack-project-Openstack-

Dự án **Multi-tier Web Application** (Frontend & Backend) chạy trên **OpenStack Zun**, kèm theo cách trình bày để ghi điểm vào CV.

### Bước 1: Chuẩn bị máy ảo (VM)

Bạn cần 1 VM Ubuntu 22.04 LTS sạch với cấu hình:

* **RAM:** Tối thiểu 8GB (Khuyến nghị 12GB+ nếu chạy nhiều container).
* **CPU:** 4 Cores.
* **Ổ cứng:** 50GB.
* **Network:** Chế độ NAT hoặc Bridged có internet.

### Bước 2: Cài đặt DevStack với dịch vụ Container (Zun)

Đăng nhập vào VM và thực hiện các lệnh sau:

1. **Tạo user chuyên dụng:**
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


3. **Cấu hình `local.conf`:**
Tạo file cấu hình để kích hoạt Dashboard (Horizon) và Container (Zun):
```bash
nano local.conf

```


Dán nội dung này vào (thay `HOST_IP` bằng IP của VM bạn):
```text
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

HOST_IP=192.168.x.x

# Kích hoạt Zun và các thành phần phụ trợ
enable_plugin zun https://opendev.org/openstack/zun stable/2024.1
enable_plugin kuryr-libnetwork https://opendev.org/openstack/kuryr-libnetwork stable/2024.1

# Kích hoạt Horizon (Dashboard)
enable_service horizon

```


4. **Cài đặt:**
```bash
./stack.sh

```


*Đợi khoảng 20-40 phút. Sau khi xong, hệ thống sẽ hiện link Horizon (thường là `http://IP-CUA-BAN/dashboard`).*

---

### Bước 3: Triển khai dự án Web Frontend & Backend

Chúng ta sẽ dùng mô hình:

* **Backend:** Một API đơn giản bằng Python (Flask/FastAPI) hoặc Node.js.
* **Frontend:** Nginx phục vụ file tĩnh hoặc React.

#### 1. Sử dụng Dashboard (Horizon) để quản lý

1. Truy cập IP Dashboard trên trình duyệt. Đăng nhập (User: `admin` / Pass: `secret`).
2. Vào mục **Container** (nếu Zun được tích hợp giao diện) hoặc sử dụng CLI để nhanh và chuyên nghiệp hơn.

#### 2. Triển khai bằng CLI (Cách này chuyên nghiệp hơn cho DevOps)

Mở terminal trên VM, chạy `source openrc admin admin` rồi thực hiện:

**A. Tạo Backend Container (Ví dụ chạy một API giả lập):**

```bash
# Tạo container backend chạy trên port 5000
zun create --name api-backend --image kennethreitz/httpbin -p 5000:80
zun start api-backend

```

**B. Tạo Frontend Container (Kết nối tới Backend):**

```bash
# Tạo container frontend chạy Nginx
zun create --name web-frontend --image nginx -p 80:80
zun start web-frontend

```

**C. Kiểm tra mạng:**

```bash
zun list
# Lấy IP của api-backend để cấu hình cho frontend nếu cần.

```

