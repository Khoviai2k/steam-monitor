# Steam Metadata Repository (`steam-monitor`)

 Kho lưu trữ file metadata `.toml` (Pattern, ABI và IPC) dành cho DLL của **TramGame** (`OpenSteamTool`).

## Cấu trúc thư mục (nhánh `main`)

```text
pattern/
  steamclient/<sha256_cua_steamclient64.dll>.toml
  steamui/<sha256_cua_steamui.dll>.toml
ipc/
  steamclient/<sha256_cua_steamclient64.dll>.toml
```

## URL Template được DLL sử dụng

DLL tự động tải metadata theo SHA-256 của `steamclient64.dll` và `steamui.dll` qua 2 nguồn (GitHub Raw chính + jsDelivr CDN dự phòng):

- **Primary (GitHub Raw):**
  `https://raw.githubusercontent.com/Khoviai2k/steam-monitor/main/{channel}/{component}/{sha256}.toml`
- **Fallback (jsDelivr CDN):**
  `https://cdn.jsdelivr.net/gh/Khoviai2k/steam-monitor@main/{channel}/{component}/{sha256}.toml`

> **Lưu ý:** Repo trên GitHub cần đặt ở chế độ **Public** để DLL trên máy người dùng tải trực tiếp qua HTTPS không cần token.

## Cập nhật khi Steam có bản update mới

Chạy script tự động từ `TramGame_v2`:

```powershell
cd E:\TramGame_v2\desktop\dll\steam-monitor-generator
.\publish_to_repo.ps1
```

Sau đó push lên GitHub:

```powershell
cd E:\TramGame_v2\desktop\dll\steam-monitor
git add .
git commit -m "chore: add metadata for new Steam update"
git push -u origin main
```

