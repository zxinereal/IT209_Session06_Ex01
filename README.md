# Bài 1 — FHS và phân quyền File/Folder nâng cao

## Mục tiêu

Triển khai thư mục ứng dụng web tại `/var/www/my-app`, gồm thư mục static công khai `public` và thư mục nhật ký `logs`, với quyền truy cập theo yêu cầu.

> Các lệnh dưới đây được thực hiện trên Ubuntu/Linux với tài khoản thường có quyền `sudo`. Thay `student` bằng tên tài khoản non-root thực tế (có thể dùng `$USER` khi chạy trực tiếp trong terminal).

## Nhật ký lệnh thực hiện

```bash
# 1. Tạo cấu trúc thư mục theo FHS (/var dành cho dữ liệu biến đổi)
sudo mkdir -p /var/www/my-app/public
sudo mkdir -p /var/www/my-app/logs

# 2. Đặt owner là tài khoản non-root và group là nhóm dịch vụ web
sudo chown -R student:www-data /var/www/my-app

# 3. Đặt quyền bằng octal
sudo chmod 750 /var/www/my-app/public
sudo chmod 770 /var/www/my-app/logs

# 4. Kiểm tra
ls -la /var/www/my-app
```

Ví dụ dùng cú pháp symbolic tương đương:

```bash
sudo chmod u=rwx,g=rx,o= /var/www/my-app/public
sudo chmod u=rwx,g=rwx,o= /var/www/my-app/logs
```

## Giải thích quyền

| Thư mục | Octal | Symbolic | Ý nghĩa |
| --- | --- | --- | --- |
| `public` | `750` | `u=rwx,g=rx,o=` | Owner đọc/ghi/truy cập; group đọc và truy cập; others không có quyền. |
| `logs` | `770` | `u=rwx,g=rwx,o=` | Owner và group có toàn quyền; others không có quyền. |

Với thư mục, quyền `x` cho phép đi vào/tham chiếu bên trong thư mục; vì vậy group của `public` cần `r-x`, không chỉ `r--`.

## Kết quả kiểm tra

Kết quả mẫu sau khi chạy lệnh (tên owner có thể khác theo máy):

```text
$ ls -la /var/www/my-app
total 16
drwxr-xr-x 4 student www-data 4096 Oct  5 10:00 .
drwxr-xr-x 3 root    root     4096 Oct  5 10:00 ..
drwxrwx--- 2 student www-data 4096 Oct  5 10:00 logs
drwxr-x--- 2 student www-data 4096 Oct  5 10:00 public
```

Hai thư mục cần đạt lần lượt `drwxr-x---` (`750`) và `drwxrwx---` (`770`), với owner non-root và group `www-data`.
