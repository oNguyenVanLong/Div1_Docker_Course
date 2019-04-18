#### Dockerfile

* Là thứ quan trọng nhất
* Không có Dockerfile thì không thể build được project

#### Magic

```shell
docker build <args>
```

#### Những lệnh quan trọng

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

#### Một vài lệnh khác

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

#### Note

* Mỗi lệnh trong dockerfile sẽ tạo ra 1 layer mới, nó sẽ làm tăng kích thước image sau khi build.

* Một số lệnh có khả năng cache

  * `yarn install` sử dụng `yarn.lock` và `package.json`
  * `bundle install` sử dụng `Gemfile.lock` và `Gemfile`

  Ta nên copy những file này vào image trước khi những lệnh này, nó sẽ sử dụng cache thay vi cài đặt lại từ đầu, build 1 layer mới (và những layer sau cũng tạch nốt)

  ```Dockerfile
  COPY Gemfile* ./
  RUN bundle
  ```

* Nên gộp những lệnh RUN lại với nhau:

  * VD khi cài nodejs + yarn:

    ```Dockerfile
    RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - && \
      curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
      echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
      apt-get update && \
      apt-get install -y build-essential mysql-client libv8-dev nodejs yarn
    ```

* Gộp lệnh COPY nếu có thể

  ```Dockerfile
  COPY Gemfile* ./
  ```

  thay vì 

  ```Dockerfile
  COPY Gemfile .
  COPY Gemfile.lock .
  ```

* Chỉ nên viết những lệnh cần thiết để khởi tạo môi trường, một số lệnh không liên quan hoặc có thể bị replace bởi volumes thì không nên viết trong Dockerfile

  * Nếu chạy `yarn install` trong quá trình build, mà sau đó ta lại mount thư mục hiện tại vào trong container, thì node_modules sau khi chạy yarn install sẽ bị replace ngay và luôn => mất => vô dụng

  * Nên tránh cách viết như sau vì nó chả liên qua gì đến quá trình build cả.

    Dưới đây là Dockerfile trong 1 dự án của 1 công ty có doanh thu nghìn tỷ yên.

    ```shell
    RUN if [ "$LOCAL_MACHINE" != "true" ]; then bundle exec rake db:create && bundle exec rake db:migrate ; fi
    ADD package.json /package.json
    RUN yarn install
    ```

  * Thay vì vậy hãy viết vào file shell ở ngoài, rồi sử dụng CMD, và tách riêng dockerfile cho dev/production ra.

    ```
    # Dockerfile
    CMD ["start.sh"]
    ```

    ```shell
    # start.sh
    yarn install
    rm -rf tmp/pids
    rails s
    ```

    Nếu viết như trên, khi container start, nó sẽ chạy `yarn install` sinh ra thư mục `node_modules`, sau đó sẽ được mount ra host, chứ không bị replace nữa.

* Nhớ sử dụng EXPOSE, mặc dù không viết thì nó vẫn chạy như thường.

#### Khi nào cần build lại image?

* Khi hệ thống - nội tại image cần có sự thay đổi

  * cài gem mới (khi chạy bundle install)
  * cài thêm system dependencies
    * apt-get install

* Khi mà hệ thống không phụ thuộc vào những thay đổi thì không cần rebuild

  * cài gem bằng bundle install --path vendor/bundle
  * yarn install

  Ở môi trường dev, ta thường có config volumes: .:/path/to/app, vì vậy khi chạy những lệnh trên, nó sẽ trực tiếp mount luôn ra ngoài host, những lần chạy service sau đó, nó cứ lấy thư mục gem kia mount vào container và chạy ầm ầm thôi.