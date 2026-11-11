# Steam Metadata Repository (`steam-monitor`)

 Kho lưu trữ file metadata `.toml` (Pattern, ABI và IPC) dành cho DLL của **TramGame** (`OpenSteamTool`).

## Cấu trúc thư mục (nhánh `main`)

```text
pattern/
  steamclient/<sha256_cua_steamclient64.dll>.toml
  steamui/<sha256_cua_steamui.dll>.toml
ipc/
  steamclient/<sha256_cua_steamclient64.dll>.toml
manifest.toml
manifest.sig
```

## URL Template được DLL sử dụng

DLL tự động tải metadata theo SHA-256 của `steamclient64.dll` và `steamui.dll` qua 2 nguồn (GitHub Raw chính + jsDelivr CDN dự phòng):

- **Primary (GitHub Raw):**
  `https://raw.githubusercontent.com/Khoviai2k/steam-monitor/main/{channel}/{component}/{sha256}.toml`
- **Fallback (jsDelivr CDN):**
  `https://cdn.jsdelivr.net/gh/Khoviai2k/steam-monitor@main/{channel}/{component}/{sha256}.toml`

> **Lưu ý:** Repo trên GitHub cần đặt ở chế độ **Public** để DLL trên máy người dùng tải trực tiếp qua HTTPS không cần token.

## Xác minh nguồn và nội dung

DLL mới kiểm chữ ký ECDSA P-256/SHA-256 của `manifest.toml` bằng khóa công
khai ghim trong DLL. `manifest.sig` là 64 byte nhị phân P1363; không chuyển
đổi xuống dòng. Manifest ràng buộc từng đường dẫn với hash nội dung và số
byte. SHA trong tên TOML chỉ xác định binary Steam, không thay chữ ký.

Cache `.tgmeta` lưu kèm manifest/chữ ký và được xác minh lại khi đọc.
Tệp tải thiếu, sai chữ ký, sai schema hoặc không khớp hash không ghi đè cache
tốt. Dữ liệu cũ đã ký cho đúng Steam có thể dùng offline. Đây là kiểm tra
metadata, không phải kiểm tra quyền sử dụng Lua.

## Cập nhật khi Steam có bản update mới

Chuẩn bị và duyệt bộ TOML riêng trước. Publisher không tự tạo pattern mới,
chỉ xác minh ba tệp đúng phiên bản Steam, giữ lịch sử và ký inventory.
Khóa ký thuộc tài khoản Windows phát hành, nằm trong CNG và không xuất vào
Git. Lệnh ký yêu cầu khóa sẵn có khớp khóa công khai ghim trong source DLL.

Chạy từ `TramGame_v2` sau khi đã đồng bộ các commit hiện có:

```powershell
cd E:\TramGame_v2\desktop\dll\steam-monitor-generator
.\publish_to_repo.ps1 -Revision 1 -CommitAndPush
```

Tăng revision khi inventory thay đổi; giữ nguyên revision chỉ khi toàn bộ
payload không đổi. Script dừng khi verify/ký/Git thất bại, từ chối thay đổi
đã stage sẵn và chỉ commit đúng các tệp phát hành. Không sao chép tệp rác
trong output hoặc tự đổi fixture kiểm thử. Kết quả xác minh tĩnh không thay
kiểm thử runtime của từng chức năng.

