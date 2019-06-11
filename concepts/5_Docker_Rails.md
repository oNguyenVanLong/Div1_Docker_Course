## Ứng dụng Docker trong framework Ruby On Rails
+ Source code

  + Một rails project thông thường thì chúng ta cần define những services như: app, db, worker, redis,...
  + Mọi người có thể tham khảo tại [source_code/rails/](https://github.com/longnv-0623/Div1_Docker_Course/tree/master/source_code/rails)

+ Để  speed up app thì chúng ta sẽ

  + Khởi động container `mysql`, `redis`, `worker` ngay từ đầu bằng việc chạy daemon:
    ``` bash
    docker-compose up -d mysql redis worker
    ```

  + Chạy rails app bằng lệnh sau:
    ```bash
    docker-compose run --rm --service-ports app
    ```
##### Note
  + Chúng ta có thể bỏ option `networks` trong file `docker-compose.yml` nếu chúng ta không cần config gì khác như `ip`, `subnet`,... (dùng luôn default network khi start container).
  + Nếu không có nhu cầu access vào database từ host machine thì cũng không cần bind `port` ra :smile: Cụ thể là ở trong service `mysql` chúng ta có thể bỏ options `ports`.
  + Một số file `docker-compose.yml` dùng `links` thay vì `depends_on` thì thay vì dùng short-hand syntax như:
    ```yaml
    web:
      links:
      - db
    ```
  + Chúng ta nên dùng `<tên service>:<service alias>` để dễ dàng cho việc maintain
    ```yaml
    web:
      links:
      - mysql:db
    ```
  + Khi làm việc với `cron jobs` chúng ta cần chú ý tới việc setting timezone cho container. Chúng ta có thể setting cho chúng thông qua `Dockerfile` hoặc biến env khi chạy container.
