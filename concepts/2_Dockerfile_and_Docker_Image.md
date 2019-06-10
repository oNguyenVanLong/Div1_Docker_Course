# 1. Những lệnh quan trọng

* FROM <image>
  * sử dụng 1 image có sẵn làm base (môi trường)
  * VD: khi code Rails thì ta cần môi trường ruby, làm về js thì cần nodejs, ....
  * Trên production, để giảm dung lượng image, người ta hay dùng image alpine
* WORKDIR <path>
  * set context cho container
  * context khi copy file, chạy command, .... Những command sẽ luôn được chạy từ folder khai báo WORKDIR
  * VD nếu set WORKDIR = /usr/src/app, khi ta exec vào container, hoặc chạy command rails s, nó sẽ chạy câu lệnh này ở thư mục /usr/src/app (trong container)
* COPY <source> <destination>
  * copy file từ hệ thống sang image
  * source sẽ có context là thư mục chứa Dockerfile
  * destination có context là WORKDIR
  * VD
    * Docker file: `/home/sun/app/Dockerfile`
    * WORKDIR /usr/src/app
    * Nếu như ta dùng `COPY Gemfile* ./` nó sẽ copy Gemfile trong thư mục /home/sun/app trên máy ta vào thư mục /usr/src/app trong image
  * Có một lệnh nữa là `ADD`. 2 lệnh này cùng chung mục đích là copy file, tuy nhiên ADD hỗ trợ thêm cả url nữa, nhưng nếu chỉ copy file từ máy thì dùng COPY là đủ
* RUN
  * dùng để chạy câu lệnh bash
  * dùng khi cần cài đặt system dependency (apt-get install imagemagick)
* EXPOSE:
  * port được expose sẽ có trong metadata khi chạy `docker ps` để người vào sau biết cổng nào mà lần.
  * khi chạy `docker run -P` sẽ map những port được expose với port trên host (random)
  * Không dùng lệnh này ta vẫn có thể truy cập được vào port của container.
* ENV:
  * Khai báo biến env cho image
  * biến này sẽ được lưu ngay cả sau khi build xong image, vì vậy tránh khai báo thông tin nhạy cảm ở đây
* ARG:
  * Tham số truyền để build khi chạy `docker run --build-arg`
  * VD khi ta truyền RAILS_MASTER_KEY vào để compile assets file, ta nên dùng lệnh này, vì nó sẽ không lưu lại trong image.

# 2. Một vài lệnh khác

* VOLUME:

  * Khi dữ liệu container lớn, ta nên dùng volume để lưu dữ liệu thay vì writable layer
  * Nhanh hơn writable layer

* CMD:

  * Lệnh mặc định khi start container

* ENTRYPOINT:

  * Khi chạy docker run, docker exec ... container sẽ lấy ENTRYPOINT + CMD rồi chạy.

  * VD image ubuntu có ENTRYPOINT là /bin/bash -c

    Nếu ta sử dụng CMD là bash => lệnh chạy thực sự sẽ là `/bin/bash -c bash`

* Những lệnh này thường hay dùng trong docker-compose thay vì viết trong dockerfile

# 3. Khi nào cần build lại image?

* Khi hệ thống - nội tại image cần có sự thay đổi

  * cài gem mới (khi chạy bundle install)
  * cài thêm system dependencies
    * apt-get install

* Khi mà hệ thống không phụ thuộc vào những thay đổi thì không cần rebuild

  * cài gem bằng bundle install --path vendor/bundle
  * yarn install

  Ở môi trường dev, ta thường có config volumes: .:/path/to/app, vì vậy khi chạy những lệnh trên, nó sẽ trực tiếp mount luôn ra ngoài host, những lần chạy service sau đó, nó cứ lấy thư mục gem kia mount vào container và chạy ầm ầm thôi.

# 4. Hướng dẫn dử dụng Dockerfile cơ bản

+ Xem source code ở thư mục: [source_code/dockerfile/](https://github.com/longnv-0623/Div1_Docker_Course/tree/master/source_code/dockerfile)

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

# 5. Cơ chế build image qua layers

+ Docker image được xây dựng dựa trên các layers xếp chồng, giống như việc bạn xếp nhiều viên gạch chồng lên nhau vậy.

+ Nếu câu lệnh trước đó đã được thực thi và tạo layer thì Docker sẽ sử dụng layer cũ đó chứ không tạo layer mới nữa, giúp giảm thời gian build image và nếu ở một layer có sự thay đổi thì kể từ layer đó trở về sau, tất cả sẽ được build lại.

+ Khi pull image cũng tương tự như khi chúng ta build image vậy, từng layer được xây dựng theo mô hình cha con, sinh sau đẻ muộn hơn thì là layer con, kế thừa từ layer cha, tất cả đều được đặt tên là <none>, đến layer cuối cùng thì mới đầy đủ image của chúng ta và đặt tên chính xác.

# 6. Docker Hub

+ Docker hub là nơi lưu giữ và chia sẻ các file images (hiện có khoảng 300.000 images)
