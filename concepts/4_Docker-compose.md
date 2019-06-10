# 1. Giải pháp

Ứng dụng Docker trong dự án như thế nào ?

+ Giải pháp đơn giản
  + Dùng Dockerfile, cài đặt tất cả những môi trường cần thiết (mysql, redis, php,... ) lên một container duy nhất.

  + Rồi chạy project trên container đó.

+ Tuy nhiên
  + Dễ nhận thấy sự chồng chéo && khó quản lý trong container duy nhất đó.
  + Nếu như bạn muốn dùng kết hợp nhiều image có sẵn trên DockerHub thì sao ?
  + Hơn nữa, với tư duy của OOP, một class thì không nên cõng nhiều nhiệm vụ.

+ Giải pháp tốt hơn:
  + Cách vận hành mang thiên hướng service
  + Khi đó, khai báo ra nhiều service khác nhau (mysql service, redis service ...)
  + Sử dụng `docker-compose.yml` để khai báo và điều phối các service.
  + Chỉ cần một lệnh duy nhất là setup xong môi trường
    ```shell
    docker-compose up
    ```
+ Nếu Dockerfile như các công thức nấu ăn thì docker-compose như là cách thức để bày những món ăn đã chế biến lên bàn ăn.

# 2. Viết docker-compose

## 2.1 Khai báo service

+ Cấu trúc

  ```yaml
  version:
  services:
    service1:
      ...
    service2:
      ...
    ...
  networks:
  volumes:
  ```

+ `version`
  + Khai báo phiên bản
  + Ví dụ:
    ```yaml
    version: "3.7"
    ```
+ `service`
  + `service1` và `service2` giúp định nghĩa những service mà dự án sử dụng.
  + Ví dụ:
    ```yaml
    web:
      build:
        context: web
      env_file:
        - .env
      command: yarn start
      restart: always
      volumes:
        - ./web:/usr/src/app
      ports:
        - 60000:3000

    server:
      build:
        context: server
      env_file:
        - .env
      command: bash -c "rm -rf ./tmp/pids && rails s -b 0.0.0.0"
      tty: true
      stdin_open: true
      restart: always
      depends_on:
        - db
      volumes:
        - ./server:/usr/src/app
      ports:
        - 60001:3000

    db:
      image: postgres:11.2-alpine
      env_file:
        - .env
      volumes:
        - ./tmp/postgresql/data:/var/lib/postgresql/data
      restart: always
    ```

  + `build`: chỉ định context để build image, từ đó docker sẽ lấy image này để tạo container

      nếu không có build thì ta phải dùng `image`

  + `env_file`: khi chạy container sẽ có những biến env được khai báo trong đó

  + `volumes, command`: tập trung volumes vào docker-compose để tiện maintain

  + `ports`: nếu cần publish port đó ra host

  + `tty/stdin_open`: cho phép attach vào container, chỉ dùng khi dev

  + `restart`: always, để tránh down-time (lúc init project thì nên tránh vì nếu config sai => tạch => restart => tạch => ....)

  + `depends_on`: sẽ đợi cho những services khác chạy xong mới chạy

      Lưu ý là nó chỉ chờ service chạy ok thôi chứ không chờ đến lúc service *ready to work*

      Trong swarm mode depends_on bị ignore nên `restart: always` rất cần thiết, vì trong trường hợp 1 service bị phụ thuộc vào service khác, mà service đó lại chưa up => crash => restart

  * Tất cả các path của host định nghĩa trong file docker-compose đều lấy context là thư mục chứa file docker-compose

  * Khi thay đổi settings trong file docker-compose thì không cần rebuild lại image (trừ những thứ liên quan đến việc build)

* `network`:

  * Nếu ta không muốn dùng network mặc định của docker thì có thể tự định nghĩa.
  * Chung network, service có thể gọi tới nhau thông qua tên service

* `volumes`:

  * Khi ta tự tạo volume ở bên ngoài (external) thì khai báo ở đây để có thể dùng được trong service.



# 3. Ví dụ

+ Dưới đây là docker-compose của 1 dự án web nhỏ, bật 3 terminal để chạy

  + rails s
  + yarn start
  + node index.js

+ File

  ```yaml
  version: "3.7"
  services:
    proxy:
      build:
        context: proxy
      env_file:
        - .env
      restart: always
      command: ["nginx", "-g", "daemon off;"]
      volumes:
        - ./proxy/fsocial.conf:/etc/nginx/conf.d/default.conf
        - ./tmp/nginx/log:/var/log/nginx
      ports:
        - 3333:80
      depends_on:
        - web
        - server

    web:
      build:
        context: web
      env_file:
        - .env
      command: yarn start
      restart: always
      volumes:
        - ./web:/usr/src/app
      ports:
        - 60000:3000

    server:
      build:
        context: server
      env_file:
        - .env
      command: bash -c "rm -rf ./tmp/pids && rails s -b 0.0.0.0"
      tty: true
      stdin_open: true
      restart: always
      depends_on:
        - db
      volumes:
        - ./server:/usr/src/app
      ports:
        - 60001:3000

    db:
      image: postgres:11.2-alpine
      env_file:
        - .env
      volumes:
        - ./tmp/postgresql/data:/var/lib/postgresql/data
      restart: always

    adminer:
      image: adminer
      restart: always
      ports:
        - 60002:8080

    utils-yml:
      build:
        context: utils/yml
      command: node index.js
      env_file:
        - .env
      volumes:
        - ./web:/usr/src/app/web
        - ./utils:/usr/src/app/utils
  ```

# 4. Cách sử dụng


###### docker-compose build

  + Dùng để build những image được định nghĩa trong docker-compose.yml

  + Khi khai báo service tất cả những key ngoài `build` ra thì những thứ còn tại đều vô dụng trong Dockerfile

  + Chả khác gì so với `docker build`. Tuy nhiên tiết kiệm thời gian rất nhiều.

###### docker-compose up

  + Tiến hành run service
  + Có thể chạy background với tham số -d
  + Khi chạy ngầm, phải dùng `docker-compose logs` để xem log của service.
  + Ngay cả khi chưa chạy lệnh build, khi up, docker vẫn sẽ build cho ta hoặc pull image về, tạo volume cần thiết, rồi mới up service
  + Mặc định docker sẽ tạo cho ta 1 network nếu ta không khai báo network, tất cả các service đều có thể nhìn thấy nhau.

###### docker-compose down

  + Stop và remove container

  + Volume không bị xóa, có thể remove đi bằng cách thêm tham số -v

###### docker-compose logs

  + Xem log service

###### Note

  + Mặc định khi chạy lệnh docker-compose, những biến trong file .env sẽ có thể truy cập trong file docker-compose, nhưng với Dockerfile thì không.
  + Để có thể dùng trong Dockerfile của service, ta cần khai báo thêm args.
  + Bản chất là wrapper của docker CLI

# 5. Ứng dụng Docker trong framework

## 5.1 Laravel

  + Updating ...

## 5.2 Ruby On Rails

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
