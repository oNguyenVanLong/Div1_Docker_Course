#### Tại sao cần Docker?

* Cài môi trường local rất mất thời gian, khó để chạy song song 3,4 project cùng lúc

* Môi trường phát triển đều được đóng gói thành 1 cục, kết quả là Dockerfile/docker-compose.yml

  Khi cài máy, ta chỉ cần cài đặt Docker, vào project gõ `docker-compose up -d` cái mà không phải nghĩ ngợi gì nhiều. Hãy nói lời tạm biệt với việc cài DB, SDK, ...

* Có thể deploy mà không biết gì về server

* Lười, muốn deploy, cài đặt môi trường mà chỉ cần 1 lệnh

* Nhẹ, vì dùng nhân linux của OS luôn, không phải cài đặt máy ảo (trên Linux)

#### Đồ chơi

* zsh
* zsh docker plugin
* VSCode docker plugin

#### Ví dụ

* Pull code có project, docker-compose, Dockerfile

* Build 1 rails app => image
* Run image => container
* Add thêm gem => rebuild => sinh ra dangling image

#### Image

* Trước hết project cần có ít nhất là Dockerfile

* Lệnh sau

  ```shell
  docker build --tag=depzai .
  # hoặc
  docker build --tag=depzai:pro . # mặc định tag = latest
  ```

  sẽ tiến hành build (giống như việc ta loay hoay ngồi cài môi trường bằng cơm vậy) project theo đúng trình tự ta viết trong Dockerfile.

* Tưởng tượng như khi nấu ăn, `Dockerfile` giống như công thức để nấu những món ăn ngon vậy

  Tuy nhiên cũng có trường hợp `Dockerfile` nát như tương, có rất nhiều câu lệnh thừa, chưa cache, ....

* Để xem danh sách image có trên máy ta:

  ```shell
  docker image ls
  ```

* Xóa image đi, ta có thể dùng lệnh

  ```shell
  docker rmi <image_hash>
  ```

  `image_hash` có thể lấy từ lệnh ls trên.

  Một vài trường hợp image đang được sử dụng thì có thể thêm option `--force` hay gọn hơn là `-f

#### Container

* OK ta build xong image rồi, vậy giờ làm gì với nó?

* Từ một image, ta có thể sử dụng lệnh `docker run` để chạy nó

  Tuy nhiên ta không chạy trực tiếp những image, vì image chỉ có thể đọc (read-only)

  Thực tế ta đang tạo ra nhiều instance khác nhau, hoạt động 1 cách độc lập (gọi là container)

  Giống như trong Ruby, ta viết `class` vậy, xong ta dùng `.new` để tạo ra những instance của class.

  Những instance này tất nhiên sẽ có những dữ liệu mà image có, tuy nhiên khi chạy, chúng sinh ra dữ liệu mới (thay đổi state) thì hoàn toàn độc lập, chả liên quan gì với nhau.

* Hãy cùng chạy một image:

  ```shell
  docker run -it -p 3000:3000 depzai
  ```

* Kiểm tra trạng thái container

  ```shell
  docker ps
  ```

* Hết giờ đi về, cần tắt container đi thì ta dùng

  ```shell
  docker stop container 		# dừng container lại
  docker start container 		# Hôm sau đến bật lại container đó
  docker kill container 		# Ngứa mắt kill luôn
  docker restart container 	# Khi sửa file config, cần restart lại server
  ```

* `docker commit`

  Image read-only, không thể thay đổi được, tuy nhiên ta lại muốn lưu trạng thái của 1 container đang chạy lại, để từ đó tạo ra nhiều instance mới. Câu lệnh này giúp ta làm điều đó, nó sẽ tạo ra 1 image mới chứa toàn bộ dữ liệu của container lại thời điểm đó

  Từ đó ta có một cách hiểu mới về container và image:

  > Image là những snapshot, immuatable (bất biến, read-only) của container. Container là những running (hoặc stopped) instance của image

  Tham khảo từ https://stackoverflow.com/a/23667302

#### Layer

* Như đã nói ở trên, image read-only, còn container có thể chạy, có nhiều state khác nhau.

  Tuy nhiên về bản chất thì khá giống nhau

  ![](https://docs.docker.com/storage/storagedriver/images/container-layers.jpg)

* Image được tạo từ nhiều layer kết hợp với nhau

  Ở Dockerfile, hình dung cứ 1 dòng lệnh (STEP) là nó sẽ tạo ra 1 layer, sau đó những layer này sẽ giao lưu kết hợp với nhau và tạo ra image.

  Layer có thể tái sử dụng để build nhiều image khác nhau

  Theo kinh nghiệm thì nếu STEP n mà khi build không sử dụng được cache, thì từ step n+1, Docker sẽ build mới layer hết luôn.

* Nhìn hình có thể thấy Container hơn Image ở đúng tầng layer trên cùng.

  Khi chạy `docker run`, thực ra nó sẽ chỉ thêm 1 layer writable lên trên image, ném cho ta dùng.

  Mọi dữ liệu khi container chạy đều được ghi vào layer này mà không làm ảnh hưởng đến bố con thằng nào khác. Và chỉ khi nào file thay đổi nó mới ghi vào layer này (copy-on-write, thin layer)

  Khi ta dừng container thì layer này cũng cuốn theo chiều gió luôn.

![](https://docs.docker.com/v17.09/engine/userguide/storagedriver/images/sharing-layers.jpg)

#### Dangling images

* Là những image có name và tag có dạng `<none>`

* Ngoài ra còn có unused images nữa, đơn giản là những image không có instance nào đang chạy (hoặc đã dừng)

* Khi nào thì tạo ra dangling image:

  * Tiến hành build project lần đầu
  * Sửa dockerfile, gemfile... => Build lại project
  * Image mới được tạo, trùng tên với image cũ, nếu ta không sửa tên image cũ, nó sẽ trở thành dangling image

* Dangling images thông thường chỉ là những image thừa, bỏ đi, gây tốn bộ nhớ, ta có thể xóa chúng đi.

* Tuy nhiên đối với môi trường production, dangling image có thể là những image phiên bản cũ, bạn có thể dùng nó để rollback khi version mới đang căng thì đứt dây đàn.

* Lệnh sau sẽ xóa toàn bộ những thứ thừa thãi (nên cân nhắc khi dùng)

  ```shell
  docker system prune -a
  ```

  

