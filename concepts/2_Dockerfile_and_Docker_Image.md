## 1. Cài đặt Docker

Tham khảo tại [trang chủ](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

## 2. Dockerfile
https://viblo.asia/p/5-dieu-can-chu-y-khi-viet-dockerfile-djeZ1VnolWz

![](https://camo.githubusercontent.com/957fbc8b45fc596089690cb9186100224b270e97/68747470733a2f2f696d616765732e7669626c6f2e617369612f37353164373531322d633965372d343461352d626535362d3662316666393039366164662e706e67)

  + [Dockerfile:](https://docs.docker.com/engine/reference/builder/)
    + Một file dạng text.
    + Không có đuôi.
    + Tập hợp các lệnh.
    + Hướng dẫn Docker tạo image.

-> `Dockerfile` sẽ quy định `Docker image` được khởi tạo từ đâu, gồm những gì trong đó.

### 2.1 Tư tưởng

  + Lấy image có sẵn.
  + Cài đặt thêm
  + Cấu hình

  ![](https://user-images.githubusercontent.com/49421807/59746865-f2a80b80-92a1-11e9-8faf-c3d56ef4feb4.png)

  -> Liên tưởng tới cài lại OS (Windows, Linux)
### 2.1 Cấu trúc

[Demo:](https://github.com/longnv-0623/Div1_Docker_Course/tree/master/source_code/dockerfile)

![](https://user-images.githubusercontent.com/49421807/59744019-d2754e00-929b-11e9-836c-24eb956699dc.png)

+ `Dockerfile` -> `Docker image` -> `Container`

+ `webroot folder:` project code

+ `start.sh` Khởi động service đi kèm.


### 2.2 Viết Dockerfile

**Thiết lập image gốc**
+ [FROM](https://docs.docker.com/engine/reference/builder/#from)
  + Sử dụng 1 image có sẵn làm base (thằng cha của image này là thằng nào)
  + Ví dụ:
    ```
    FROM ubuntu:16.04
    ```
  + Tìm image `ubuntu:16.04` ở trong máy
  + Tự động `pull image` này về từ [DockerHub](https://hub.docker.com/_/ubuntu)
    ![](https://user-images.githubusercontent.com/49421807/59745699-8b895780-929f-11e9-817f-2558d84e1b20.png)
  + ubuntu: tên image, 16:04 là tên tag.

+ [LABEL](https://docs.docker.com/engine/reference/builder/#label)
  + Thông tin đi kèm (Optional)
  + Ví dụ:
    ```
    LABEL author.name="..."
    LABEL author.email="..."
    LABEL description="This docker tutorial"
    ```

**Cài đặt ứng dụng**

![](https://user-images.githubusercontent.com/49421807/59752762-04db7700-92ad-11e9-9c04-7126468822bb.png)

+ [ENV](https://docs.docker.com/engine/reference/builder/#env)
  + Khai báo biến môi trường
  + Vẫn được lưu ngay cả sau khi build xong image
    -> Tránh khai báo thông tin nhạy cảm.
    -> `ENV DEBIAN_FRONTEND noninteractive` là một ví dụ.

+ [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir)
  + Thiết lập thư mục làm việc cho RUN, CMD, ENTRYPOINT, COPY và ADD
  + Hoặc khi exec vào container

+ [RUN](https://docs.docker.com/engine/reference/builder/#run)
  + Thực thi bất kỳ lệnh nào trong một layer mới
  + Kể từ trên cùng của image hiện tại và commit kết quả
  + Sẽ được sử dụng cho bước tiếp theo trong Dockerfile.

+ [COPY](https://docs.docker.com/engine/reference/builder/#copy)
  + Copy file từ host sang image
  + VD:
    + Docker file: `/home/sun/app/Dockerfile`
    + WORKDIR `/usr/src/app`
    + `COPY Gemfile+ ./`
    -> `/home/sun/app/Dockerfile` máy thật -> `/usr/src/app` trong image

+ [ADD](https://docs.docker.com/engine/reference/builder/#add)
  Tương tự COPY
  + Hỗ trợ cả url
  + Hỗ trợ giải nén ...

**Cấu hình**

+ [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)
  + port được expose sẽ có trong metadata khi chạy `docker ps` để người vào sau biết cổng nào mà lần.
  + khi chạy `docker run -P` sẽ map những port được expose với port trên host (random)
  + Không dùng lệnh này ta vẫn có thể truy cập được vào port của container.

+ ARG:
  + Tham số truyền để build khi chạy `docker run --build-arg`
  + VD khi ta truyền RAILS_MASTER_KEY vào để compile assets file, ta nên dùng lệnh này, vì nó sẽ không lưu lại trong image.

+ VOLUME:

  + Khi dữ liệu container lớn, ta nên dùng volume để lưu dữ liệu thay vì writable layer
  + Nhanh hơn writable layer

+ CMD:

  + Lệnh mặc định khi start container

+ ENTRYPOINT:

  + Khi chạy docker run, docker exec ... container sẽ lấy ENTRYPOINT + CMD rồi chạy.

  + VD image ubuntu có ENTRYPOINT là /bin/bash -c

    Nếu ta sử dụng CMD là bash => lệnh chạy thực sự sẽ là `/bin/bash -c bash`

+ Những lệnh này thường hay dùng trong docker-compose thay vì viết trong dockerfile


## 2.2. Hướng dẫn dử dụng Dockerfile cơ bản

+ [Source code: source_code/dockerfile](https://github.com/longnv-0623/Div1_Docker_Course/tree/master/source_code/dockerfile)

+ Build docker image từ Dockerfile, ta sử dụng câu lệnh sau:

    ```
    sudo docker build -t <image_name> .
    ```

   Ví dụ:

    ```
    sudo docker build -t ubuntu_nginx .
    ```

+ Bạn có thể dùng lệnh

    ```
    docker images
    ```

  để xem thành quả nhé !

  ![](https://user-images.githubusercontent.com/18675907/59087107-f3e64980-892d-11e9-840e-0abd3fd18f07.png)

+ Và

  ```
  docker inspect ubuntu_nginx
  ```

  để xem thêm thông tin.

  ![](https://user-images.githubusercontent.com/18675907/59087541-23498600-892f-11e9-9caf-9b56da755f51.png)

## 5. Cơ chế build image qua layers

+ Docker image được xây dựng dựa trên các layers xếp chồng, giống như việc bạn xếp nhiều viên gạch chồng lên nhau vậy.

+ Nếu câu lệnh trước đó đã được thực thi và tạo layer thì Docker sẽ sử dụng layer cũ đó chứ không tạo layer mới nữa, giúp giảm thời gian build image và nếu ở một layer có sự thay đổi thì kể từ layer đó trở về sau, tất cả sẽ được build lại.

+ Khi pull image cũng tương tự như khi chúng ta build image vậy, từng layer được xây dựng theo mô hình cha con, sinh sau đẻ muộn hơn thì là layer con, kế thừa từ layer cha, tất cả đều được đặt tên là <none>, đến layer cuối cùng thì mới đầy đủ image của chúng ta và đặt tên chính xác.

+ The default docker images will show all top level images, their repository and tags, and their size.

## 3. Khi nào cần build lại image?

+ Khi hệ thống - nội tại image cần có sự thay đổi

  + cài gem mới (khi chạy bundle install)
  + cài thêm system dependencies
    + apt-get install

+ Khi mà hệ thống không phụ thuộc vào những thay đổi thì không cần rebuild

  + cài gem bằng bundle install --path vendor/bundle
  + yarn install

  Ở môi trường dev, ta thường có config volumes: .:/path/to/app, vì vậy khi chạy những lệnh trên, nó sẽ trực tiếp mount luôn ra ngoài host, những lần chạy service sau đó, nó cứ lấy thư mục gem kia mount vào container và chạy ầm ầm thôi.
