# Jenkins LTS mới (tương thích kubernetes-client-api 7.x, tránh lỗi "emulationMajor")
FROM jenkins/jenkins:2.479.3-lts-jdk17

# Cài Docker CLI (để job trong Jenkins có thể gọi docker qua /var/run/docker.sock)
USER root
RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends ca-certificates curl gnupg; \
    install -m 0755 -d /etc/apt/keyrings; \
    curl -fsSL https://download.docker.com/linux/debian/gpg \
      | gpg --dearmor -o /etc/apt/keyrings/docker.gpg; \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
      https://download.docker.com/linux/debian $(. /etc/os-release; echo $VERSION_CODENAME) stable" \
      > /etc/apt/sources.list.d/docker.list; \
    apt-get update; \
    apt-get install -y --no-install-recommends docker-ce-cli; \
    apt-get clean; rm -rf /var/lib/apt/lists/*

# Cài sẵn plugin Jenkins cần cho Kubernetes (plugin-cli sẽ tự chọn phiên bản tương thích)
# - kubernetes + client API + credentials
# - cloudbees-folder + matrix-auth để tránh lỗi phụ thuộc
RUN jenkins-plugin-cli --plugins \
  kubernetes \
  kubernetes-client-api \
  kubernetes-credentials \
  cloudbees-folder \
  matrix-auth

# Đảm bảo quyền thư mục dữ liệu (tránh "Permission denied copy_reference_file.log")
RUN chown -R jenkins:jenkins /var/jenkins_home

USER jenkins

# (Giữ entrypoint/cấu hình mặc định của image official)
# Expose cổng như tiêu chuẩn Jenkins
EXPOSE 8080 50000
