# Bich
So sánh CMD / entrypoint
Giới thiệu Docker multi-stage build

        // write your code here


# Hoan

### Makefile

+ Phải công nhận là gõ những dòng docker-compose up -d ... nhiều như vậy thật là mệt, vậy thì hãy dùng Makefile

+ Thay vì phải gõ từng câu lệnh một

```
docker-compose up -d mysql redis worker
```

thì hãy viết vào trong [Makefile](https://github.com/longnv-0623/Div1_Docker_Course/blob/master/practice/Makefile)

```
up:
   docker-compose up -d mysql redis worker

dev:
	docker-compose run --rm -p 3000:3000 app rails s
```

Khi đó, trên Terminal gõ `make up` để start các background containers, sau đó gõ `make dev` để start main container


Hơn nữa, khi có một member mới vào dự án và chưa rõ về Docker (có thể là member của team front end chẳng hạn), khi support setup project, bạn chỉ cần hướng dẫn.

Chạy make up để bật các tiến trình nền.

Chạy make dev để start project.

Chạy make test để test code trước khi gửi pull request.

Mà bản thân họ không cần phải có quá nhiều kiến thức về Docker, khá là tiện lợi phải không ^_^


### Xóa dangling images

Dòng ---> Using cache xuất hiện mỗi khi bạn build image chính là tái sử dụng các layers. Những layers mà không được tái sử dụng nữa được gọi là dangling images, tạm dịch là những image lơ lửng -> nó không trỏ tới images nào cả.

Xóa dangling image

```
sudo docker rmi -f $(docker images -f "dangling=true" -q)
```


### Preload for rals project với spring

From this example: https://github.com/jonleighton/spring-docker-example

Step 1: Add gem, then install it
```
$ gem 'spring-commands-rspec'
$ gem 'spring-commands-rubocop'
```

```
$ bundle install
$ bundle exec spring binstub --all
```

Step 2: Add service to docker-compose.yml
```
  spring:
    container_name: spring
    build: .
    volumes:
      - .:/app
    restart: always
    command: bundle exec spring server
    networks:
      - kauriru
    # This ensures that the pid namespace is shared between the host
    # and the container. It's not necessary to be able to run spring
    # commands, but it is necessary for "spring status" and "spring stop"
    # to work properly.
    pid: host
```

Step 3: Config

```
config/spring_client.rb
ENV["SPRING_SOCKET"] = "tmp/spring.sock"
```

Note:

Hãy luôn giữ cho container spring running
