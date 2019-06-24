## 1. Lý thuyết

![](https://camo.githubusercontent.com/957fbc8b45fc596089690cb9186100224b270e97/68747470733a2f2f696d616765732e7669626c6f2e617369612f37353164373531322d633965372d343461352d626535362d3662316666393039366164662e706e67)

- Từ có thể run image tạo nên container.

- Tuy nhiên ta không chạy trực tiếp những image được, vì image chỉ có thể đọc (read-only), và nó vỗn dĩ không phải là file vật lý và chỉ tồn tại trong Docker.

- Nên trong thực tế, ta đang tạo ra nhiều instance khác nhau, hoạt động 1 cách độc lập (mỗi instance đó chính là container)

- Giống như trong lập trình, sau khi viết `class`, xong ta dùng `.new` để tạo ra những instance của class.

- Những instance này tất nhiên sẽ có những dữ liệu mà image có, tuy nhiên khi chạy, chúng sinh ra dữ liệu mới (thay đổi state) thì hoàn toàn độc lập, không liên quan gì với nhau nữa.

## 2. Câu lệnh

- Run một image:

  ```shell
  sudo docker run -v <forder_in_computer>:<forder_in_container> -p <port_in_computer>:<port_in_container> -it <image_name> /bin/bash
  ```

  Trong đó:

  + -v : Thể hiện việc mount volume, dữ liệu từ thư mục từ máy thật có thể được truy cập từ thư mục của máy ảo.

  + -p: Cổng mạng từ máy thật để dẫn tới cổng mạng của máy ảo đang chạy.

  + -t: Chạy container và mở terminal bằng /bin/bash


- Kiểm tra trạng thái các container hđang chạy

  ```shell
  docker ps
  docker ps -a
  ```

- Hết giờ đi về, cần tắt container đi thì ta dùng

  ```shell
  docker stop container 		# dừng container lại
  docker start container 		# Hôm sau đến bật lại container đó
  docker kill container 		# Ngứa mắt kill luôn
  docker restart container 	# Khi sửa file config, cần restart lại container
  ```

- Docker commit

  + Image read-only, không thể thay đổi được
  + Tuy nhiên ta lại muốn lưu trạng thái của 1 container đang chạy lại, để từ đó tạo ra nhiều instance mới.
  + `docker-commit` giúp ta làm điều đó, nó sẽ tạo ra 1 image mới chứa toàn bộ dữ liệu của container lại thời điểm đó

-> Từ đó ta có một cách hiểu mới về container và image:

  > Image là những snapshot, immuatable (bất biến, read-only) của container. Container là những running (hoặc stopped) instance của image

  Tham khảo từ https://stackoverflow.com/a/23667302

## 3. Thực hành

+ Tiếp tục xem source code ở thư mục: [source_code/dockerfile/](https://github.com/longnv-0623/Div1_Docker_Course/tree/master/source_code/dockerfile)

+ Tạo container từ image.

  + Ví dụ vào localhost mặc định của nginx:
    ```
    sudo docker run -p 9000:80 -it ubuntu_nginx /bin/bash
    ```

    ![](https://camo.githubusercontent.com/53ccfe2c05911d26d0093800cea1b74eee7333ce/68747470733a2f2f696d616765732e7669626c6f2e617369612f38333531633333362d393130312d343334342d623632362d3937313933666434386563382e706e67)

  + Ví dụ vào thư mục dự án ở máy thật:
    ```
    sudo docker run -v ~/GitRepo/Div1_Docker_Course/source_code/dockerfile/web_root:/var/www/html -p 9000:80 -it ubuntu_nginx /bin/bash
    ```

    Thay thế
    ```~/GitRepo/Div1_Docker_Course/source_code/dockerfile/web_root```
    cho đúng với trên máy bạn nhé !

    ![](https://camo.githubusercontent.com/77244822b9ada2bc0b66b552c65a085075e00972/68747470733a2f2f696d616765732e7669626c6f2e617369612f30386435386566652d386434352d346335662d616138372d6135396237653936666638302e706e67)
