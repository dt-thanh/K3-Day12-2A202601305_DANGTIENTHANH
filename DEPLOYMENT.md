# Thông Tin Deploy — Checkpoint 5

> Điền file này sau khi deploy xong. `pytest tests/test_cp5.py` đọc file này
> để tìm địa chỉ service của bạn và gọi thử.
>
> **Chỉ ghi TÊN biến môi trường, tuyệt đối không dán giá trị API key vào đây.**
> Repo này công khai — dán khóa vào là mất khóa.

## Thông Tin Học Viên

| Mục | Nội dung |
|-----|----------|
| Họ và tên | Đặng Tiến Thành |
| Mã học viên | 2A202601305 |
| Repo | https://github.com/dt-thanh/K3-Day12-2A202601305_DANGTIENTHANH |

## Service

| Mục | Nội dung |
|-----|----------|
| Public URL | day12-agent-production-29a9.up.railway.app |
| Platform | Railway |
| Ngày deploy | 2026-08-10 |

## Biến Môi Trường Đã Set Trên Cloud

Ghi tên biến và **nguồn giá trị**, không ghi giá trị:

| Biến | Đã set | Ghi chú |
|------|--------|---------|
| `PORT` | ✅ | platform tự gán |
| `AGENT_API_KEY` | ✅ | đặt trong dashboard, không nằm trong repo |
| `REDIS_URL` | ✅ | (điền: Redis add-on của platform / Upstash / ...) |
| `RATE_LIMIT_PER_MINUTE` | ✅ | 10 |
| `MONTHLY_BUDGET_USD` | ✅ | 10.0 |
| `LOG_LEVEL` | ✅ | INFO |

## Lệnh Kiểm Tra

URL="https://day12-agent-production-29a9.up.railway.app"

```bash
# 1. Liveness — mong đợi 200 {"status":"ok"}
curl -i <URL>/health

# 2. Readiness — mong đợi 200 {"status":"ready"} (đã nối được Redis)
curl -i <URL>/ready

# 3. Không có API key — mong đợi 401
curl -i -X POST <URL>/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'

# 4. Có API key — mong đợi 200 kèm câu trả lời
curl -i -X POST <URL>/ask \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $AGENT_API_KEY" \
  -H "X-User-Id: sv-test" \
  -d '{"question":"Deploy là gì?"}'

# 5. Rate limit — gọi 15 lần, những lần cuối phải trả 429
for i in $(seq 1 15); do
  curl -s -o /dev/null -w "%{http_code} " -X POST <URL>/ask \
    -H "Content-Type: application/json" \
    -H "X-API-Key: $AGENT_API_KEY" \
    -H "X-User-Id: sv-test" \
    -d '{"question":"test"}'
done; echo
```

## Kết Quả Chạy Thật

Dán output của các lệnh trên vào đây:

```
(.venv) thanhdt@thanhdt-Dell-G15-5511:~/Documents/AI IN VINUNI/K3-Day12-2A202601305_DANGTIENTHANH$ URL="https://day12-agent-production-29a9.up.railway.app"

curl -i "$URL/health"

curl -i "$URL/ready"

curl -i -X POST "$URL/ask" \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
HTTP/2 200 
content-type: application/json
date: Mon, 10 Aug 2026 09:26:10 GMT
server: railway-hikari
x-railway-request-id: 1B6I6PnFTMqXOXpn-_9nXA
content-length: 57
x-hikari-trace: sin1.nzn2
x-railway-edge: sin1

{"status":"ok","service":"day12-agent","version":"1.0.0"}HTTP/2 500 
content-type: text/plain; charset=utf-8
date: Mon, 10 Aug 2026 09:26:10 GMT
server: railway-hikari
x-railway-request-id: wCOBrwmoRkqySguA9o6EoQ
content-length: 21
x-hikari-trace: sin1.hs0s
x-railway-edge: sin1

Internal Server ErrorHTTP/2 500 
content-type: text/plain; charset=utf-8
date: Mon, 10 Aug 2026 09:26:11 GMT
server: railway-hikari
x-railway-request-id: 2uyv3G67RXCl9YZtlt7tkg
content-length: 21
x-hikari-trace: sin1.nzn2
x-railway-edge: sin1
```

## Ảnh Chụp Màn Hình

Đặt ảnh trong thư mục `screenshots/`:

- `screenshots/dashboard.png` — trang quản lý service trên platform
- `screenshots/health.png` — kết quả gọi `/health` từ trình duyệt hoặc curl

---

## Nếu Dùng Phương Án Dự Phòng

Không đăng ký được tài khoản cloud? Vẫn nộp được bài, nhưng CP5 tối đa 60% điểm:

1. Đặt `LOCAL_FALLBACK=true` trong `.env`
2. Chạy `docker compose up -d` rồi kiểm tra `docker compose ps`
3. Chụp màn hình vào `screenshots/`
4. Chạy `pytest tests/test_cp5.py -v` — bộ test sẽ tự chuyển sang kiểm tra
   `http://localhost:8000`
5. Ghi rõ lý do không deploy được vào phần dưới đây:

```
(điền lý do nếu dùng phương án dự phòng, ngược lại xóa mục này)
```
