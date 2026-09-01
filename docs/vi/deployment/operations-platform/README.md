# Tổng quan nền tảng vận hành

[← ShimoDocs Suite Tài liệu triển khai](../README.md)

## Tổng quan chức năng

- **ShimoDocs Suite**: Dùng để quản lý quyền, khách thuê, người dùng, thương hiệu và các cấu hình AI liên quan đến ShimoDocs Suite.
- **Dịch vụ Hệ thống**: Dùng cho các nhiệm vụ vận hành và bảo trì chung như cấu hình toàn cục, quản lý cụm, xem nhật ký, kiểm tra tính năng, truy vấn sự cố, sửa tài liệu và **nâng cấp hệ thống**.

> **Lưu ý**: Chức năng cụ thể hiển thị phụ thuộc vào phiên bản triển khai hiện tại và các tính năng đã được bật.

## Đăng nhập vào Nền tảng Vận hành

Truy cập địa chỉ sau trong trình duyệt của bạn:
> **Yêu cầu Trình duyệt**: Vui lòng sử dụng Google Chrome phiên bản 111 trở lên để truy cập Nền tảng Vận hành. Khuyến nghị nâng cấp lên phiên bản ổn định mới nhất trước.

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

Nhập tài khoản quản trị và PASSWORD, sau đó nhấp vào "Đăng nhập."

## Làm quen với Trang chủ Nền tảng Vận hành

Sau khi đăng nhập, bạn có thể truy cập các chức năng quản lý tương ứng thông qua menu ở bên trái trang. Menu hiển thị phụ thuộc vào các sản phẩm và phiên bản đã được triển khai và cấp quyền trong môi trường hiện tại.

## Đặt lại Quản trị viên PASSWORD Khi quên mật khẩu

Nếu quản trị viên của Nền tảng Vận hành PASSWORD bị quên, bạn có thể đăng nhập vào nút triển khai và thực hiện lệnh sau để đặt lại.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

Ví dụ trên đặt lại PASSWORD sang `Aa1234567.`. Trong thực tế vận hành, vui lòng thay thế PASSWORD ở cuối lệnh bằng một PASSWORD mới đáp ứng yêu cầu bảo mật.

Sau khi hoàn tất việc đặt lại, quay lại trang đăng nhập, đăng nhập bằng PASSWORDmới, và xác nhận rằng menu có thể truy cập bình thường.
