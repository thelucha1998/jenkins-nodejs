1. Cài đặt sonarqube với docker
Tạo file: sonarqube.yaml

version: "3"
services:
  sonarqube:
    image: sonarqube:lts
    container_name: sonarqube
    ports:
      - 9000:9000
    networks:
      - mynetwork
    environment:
      - SONAR_FORCEAUTHENTICATION=false
networks:
  mynetwork:

Chạy lệnh: docker-compose -f sonarqube.yaml up -d

2. Truy cập Sonarqube web
http://<ip_addr>:9000
3. Cấu hình Sonarqube
- Tạo token: Administration -> Security -> Users -> Generate token
- Vào Jenkins -> Manage Jenkins -> Credential:
  Chọn Add Credential -> Secret Text, sau đó nhập token vừa tạo ở bước trên
4. Tích hợp Sonarqube với Jenkins
- Cài đặt Plugins: 
  Vào Manage Jenkins -> Plugins
  Tìm plugin Sonarqube & Sonarqube Scanner
- Cấu hình Sonarqube
  Vào Manage Jenkins -> System
  Tìm kiếm "SonarQube servers":
    * Tích chọn Environment variables
    * Trong SonarQube installations:
        +) Name: sonarqube-container
        +) Server URL: http://172.25.166.55:9000
        +) Server authentication token: chọn token vừa tạo ở trên
- Cấu hình Sonarqube Scanner:
  Cài đặt Scanner:
  sudo apt update && sudo apt install -y openjdk-17-jdk
  wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-5.0.1.3006-linux.zip
  sudo apt install unzip -y
  unzip sonar-scanner-5.0.1.3006-linux.zip
  sudo mv sonar-scanner-5.0.1.3006-linux /opt/sonar-scanner
  Cấu hình Scanner trong Jenkins:
    * Vào Manage Jenkins -> Tool
         +) Tìm SonarQube Scanner installations
         +) Add Sonarqube Scanner
         +) Bỏ chọn Install automatically
         +) Nhập Name: SonarQube-Scanner
         +) SONAR_RUNNER_HOME: /opt/sonar-scanner/bin
