# Dong-learns-Docker

# Mục tiêu
- Làm quen với Docker:
  Biết cách cài đặt, chạy container, hiểu được Docker hoạt động ra sao
- Dockerfile và Image:
  Tạo image tùy chỉnh với `Dockerfile`
- Docker Compose:
  Chạy nhiều container cùng lúc bằng cách viết file `docker-compose.yml`
- Thực hành dự án:
  Xây dựng ứng dụng thực tế gồm Backend, Database và Frontend.

---

# 1. Làm quen với Docker
## 1.1. Cài đặt Docker Desktop (Windows/macOS)
- Tải tại: https://www.docker.com/products/docker-desktop/
- Sau khi cài đặt xong:
  Mở `Terminal` hoặc `PowerShell`, chạy: `docker --version`
  Nếu hiện version --> Docker đã cài đặt thành công.
  ![image](https://github.com/user-attachments/assets/0a140646-eeac-4b24-87db-27380ea05850)

## 1.2. Chạy container đầu tiên
- Lệnh CLI cơ bản: `docker run hello-world`
- Lệnh này sẽ:
  * Tìm image `hello-world` từ Docker Hub.
  * Tạo và chạy container từ image đó.
  * Hiển thị thông báo chào mừng nếu thành công.

## 1.3. Kiểm tra container đang chạy
- `docker ps # Container đang chạy`
- `docker ps -a # Tất cả container (đã dừng và đang chạy)`

## 1.4. Thực hành: chạy container NGINX
- `docker run -d -p 8080:80 --name web nginx`
