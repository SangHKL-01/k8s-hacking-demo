# Kubernetes Attack Simulation Lab 🛡️🏴‍☠️

> **Đề tài:** Nghiên cứu xây dựng kịch bản kiểm thử Kubernetes với mô phỏng tấn công từ Pod.
> **Học viện:** Học viện Kỹ thuật Mật mã (KMA)

## 📖 Giới thiệu
Repository này chứa các tệp cấu hình (Manifest YAML) được sử dụng để xây dựng môi trường thực nghiệm (Lab). Mục tiêu của Lab là mô phỏng một kịch bản tấn công thực tế vào cụm Kubernetes (K8s) xuất phát từ một Pod bị xâm nhập, sau đó thực hiện leo thang đặc quyền và chiếm quyền kiểm soát Node.

### Cấu trúc mô phỏng:
* **Gateway:** Ứng dụng Spring Cloud Gateway (Mô phỏng lỗ hổng RCE).
* **Shop-Web:** Ứng dụng vệ tinh và cơ sở dữ liệu.
* **Exploit:** Các script và Pod độc hại dùng để tấn công.
* **RBAC:** Cấu hình phân quyền (có lỗ hổng để khai thác).

## ⚠️ Yêu cầu tiền quyết (Prerequisites)
Repo này **không** bao gồm hướng dẫn cài đặt cụm Kubernetes. Để chạy được Lab này, bạn cần có:
1.  Một cụm Kubernetes đang hoạt động (Minikube, Kind, hoặc Kubeadm).
2.  Đã cài đặt **Ingress Controller** (VD: Nginx Ingress).
3.  Công cụ `kubectl` đã được cấu hình kết nối tới cụm.

## 📂 Cấu trúc thư mục

```text
k8s-hacking-demo/
├── Ingress_RBAC/
│   ├── ingress.yaml           # Cấu hình Ingress public ứng dụng
│   └── rbac.yaml              # Cấu hình ServiceAccount và Role (Lỗ hổng leo thang)
├── exploit/
│   ├── curl_apiserver         # Script/Lệnh tương tác API Server
│   └── pod_escape.yaml        # Pod độc hại dùng để thoát Container (Node Escape)
├── gateway/
│   ├── configmap-getway.yaml  # ConfigMap cho Gateway
│   └── service_spring_getway.yaml # Deployment & Service cho Spring Gateway
└── shop-web/
    ├── cridentcal.txt         # Thông tin credentials (dùng cho tham khảo/lab)
    ├── shop_web_srv           # Service cho Shop Web
    └── shop_wep.yaml          # Deployment cho Shop Web
