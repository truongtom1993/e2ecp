# Hướng Dẫn Chạy Dự Án e2ecp Ở Local

Hướng dẫn này sẽ giúp bạn thiết lập và chạy dự án e2ecp trên máy tính cá nhân.

## Yêu Cầu Hệ Thống

Trước khi bắt đầu, bạn cần cài đặt các công cụ sau:

### 1. Node.js
- **Phiên bản yêu cầu**: >= v24
- **Tải xuống**: https://nodejs.org/
- **Kiểm tra phiên bản**:
  ```bash
  node --version
  ```

### 2. Go
- **Phiên bản yêu cầu**: >= 1.25
- **Tải xuống**: https://go.dev/dl/
- **Kiểm tra phiên bản**:
  ```bash
  go version
  ```

### 3. Git
- **Tải xuống**: https://git-scm.com/
- **Kiểm tra**:
  ```bash
  git --version
  ```

## Các Bước Cài Đặt

### Bước 1: Clone Repository

```bash
git clone https://github.com/schollz/e2ecp.git
cd e2ecp
```

### Bước 2: Build Dự Án

Có 2 cách để build dự án:

#### Cách 1: Sử dụng Make (Khuyến nghị)

```bash
make
```

Lệnh này sẽ tự động:
1. Cài đặt dependencies cho web frontend (`cd web && npm install`)
2. Build web frontend (`cd web && npm run build`)
3. Build Go binary

#### Cách 2: Build Từng Bước

**Bước 2.1: Cài đặt và build web frontend**

```bash
cd web
npm install
npm run build
cd ..
```

**Bước 2.2: Build Go binary**

```bash
go build -o e2ecp .
```

### Bước 3: Chạy Relay Server

Sau khi build thành công, bạn có thể chạy relay server:

```bash
./e2ecp serve --port 8080
```

Server sẽ chạy tại: http://localhost:8080

#### Các Tùy Chọn Khi Chạy Server

```bash
./e2ecp serve \
  --port 8080 \                    # Port để server lắng nghe (mặc định: 3001)
  --max-rooms 50 \                 # Số phòng tối đa cùng lúc (mặc định: 10)
  --max-rooms-per-ip 5 \           # Số phòng tối đa mỗi IP (mặc định: 2)
  --log-level debug                # Mức độ log: debug, info, warn, error
```

## Chạy Development Mode

### Frontend Development

Nếu bạn muốn phát triển frontend với hot-reload:

```bash
cd web
npm run dev
```

Frontend dev server sẽ chạy tại: http://localhost:5173

### Backend Development

Để tự động rebuild khi có thay đổi trong code Go, bạn có thể sử dụng Air:

1. Cài đặt Air:
   ```bash
   go install github.com/cosmtrek/air@latest
   ```

2. Chạy với Air (dự án đã có file `.air.toml`):
   ```bash
   air
   ```

## Sử Dụng Command-Line Tool

### Gửi File

```bash
./e2ecp send <đường-dẫn-file>
```

Ví dụ:
```bash
./e2ecp send document.pdf
```

### Nhận File

```bash
./e2ecp receive
```

### Kết Nối Tới Server Tùy Chỉnh

Nếu bạn đang chạy relay server riêng:

```bash
./e2ecp send --relay http://localhost:8080 myfile.txt
./e2ecp receive --relay http://localhost:8080
```

## Chạy Bằng Docker (Tùy Chọn)

### Cách 1: Build Từ Binary Đã Compile

```bash
# Build binary trước
make build

# Build Docker image
docker build -t e2ecp-relay .

# Chạy container
docker run -p 3001:3001 e2ecp-relay
```

### Cách 2: Build Toàn Bộ Từ Source

```bash
# Build và chạy
docker build -f Dockerfile.build -t e2ecp-relay .
docker run -p 8080:8080 e2ecp-relay --port 8080
```

## Testing

### Chạy Unit Tests

```bash
go test -v ./...
```

### Chạy End-to-End Tests

```bash
cd tests
./run-tests.sh
```

Xem thêm chi tiết tại [TESTING.md](TESTING.md)

## Cấu Trúc Dự Án

```
e2ecp/
├── main.go              # Entry point của ứng dụng Go
├── src/                 # Source code Go
├── web/                 # Web frontend
│   ├── src/            # Source code React
│   ├── package.json    # Dependencies frontend
│   └── vite.config.js  # Cấu hình Vite
├── tests/              # E2E tests
├── Makefile            # Build automation
└── go.mod              # Go dependencies
```

## Troubleshooting

### Lỗi "Node version not supported"

Đảm bảo bạn đang sử dụng Node.js >= v24:

```bash
node --version
```

Nếu phiên bản thấp hơn, tải phiên bản mới nhất từ https://nodejs.org/

### Lỗi "Go version not supported"

Đảm bảo Go >= 1.25:

```bash
go version
```

### Lỗi "command not found: make"

**Trên Ubuntu/Debian:**
```bash
sudo apt-get install build-essential
```

**Trên macOS:**
```bash
xcode-select --install
```

**Trên Windows:**
- Sử dụng WSL (Windows Subsystem for Linux)
- Hoặc cài đặt Make qua Chocolatey: `choco install make`

### Port đã được sử dụng

Nếu port 8080 đã được sử dụng, thay đổi sang port khác:

```bash
./e2ecp serve --port 9000
```

### Build frontend thất bại

Thử xóa node_modules và cài lại:

```bash
cd web
rm -rf node_modules package-lock.json
npm install
npm run build
```

## Làm Sạch Build

Để xóa các file đã build:

```bash
make clean
```

Lệnh này sẽ xóa:
- Binary `e2ecp`
- Thư mục `web/dist`

## Đóng Góp

Nếu bạn muốn đóng góp cho dự án, vui lòng:
1. Fork repository
2. Tạo branch mới cho feature của bạn
3. Commit và push
4. Tạo Pull Request

## Hỗ Trợ

- **Issues**: https://github.com/schollz/e2ecp/issues
- **Documentation**: https://e2ecp.com
- **README**: [README.md](README.md)

## License

MIT License - Xem [LICENSE](LICENSE) để biết thêm chi tiết.
