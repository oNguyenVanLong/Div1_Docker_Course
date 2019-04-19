# Bich

#### Mở đầu:

* Nếu Dockerfile như các công thức nấu ăn thì docker-compose như là cách thức để bày những món ăn đã chế biến lên bàn ăn.

* docker-compose mang thiên hướng service

* Chỉ cần một lệnh duy nhất là setup xong môi trường

  ```shell
  docker-compose up
  ```

#### Ví dụ

```yml
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

Trên là docker-compose của 1 dự án web cùi cùi, như bình thường ta sẽ phải bật ít nhất là 3 terminal để chạy

* rails s
* yarn start
* node index.js

Sử dụng docker-compose ta có thể chạy deamon, chả tốn terminal nào cả.

#### Khai báo service

```yml
version:
services:
	service1:
		...
	service2:
		...
networks:
volumes:
```

* Thông thường file docker-compose sẽ có 4 phần như trên

  Tuy nhiên `networks` và `volumes` cũng ít khi phải dùng.

  Nếu khai báo thẳng vào services thì docker sẽ tự tạo cho ta luôn, còn khi tự tạo volumes, networks ở ngoài bằng `docker network` hay `docker volumes` thì ta sẽ phải dùng 2 key ở dưới để khai báo chúng.

* `service1` và `service2` là tên service, ta sẽ dùng chúng để chạy những câu lệnh của docker-compose

* Bắt buộc:

  * `build`: chỉ định context để build image, từ đó docker sẽ lấy image này để tạo container

    nếu không có build thì ta phải dùng `image`

* Recommend:

  * env_file: khi chạy container sẽ có những biến env được khai báo trong đó

  * volumes, command: tập trung volumes vào docker-compose để tiện maintain

  * ports: nếu cần publish port đó ra host

  * tty/stdin_open: cho phép attach vào container, chỉ dùng khi dev

  * restart: always, để tránh down-time (lúc init project thì nên tránh vì nếu config sai => tạch => restart => tạch => ....)

  * depends_on: sẽ đợi cho những services khác chạy xong mới chạy

    Lưu ý là nó chỉ chờ service chạy ok thôi chứ không chờ đến lúc service *ready to work*

    Trong swarm mode depends_on bị ignore nên `restart: always` rất cần thiết, vì trong trường hợp 1 service bị phụ thuộc vào service khác, mà service đó lại chưa up => crash => restart

* Tất cả các path của host định nghĩ trong file docker-compose đều lấy context là thư mục chứa file docker-compose

* Khi thay đổi settings trong file docker-compose thì không cần rebuild lại image (trừ những thứ liên quan đến việc build)

* `network`: 

  * Nếu ta không muốn dùng network mặc định của docker thì có thể tự định nghĩa.
  * Chung network, service có thể gọi tới nhau thông qua tên service

* `volumes`: 

  * Khi ta tự tạo volume ở bên ngoài (external) thì khai báo ở đây để có thể dùng được trong service.

#### Một số lệnh của docker-compose

###### Note

* Mặc định khi chạy lệnh docker-compose, những biến trong file .env sẽ có thể truy cập trong file docker-compose, nhưng với Dockerfile thì không.
* Để có thể dùng trong Dockerfile của service, ta cần khai báo thêm args.
* Bản chất là wrapper của docker CLI

###### docker-compose build

* Dùng để build những image được định nghĩa trong docker-compose.yml
* Khi khai báo service tất cả những key ngoài `build` ra thì những thứ còn tại đều vô dụng trong Dockerfile

* Chả khác gì so với `docker build`. Tuy nhiên tiết kiệm thời gian rất nhiều.

###### docker-compose up

* Tiến hành run service
* Có thể chạy background với tham số -d
* Khi chạy ngầm, phải dùng `docker-compose logs` để xem log của service.
* Ngay cả khi chưa chạy lệnh build, khi up, docker vẫn sẽ build cho ta hoặc pull image về, tạo volume cần thiết, rồi mới up service
* Mặc định docker sẽ tạo cho ta 1 network nếu ta không khai báo network, tất cả các service đều có thể nhìn thấy nhau.

###### docker-compose down

* Stop và remove container

* Volume không bị xóa, có thể remove đi bằng cách thêm tham số -v

###### docker-compose logs

* Xem log service

# Nam

Thực tế áp dụng trong dự án

        // write your code here

