# Dokumentasi: Menjalankan Jenkins dengan Nginx sebagai Reverse Proxy di Docker

## 1. **Persiapan**

Pastikan Anda memiliki **Docker** dan **Docker Compose** terinstal di sistem Anda.

## 2. **Membuat Jaringan Docker**

Buat jaringan khusus untuk Jenkins dan Nginx agar dapat saling terhubung.

```bash
docker network create jenkins
```

## 3. **Menjalankan Jenkins di Port 49000**

Jalankan container Jenkins dengan konfigurasi yang diperlukan:

```bash
docker run \
  --name jenkins-blueocean \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2375 \
  --env DOCKER_TLS_CERTDIR="" \
  --publish 49000:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume "$HOME/belajar_implementasi_cicd":/var/jenkins_projects \
  --restart=on-failure \
  --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" \
  myjenkins-blueocean:2.492.1-1
```

## 4. **Konfigurasi Nginx sebagai Reverse Proxy**

Buat direktori konfigurasi Nginx:

```bash
mkdir -p ~/belajar_implementasi_cicd/submission-cicd-pipeline/simple-python-pyinstaller-app/nginx-jenkins/conf.d
```

Buat file **jenkins.conf** di dalam direktori tersebut:

```bash
nano ~/belajar_implementasi_cicd/submission-cicd-pipeline/simple-python-pyinstaller-app/nginx-jenkins/conf.d/jenkins.conf
```

Tambahkan konfigurasi berikut:

```nginx
server {
    listen 9000;
    server_name localhost;

    location / {
        proxy_pass http://jenkins-blueocean:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;

        proxy_read_timeout 90s;
        proxy_max_temp_file_size 0;
        client_max_body_size 100m;
    }
}
```

## 5. **Menjalankan Nginx**

Jalankan Nginx dengan perintah berikut:

```bash
docker run --name nginx-jenkins \
  -d \
  -p 9000:9000 \
  --network jenkins \
  -v ~/belajar_implementasi_cicd/submission-cicd-pipeline/simple-python-pyinstaller-app/nginx-jenkins/conf.d:/etc/nginx/conf.d \
  nginx
```

## 6. **Verifikasi Koneksi**

Cek apakah Jenkins dan Nginx berjalan dengan:

```bash
docker ps
```

Cek apakah Jenkins dapat ditemukan dalam jaringan Docker:

```bash
docker network inspect jenkins
```

Jika **jenkins-blueocean** tidak ditemukan dalam jaringan, tambahkan dengan:

```bash
docker network connect jenkins jenkins-blueocean
```

## 7. **Mengatasi Error 502 Bad Gateway**

Jika muncul error **502 Bad Gateway**, coba lakukan langkah berikut:

- **Cek log Nginx:**
  ```bash
  docker logs nginx-jenkins
  ```
- **Pastikan Jenkins berjalan:**
  ```bash
  docker ps | grep jenkins-blueocean
  ```
- **Gunakan IP Jenkins jika perlu:**
  ```bash
  docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' jenkins-blueocean
  ```
  Lalu edit `jenkins.conf` untuk menggunakan IP tersebut di `proxy_pass`:
  ```nginx
  proxy_pass http://192.168.1.100:8080;
  ```
- **Restart Nginx:**
  ```bash
  docker restart nginx-jenkins
  ```

## 8. **Mengakses Jenkins**

Buka browser dan akses Jenkins melalui:

```
http://localhost:9000
```

---

## **Selesai! 🚀**

Jika masih mengalami kendala, jalankan perintah berikut untuk melihat log error:

```bash
docker logs nginx-jenkins
```

Bagikan error yang muncul jika masih mengalami masalah. Semoga sukses! 🎯

