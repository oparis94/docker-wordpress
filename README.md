1. Tạo thư mục trên máy host để lưu trữ database: "database"
2. Tạo thư mục trên máy host để lưu trữ src code của wordpress: "html"
3. Tạo thư mục trên máy host để lưu trữ file tùy chỉnh cấu hình web server nginx: "nginx-custom-config"
4. Bên trong thư mục "nginx-custom-config" tạo file tùy chỉnh cấu hình web server nginx: "nginx.conf". Copy và paste nội dung bên dưới vào file "nginx.conf":

        server {
            listen 80;
            listen [::]:80;
            access_log off;
            root /var/www/html;     # Đây là thư mục chứa src code của wordpress
            index index.php;
            server_name example.com;
            server_tokens off;
            location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to displaying a 404.
            try_files $uri $uri/ /index.php?$args;
            }
            # pass the PHP scripts to FastCGI server listening on wordpress:9000
            location ~ \.php$ {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            # Khi gặp file php thì nginx sẽ dùng service "wordpress-php-service" lắng nghe ở post 9000 để render code (service này sẽ tạo trong file docker-compose.yml). Ở đây có thể thay bằng tên container nếu có định nghĩa container_name trong docker-compose.yml
            fastcgi_pass wordpress-php-service:9000;    
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param SCRIPT_NAME $fastcgi_script_name;
            }
        }
5. Tạo file docker-compose.yml nằm đồng cấp với các thư mục đã tạo ở bước 1, 2, 3
6. Chạy lệnh docker-compse up -d
7. Truy cập địa chỉ web theo port định nghĩa trong file docker-compose để tiến hành cài đặt wordpress.