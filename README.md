# Bài 5: Docker Compose & File .env

## Mô tả
Đóng gói EX4 thành `docker-compose.yml`:
- Service `database`: `postgres:15-alpine`, dữ liệu lưu ở named volume `pgdata`, có healthcheck.
- Service `backend`: kết nối DB qua hostname `database`, publish port `${PORT}` -> 8080, chỉ khởi động sau khi DB healthy (`depends_on`).
- Khai báo `volumes` và `networks` (`backend-net`) ở cấp root.
- Thông tin nhạy cảm nằm trong `.env` (được `.gitignore` bỏ qua); `.env.example` là file mẫu.

## Cách chạy
```bash
cp .env.example .env      # sửa lại giá trị (đặc biệt DB_PASSWORD)
docker compose up -d      # tự tạo network, volume, chạy 2 service
docker compose ps         # cả 2 service ở trạng thái running/healthy
docker compose logs -f    # xem log (Ctrl+C để thoát)
docker compose down       # dừng và xóa container, network (giữ volume)
```

Xóa luôn dữ liệu: `docker compose down -v`.

## Kiểm tra thêm
```bash
docker compose config                                   # kiểm tra biến .env đã được thay vào
docker compose exec backend ping -c 2 database          # DNS nội bộ
curl localhost:8080                                     # backend phản hồi
docker volume ls | grep pgdata
```

## Kết quả mong đợi
`docker compose up -d` tự tạo network `<thư-mục>_backend-net`, volume `<thư-mục>_pgdata` và đưa cả `database` lẫn `backend` lên trạng thái Running.

## Ghi chú
Service `backend` hiện dùng `python:3.12-alpine` chạy `http.server` làm placeholder. Khi có Dockerfile thật, thay `image` + `command` bằng `build: .`.

## Kết quả chạy
_(Dán output terminal hoặc ảnh chụp vào đây)_
