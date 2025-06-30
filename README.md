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

# I. Tổng quan về Docker
## 1. Docker là gì?
- Docker là nền tảng ảo hóa ở cấp độ hệ điều hành, giúp bạn:
* Đóng gói toàn bộ môi trường (code + thư viện + hệ điều hành) vào 1 "container".
* Chạy môi trường đó trên bất kỳ máy nào có cài Docker.
* Giảm vấn đề "chạy được trên máy anh nhưng không chạy được trên máy em".

## 2. Các khái niệm quan trọng
- Image: một bản build của ứng dụng, như là một "mẫu container"
- Container: Một instance (bản chạy) của image. Bạn có thể tạo nhiều container từ cùng 1 image.
- Dockerfile: file chứa tập hợp các hướng dẫn để tạo ra image.
- Volume: Dữ liệu dùng chung giữa máy chủ và container.
- Network: Kết nối giữa các container với nhau.
![image](https://github.com/user-attachments/assets/c6e6e505-fa5e-45ed-82ee-17da5c38b3b4)

---

# II. Cài đặt Docker
## 1 Cài đặt Docker Desktop (Windows/macOS)
- Tải và làm theo hướng dẫn docs tại: https://www.docker.com/products/docker-desktop/
- Sau khi cài đặt xong:
  Mở `Terminal` hoặc `PowerShell`, chạy: `docker --version`
  Nếu hiện version --> Docker đã cài đặt thành công.
  ![image](https://github.com/user-attachments/assets/0a140646-eeac-4b24-87db-27380ea05850)

## 2. Chạy container đầu tiên
- Lệnh CLI cơ bản: `docker run hello-world`
- Lệnh này sẽ:
  * Tìm image `hello-world` từ Docker Hub.
  * Tạo và chạy container từ image đó.
  * Hiển thị thông báo chào mừng nếu thành công.

## 3. Kiểm tra container đang chạy
- `docker ps # Container đang chạy`
- `docker ps -a # Tất cả container (đã dừng và đang chạy)`

## 4. Thực hành: chạy container NGINX
- `docker run -d -p 8080:80 --name web nginx`
  * `-d`: chạy nền (detached)
  * `-p 8080:80`: ánh xạ cổng máy thật (8080) --> container (80)
  * `--name web`: đặt tên container là "web"
 
## 5. Dừng, xóa container
- `docker stop web # dừng container`
- `docker rm web # xóa container`
- `docker rmi nginx # xóa image`

---

# III. CLI
- Command-line interface là cách bạn điều khiển Docker bằng lệnh trong Terminal (CDM, PowerShell hoặc WSL)
- Một số lệnh Docker cơ bản:

| Lệnh Docker | Mô tả |
|-------------|-------|
| `docker --version` | Kiểm tra cài đặt Docker |
| `docker pull image_name` | Tải image từ Docker Hub |
| `docker run image_name` | Tạo và chạy container |
| `docker ps` | Liệt kê các container đang chạy |
| `docker ps -a` | Liệt kê các container (gồm cả dừng và đang chạy) |
| `docker ps -l` | Xem container gần nhất |
| `docker stop container_id` | Dừng container |
| `docker stop $(docker ps -q)` | Dừng toàn bộ container đang chạy |
| `docker build -t name .` | Build image từ Dockerfile trong thư mục hiện tại |
| `docker exec -it container bash` | Truy cập bên trong container |
| `docker rm container_id` | Xóa container |
| `docker rmi image_id` | Xóa image |
| `docker network ls` | Liệt kê các mạng Docker |
| `docker volume ls` | Liệt kê volumes |
| `docker stats` | Xem tài nguyên đang dùng của container |
| `docker system prune` | Xóa toàn bộ unused containers, networks, volumes và images |

---

# IV. Docker Compose - Quản lý nhiều container dễ dàng hơn
1. Docker Compose là gì
- là công cụ để định nghĩa và chạy nhiều container cùng lúc bằng file `docker-compose.yml`
- Ví dụ dành cho những dự án cần:
  * 1 container là Python script
  * 1 container là PostgreSQL
  * 1 container là Apache Airflow
   
2. Cấu trúc cơ bản của `docker-compose.yml`
- Đây file `docker-compose.yml` dùng để chạy một ứng dụng web và CSDL PostgreSQL cùng lúc bằng Docker:
```
version: '3.8' # Chỉ định phiên bản cú pháp của Docker compose

services: # Danh sách các dịch vụ (containers) bạn muốn chạy
  app: # dịch vụ app - ứng dụng web
    build: . # dùng Dockerfile trong thư mục hiện tại để build image
    volumes:
      - .:/app # mount thư mục hiện tại vào trong container tại /app (đồng bộ mã nguồn)
    ports:
      - "8000:8000" # mở cổng: gán cổng 8080 ngoài máy (host) với cổng 8000 trong container
  db:
    image: postgres # dùng image chính thức từ Docker Hub
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
```
- Giải thích thêm:
* `build .`: docker sẽ tìm file `Dockerfile` trong thư mục hiện tại để tạo image
* `volumes: .:/app`: gắn thư mục code hiện tại vào `/app` trong container, tiện cho dev
* `ports: "8000:8000"`: truy cập ứng dụng tại `http://localhost:8000` trên máy tính thật

* `image: postgres`: dùng image PostgreSQL chính thức
* `environment:`: tạo `user`, mật khẩu `pass`, và database `mydb` khi khởi động lần đầu
* `ports: "5432:5432"`: truy cập database từ máy ngoài qua `localhost:5432`
* Có thể dùng công cụ như `DBeaver`, `pgAdmin` hoặc `psql` để kết nối DB.

* Tóm tắt kết nối giữa các dịch vụ: hai dịch vụ `app` và `db` nằm trong cùng một mạng Docker ảo nên `app` có thể kết nối với PostgreSQL bằng:
  ```
  host = db
  port = 5432
  username = user
  password = pass
  database = mydb
  ```
3. Lệnh dùng với Docker Compose:
- `docker-compose up`: khởi động các container
- `docker-compose down`: dừng và xóa các container
- `docker-compose build`: build lại images
- `docker-compose logs`: xem log của các container

---

# V. Thực hành: Phân tích dữ liệu bằng Python và PostgreSQL qua Docker
Mục tiêu:
- Tạo môi trường gồm:
  * JupyterLab dùng Python (chạy Pandas, SQLAlchemy)
  * PostgreSQL chứa dữ liệu
 - Phân tích dữ liệu bằng pandas sau khi truy vấn từ PostgreSQL

## 1. Câu trúc thư mục
```
my-data-analysis/
├── docker-compose.yml
├── jupyter/
│   └── requirements.txt
└── data/
```
![image](https://github.com/user-attachments/assets/4d7b2f07-90f2-483b-a19f-ed130cc3b26a)

## 2. File `requirements.txt` trong thư mục `jupyter/`:
```
pandas
sqlalchemy
psycopg2
jupyterlab
```
![image](https://github.com/user-attachments/assets/277b0a92-589d-4357-a4d9-f41913f1c325)

## 3. Tạo `Dockerfile` trong thư mục `jupyter/`:
```
FROM python:3.10

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"]
```
![image](https://github.com/user-attachments/assets/de42efa9-6160-4dc5-9fb4-276a590dffa2)

## 4. Tạo `docker-compose.yml`:
```
version: "3.8"

services:
  jupyter:
    build: ./jupyter
    volumes:
      - ./jupyter:/app
    ports:
      - "8888:8888"
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: analysis_db
      POSTGRES_USER: analyst
      POSTGRES_PASSWORD: analyst123
    ports:
      - "5432:5432"
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```
![image](https://github.com/user-attachments/assets/18d9d03a-2e55-48b2-b0ca-eef6c2ea32bf)

## 5. Chạy thử
```
docker-compose up --build
```
![image](https://github.com/user-attachments/assets/8077b2d4-900d-4266-b624-8eab14a3d174)

- Mở trình duyệt truy cập: `http://localhost:8888`
![image](https://github.com/user-attachments/assets/ea3eb8e5-ebc2-4103-b881-311f39a055e0)

- Tạo notebook --> dùng `sqlalchemy` + `pandas` kết nối database:
  ```
  from sqlalchemy import create_engine
  import pandas as pd
  
  engine = create_engine("postgresql://analyst:analyst123@db:5432/analysis_db")
  
  df = pd.read_sql("SELECT NOW();", engine)
  print(df)
  ```
  => kết quả hiển thị thời gian => kết nối thành công
![image](https://github.com/user-attachments/assets/f70ec911-e1aa-41aa-80d1-b2c3e885862d)

---

# VI. Docker Desktop
## 1. Docker Desktop là gì?
Docker Desktop là ứng dụng GUI (giao diện đồ họa) giúp bạn:
- Quản lý Docker dễ dàng trên Windows/Mac.
- Theo dõi các containers, images, volumes, networks.
- Cung cấp môi trường để chạy Docker CLI và Docker Compose.
- Hỗ trợ WSL2 (trên Windows) để chạy Linux container.

## 2. Cách sử dụng Docker Desktop hiệu quả
- Giao diện chính bao gồm:

| Thành phần           | Mô tả                                               |
| -------------------- | --------------------------------------------------- |
| **Containers**       | Danh sách container đang chạy hoặc đã chạy          |
| **Images**           | Tất cả các Docker image đã pull hoặc build          |
| **Volumes**          | Nơi lưu trữ dữ liệu của các container               |
| **Dev Environments** | Các môi trường code tích hợp (nâng cao)             |
| **Extensions**       | Các plugin hỗ trợ như MongoDB Compass, Redis UI,... |

- Terminal CLI đi kèm:
  * Docker Desktop cung cấp `Docker CLI` ngay trong Terminal hoặc PowerShell
  * Cũng hỗ trợ `Docker Compose` nếu cài đúng phiên bản.
 
## 3. Cách backup và quản lý dữ liệu cho Data Analyst/Data Engineer
### a. Dữ liệu nằm ở đâu?
- Dữ liệu tạm thời: dữ liệu trong container không có volume => sẽ mất khi xóa container.
- Dữ liệu cố định qua volume (khuyên dùng):
  ```
  services:
    db:
      image: postgres
      volumes:
        - db_data:/var/lib/postgresql/data
  volumes:
    db_data:
  ```
  Docker sẽ tự tạo volume `db_data` lưu dữ liệu.
### b. Case study: Từ `image` => `Container` => `Backup` => Tái sử dụng
#### Kéo `Docker image` từ `Docker Hub`
- Câu lệnh:
```
docker pull <image_name>:<tag>
```
- Ví dụ:
```
docker pull postgres:15 # image PostgreSQL version 15
docker pull jupyter/base-notebook:latest # image JupyterLab có Python
```
- Kiểm tra image đã tải: `docker images`
#### Tạo và chạy Container từ image:
```
docker run --name my_postgres \ # Đặt tên container
  -e POSTGRES_USER=analyst \ # biến môi trường (tài khoản, mật khẩu)
  -e POSTGRES_PASSWORD=analyst123 \ # biến môi trường (tài khoản, mật khẩu)
  -e POSTGRES_DB=analysis_db \ biến môi trường (tài khoản, mật khẩu)
  -v ~/docker_backups/pgdata:/var/lib/postgresql/data \ # mount thư mục để lưu trữ data bền vững
  -p 5432:5432 \ # image sẽ chạy
  -d postgres:15 # chạy dưới nền (detached mode)
```
- Kiểm tra container đang chạy: `docker ps`
- Dừng container đang chạy: `docker stop my_postgres`
- Khởi động lại: `docker start my_postgres`

#### Backup contain đúng cách:
- Cách 1: Backup Volume (toàn bộ thư mục PostgreSQL)
```
docker run --rm \
  -v pgdata:/volume \
  -v $(pwd):/backup \
  alpine tar -czf /backup/pg_backup.tar.gz -C /volume .
```
=> Backup nằm tại thư mục hiện tại (`.pg_backup.tar.gz`)
- Cách 2: Backup file SQL (chỉ dữ liệu database):
```
docker exec my_postgres \
  pg_dump -U analyst -d analysis_db > backup.sql
```
#### Tái sử dụng dữ liệu từ thư mục backup
- Giả sử bạn đã có thư mục backup `~/docker_backups//pgdata`
- Bạn chỉ cần chạy container mới và mount lại volume đó:
```
docker run --name my_pg_restore \
  -e POSTGRES_USER=analyst \
  -e POSTGRES_PASSWORD=analyst123 \
  -e POSTGRES_DB=analysis_db \
  -v ~/docker_backups/pgdata:/var/lib/postgresql/data \
  -p 5433:5432 \
  -d postgres:15
```
#### Tổng kết cấu trúc folder backup:
```
~/docker_backups/
├── pgdata/               ← Dữ liệu volume PostgreSQL (toàn bộ DB)
├── pg_backup.tar.gz      ← File nén toàn bộ volume
├── backup.sql            ← Dump SQL thủ công
```
#### Có thể áp dụng tương tự cho:
| Dịch vụ        | Mount volume để backup                     |
| -------------- | ------------------------------------------ |
| **JupyterLab** | Mount `~/jupyter_work` để giữ notebook     |
| **Airflow**    | Mount `~/airflow/dags` và `~/airflow/logs` |
| **Kafka**      | Mount `~/kafka_data` cho dữ liệu stream    |

## 4. Những lưu ý khi dùng Docker Desktop
| Chủ đề            | Lưu ý                                                                                    |
| ----------------- | ---------------------------------------------------------------------------------------- |
| **Ổ đĩa lưu trữ** | Docker Desktop lưu image, volume mặc định ở ổ C. Bạn nên di chuyển sang ổ D nếu C đầy.   |
| **CPU/RAM**       | Có thể giới hạn Docker dùng bao nhiêu CPU/RAM trong **Settings → Resources**             |
| **Quyền admin**   | Một số container cần quyền admin để mount ổ đĩa hoặc chạy cổng đặc biệt (ví dụ: 80, 443) |
| **Tường lửa**     | Nếu bị lỗi không kết nối được DB giữa container với host, hãy tắt firewall hoặc mở cổng  |

## 5. Docker Desktop trong thực tế - Với vai trò Data Analyst/ Data Engineer
| Tình huống                | Mô tả ngắn                     | Docker hỗ trợ gì?                 |
| ------------------------- | ------------------------------ | --------------------------------- |
| Phân tích dữ liệu CSV lớn | Dùng JupyterLab/Pandas         | Dễ setup Jupyter + mount folder   |
| Kết nối PostgreSQL        | Lưu database project phân tích | Docker Postgres + volume          |
| Chạy pipeline Airflow     | Lập lịch ETL                   | Docker Compose với nhiều services |
| Chạy Spark local          | Test PySpark                   | Cài Spark trên container          |
| Quản lý Kafka             | Streaming Data                 | Dùng image Kafka/Zookeeper rất dễ |

---
Thanks for learning!
