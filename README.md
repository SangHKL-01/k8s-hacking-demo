📖 Giới thiệu
Repository này chứa các tệp cấu hình (Manifest YAML) được sử dụng để xây dựng môi trường thực nghiệm (Lab) cho đồ án tốt nghiệp. Mục tiêu của Lab là mô phỏng một kịch bản tấn công thực tế vào cụm Kubernetes (K8s) xuất phát từ một Pod bị xâm nhập, sau đó thực hiện leo thang đặc quyền và chiếm quyền kiểm soát Node.

Môi trường này bao gồm:

Các ứng dụng tồn tại lỗ hổng (Vulnerable Apps).

Cấu hình RBAC lỏng lẻo (Misconfigurations).

Các tài nguyên giả lập tấn công (Malicious Pods).

⚠️ Lưu ý quan trọng
Mục đích giáo dục: Repository này chỉ phục vụ cho mục đích nghiên cứu và học tập. Tuyệt đối không sử dụng các cấu hình này trên môi trường Production.

Hạ tầng: Repo này không bao gồm hướng dẫn cài đặt cụm Kubernetes (Cluster Installation). Giả định rằng bạn đã có sẵn một cụm K8s hoạt động (Minikube, Kind, Kubeadm, v.v.) và đã cài đặt Ingress Controller (ví dụ: Nginx Ingress).

📂 Cấu trúc thư mục
Bash

.
├── apps/
│   ├── shop-web.yaml          # Ứng dụng Web mô phỏng (Spring Cloud Gateway - CVE-2022-22947)
│   ├── database.yaml          # MariaDB/MySQL Service (Target của Lateral Movement)
│   └── secrets.yaml           # Kubernetes Secrets chứa thông tin DB (bị mã hóa Base64)
├── rbac/
│   ├── service-account.yaml   # Service Account với quyền hạn quá mức (Privilege Escalation vector)
│   └── role-binding.yaml      # Gán quyền cho Service Account
├── network/
│   └── ingress.yaml           # Cấu hình Ingress để public ứng dụng ra ngoài
└── attack/
    └── malicious-pod.yaml     # Pod độc hại dùng để thoát Container (Node Escape)
🚀 Hướng dẫn triển khai (Deployment)
Để dựng môi trường Lab, hãy thực hiện apply các file YAML theo thứ tự sau:

1. Tạo Namespace và Database
Tạo môi trường cô lập cho bài lab (ví dụ namespace: k8s-hacking).

Bash

kubectl create namespace k8s-hacking
kubectl apply -f apps/secrets.yaml -n k8s-hacking
kubectl apply -f apps/database.yaml -n k8s-hacking
2. Triển khai Ứng dụng mục tiêu & RBAC lỗi
Triển khai ứng dụng Web có lỗ hổng và gán cho nó một Service Account có quyền hạn quá cao (như quyền create pods, get secrets).

Bash

kubectl apply -f rbac/service-account.yaml -n k8s-hacking
kubectl apply -f rbac/role-binding.yaml -n k8s-hacking
kubectl apply -f apps/shop-web.yaml -n k8s-hacking
3. Cấu hình Ingress
Mở đường dẫn truy cập từ bên ngoài vào ứng dụng.

Bash

kubectl apply -f network/ingress.yaml -n k8s-hacking
4. Kích hoạt tấn công (Simulate Attack)
Sau khi đã chiếm được quyền điều khiển (Initial Access) và có quyền tạo Pod, Hacker sẽ sử dụng file này để tạo một Pod đặc quyền nhằm thoát ra máy chủ vật lý (Node Escape).

Lưu ý: File này sử dụng cấu hình privileged: true, hostNetwork: true và mount / của host.

Bash

kubectl apply -f attack/malicious-pod.yaml -n k8s-hacking
🛠️ Kịch bản mô phỏng (Attack Chain)
Các file cấu hình trong repo này hỗ trợ mô phỏng chuỗi tấn công sau:


Initial Access: Khai thác lỗ hổng RCE trên Spring Cloud Gateway (apps/shop-web.yaml).


Credential Access: Lạm dụng quyền RBAC sai lầm (rbac/) để đọc Secrets (apps/secrets.yaml).



Lateral Movement: Sử dụng thông tin trộm được để truy cập vào Database nội bộ (apps/database.yaml).


Privilege Escalation & Escape: Tạo một Malicious Pod (attack/malicious-pod.yaml) để thực hiện kỹ thuật Container Escape, chiếm quyền root của Node.


🤝 Đóng góp
Mọi đóng góp hoặc phát hiện vấn đề vui lòng tạo Issue hoặc Pull Request.
