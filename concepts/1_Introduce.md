## 1. Bài toán

**Bài toán 1:**

  + project_1 của bạn đã được cài và đang hoạt động ổn định trên máy.

  + project_2 chuẩn bị được cài đặt.

  -> Việc cài đặt thêm các software, package dễ ảnh hưởng tới những chương trình đang chạy.

**Bài toán 2:**

  + Cài đặt project vào máy, yêu cầu:
    + Ruby,
    + Raisl,
    + Mysql,
    + MongoDB,
    + Redis,
    + Sidekiq ...
  cùng với version, package đi kèm ...

-> Việc cài đặt, cấu hình phức tạp.

## 2. Phương pháp ảo hóa

  + Tạo ra máy ảo && đặt dự án chạy bên trong máy ảo đó.
  ![](https://user-images.githubusercontent.com/49421807/59422554-c6504300-8dfa-11e9-8969-712e254d9952.png)
  + Các công nghệ máy ảo ra đời:
    + Hypervisor (Virtualbox, Vmware, BlueStack..v..v)
    + Containerization (**Docker**, incognito_1, incognito_2, ...)

  **Mục đích của chia sẻ:**
    -> Nguồn gốc
    -> Công dụng
    -> Cách vận hành
  **Tránh mông lung khi nhắc tới `Docker`**

## 2. Công nghệ Hypervisor

+ `Hypervisor` là công nghệ ảo hóa ở tầng Hardware (phần cứng)
+ Tư tưởng:
  + Cấp cho 4 GB RAM, 10 GB disk,...
  + Dùng tài nguyên trên để cài đặt OS (Operation System )
  ![](https://user-images.githubusercontent.com/49421807/59420465-23e29080-8df7-11e9-9188-390eda275f98.png)
+ Nhược điểm:
  + Lãng phí tài nguyên nếu không sử dụng hết.
  + Thời gian khởi động lớn.
  + Vẫn chưa giải quyết được bài toán 2.

## 3. Công nghệ Containerization

  + `Containerization` là công nghệ ảo hóa ở tầng OS.
  + Tư tưởng:
    + Cài đặt máy ảo trên máy thật.
    + Dùng chung tài nguyên với máy thật (cần bao nhiêu, cấp bấy nhiêu, dùng xong trả lại).
    ![](https://user-images.githubusercontent.com/49421807/59420970-05c96000-8df8-11e9-829f-58bda1ca642c.png)
  + Nền tảng:
    + Container:
      + Một container (chiếc thùng) là một máy ảo.
      + Chứa các phần mềm cần thiết để dự án có thể chạy được.
      + Không bị ảnh hưởng từ bên ngoài
      + Cũng không làm ảnh hưởng ra bên ngoài.
      + Có thể  khởi động chỉ bằng 1 câu lệnh.
      + Có các trạng thái run, started, stopped, moved và deleted.

  + Ưu điểm:
    + Giải quyết cả 2 bài toán
    + Nhanh, gọn, nhẹ
  + Khuyêt điểm:
    + Khó áp dụng hơn.
    + Nếu tầng kernel bị hack thì sẽ ảnh hưởng toàn bộ.
  + Độ tin cậy:
    + Google, Amazon,..v..v sử dụng rộng rãi.
  ![](https://user-images.githubusercontent.com/49421807/59021343-d3f34f00-8875-11e9-92e4-4d86fbb3116a.png)

## 4. Giới thiệu về Docker

### Lịch sử hình thành

  + Các `ông lớn` dùng phần mềm gì để ứng dụng công nghệ này:
    + Google
      + Private source code.
    + Amazon
      + Private source code.
    + Docker (company name)
      + Docker (software name) - open source.
      ![](https://camo.githubusercontent.com/e93591edba8c651f251d3b24717097d77d705c34/68747470733a2f2f7669626c6f2e617369612f75706c6f6164732f35623162623362342d663362382d343138332d383533612d6431316630663461343939372e706e67)
  + Docker là:
    + Một phần mềm,
    + Xây dựng ra môi trường ảo hóa,
    + Trên công nghệ containerization,
    + Bằng cách tạo ra các container.


### Các khái niệm:

  ![](https://camo.githubusercontent.com/957fbc8b45fc596089690cb9186100224b270e97/68747470733a2f2f696d616765732e7669626c6f2e617369612f37353164373531322d633965372d343461352d626535362d3662316666393039366164662e706e67)

+ **Basic:**
  + `Docker Image:`
    + Khung xương định hình cho container.
    + Phong cách OOP:
      + Docker image -> class.
      + Docker container -> instance của class đó.
    + Snapshot tại một thời điểm của container.
    + Liên tưởng tới file ghost windows.

  + `Dockerfile:`
    + Một fiel dạng text.
    + Không có đuôi.
    + Tập hợp các lệnh.
    + Hướng dẫn Docker tạo image.

  + `Docker Hub:`
    + Lưu trữ các docker image.
    + Có thể  pull hoặc push image.

  + `Docker Compose:`
    + Chuyên biệt hóa, mỗi container làm một nhiệm vụ riêng.
      + Container nginx,
      + Container mysql,
      + Container redis ...
    + Khai báo và điều phối hoạt động các container.

+ **Advanced:**

  + `Docker engine:` Khi người ta nói đến “Docker” thì có thể hiểu đơn giản là họ đang nhắc đến Docker Engine. Docker Engine cung cấp đầy đủ các function để cho người sử dụng có thể làm việc được với Docker image và  Docker container. Để gọi đến các function này, người sử dụng có thể dùng gọi đến REST API của Docker, hoặc thực hiện thông qua giao diện conmand line (CLI – cấp cao hơn REST API). ví dụ: `docker run <image>` ... và nhiều câu lệnh khác.

  + `Docker Toolbox:` đây là tool của Docker, được sử dụng  trên MacOS và Window

  + `Docker Trusted Registry (DTR):` Nếu công ti của bạn cần bảo mật và chỉ muốn chia sẻ image ở nội bộ, hoặc chỉ cho những ai bạn muốn chia sẻ. Thì DTR như một Docker Hub phiên bản private. (đây là phiên bản mất phí, bạn sẽ trả tiền, và sẽ có người của công ti support cho bạn trong việc cài đặt và bảo trì hệ thống)

  + `Docker Machine:` là công cụ giúp bạn cài đặt Docker Engine lên môi trường máy ảo một cách tự động, đồng thời cũng quản lí các host này với các câu lệnh của docker-machine (cái này hỗ trợ cả trên host thật, cloud…).

  + `Docker Swarm:` is native clustering for Docker (theo docs.docker.com). Hiểu nôm na nó như một thằng trung gian giữa bạn và các Docker Host, nó tập trung các Docker Host về thành một mối. Và khi bạn làm việc thì bạn chỉ cần làm việc với cái “virtual host” mà Swarm tạo ra là ổn.

  + `Docker Registry:` Chức năng tương tự như Docker Trusted Registry. Kho chứa images phiên bản open-sources (không mất phí).

  + `Docker Cloud:` là hệ thống Paas cho phép bạn dễ dàng triển khai các app của mình lên môi trường cloud.

  + `Docker Daemon:` Docker daemon chạy trên các máy host. Người dùng sẽ không tương tác trực tiếp với các daemon, mà thông qua Docker Client.

    + Docker sử dụng kiến trúc client-server. Docker client sẽ liên lạc với các Docker daemon, các Docker daemon sẽ thực hiện các tác vụ build, run và distribuing các Docker container.  Cả Docker client và Docker daemon có thể chạy trên cùng 1 máy, hoặc có thể kết nối theo kiểu Docker client điều khiển các docker daemon giao tiếp với nhau thông qua socket hoặc RESTful API.

  + `Docker Client:` Là giao diện người dùng của Docker, nó cung cấp cho người dùng giao diện dòng lệnh và thực hiện phản hồi với các Docker Daemon.
