# Data Directory

Dữ liệu được quản lý bằng **DVC** (Data Version Control) và lưu trữ trên Google Drive.

## Cấu trúc thư mục

```
data/
├── raw/                                  # Dataset gốc (DVC tracked)
│   ├── dataset_CIDIS_guided_x8x16/       # Guided Super Resolution dataset (x8, x16)
│   │   ├── thermal/                      # Ảnh nhiệt (infrared)
│   │   │   ├── train/
│   │   │   ├── val/
│   │   │   └── test/
│   │   └── visible/                      # Ảnh RGB thường
│   │       ├── train/
│   │       ├── val/
│   │       └── test/
│   └── dataset_CIDIS_sisr_x8/            # Single Image Super Resolution dataset (x8)
│       └── thermal/
│           ├── train/
│           ├── val/
│           └── test/
├── processed/                            # Dữ liệu đã tiền xử lý
├── features/                             # Feature store cho training
├── raw.dvc                               # DVC tracking file
└── README.md                             # File này
```

## 🔽 Cách tải dữ liệu

### Bước 1: Cài DVC
```bash
pip install dvc dvc-gdrive
```

### Bước 2: Thêm DVC remote (chỉ cần làm 1 lần nếu chưa có)

```bash
# FOLDER_ID lấy từ URL Google Drive:
# https://drive.google.com/drive/folders/FOLDER_ID (FOLDER_ID ở cuối trước dấu //)
dvc remote add -d gdrive gdrive://FOLDER_ID
```

### Bước 3: Cấu hình OAuth credentials

Cần file `client_secret_*.json` (liên hệ team để nhận). Mở file → copy `client_id` và `client_secret`, sau đó chạy:

```bash
# Cách 1: Dùng OAuth credentials (phổ biến)
dvc remote modify --local gdrive gdrive_client_id "CLIENT_ID_TRONG_FILE_JSON"
dvc remote modify --local gdrive gdrive_client_secret "CLIENT_SECRET_TRONG_FILE_JSON"

# Cách 2: Dùng Service Account
dvc remote modify --local gdrive gdrive_use_service_account true
dvc remote modify --local gdrive gdrive_service_account_json_file_path "path/to/service-account.json"
```

### Bước 3: Pull dữ liệu
```bash
dvc pull                  # Tải toàn bộ data
dvc pull data/raw.dvc     # Chỉ tải raw data
```

Trình duyệt sẽ mở để đăng nhập Google (lần đầu). Cần có quyền truy cập folder Drive của project.

## ⚠️ Lưu ý
- **KHÔNG** commit file `client_secret*.json` hoặc `service_account*.json` lên Git
- File `.dvc/config.local` chứa credentials cá nhân, đã nằm trong `.gitignore`
- Khi thay đổi data → chạy `dvc add data/<folder>` → `dvc push` → `git add .` → `git commit`
