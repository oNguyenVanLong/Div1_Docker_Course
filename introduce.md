# 1. So sánh công nghệ Container với Hypervisor
## Bài toán
  Ảo hoá là giải pháp được nghĩ ngay khi ta chỉ có một bộ máy tính đang cài windows nhưng muốn có thêm MacOS hay Ubuntu để code.
  Tuy nhiên đối với các doanh nghiệp lớn, những ứng dụng phức tạp, số lượng user tính tới hàng triệu thì nó lại là lời giải cho bài toán hóc búa về chi phí, quản lý, nâng cấp...
  Chưa kể tới bài toán chuẩn hoá, đồng bộ hoá môi trường. Làm thế nào đưa 1 ứng dụng chỉ có thể tương thích trên môi trường này sang một máy chủ với môi trường khác ?
  Các công nghệ máy ảo VM ra đời, được chia làm 2 mảng chính: *Container với Hypervisor*

## Container là gì?
  Công nghệ ảo hóa nằm ở tầng Operation System (hệ điều hành). Ví dụ đơn giản là máy bạn đã cài một OS, sau đó phần ảo hoá sẽ được cài như 1 ứng dụng chạy trong OS đó.

## Hypervisor là gì ?
  Hypervisor là ảo hỏa nằm ở tầng Hardware (phần cứng), tức là mô phỏng phần cứng và chạy những OS con trên phần cứng đó. Ví dụ điển hình là Virtual Box, VMware, BlueStack..v..v

  |   |Container|Hypervisor|
  |---|---------|----------|
  |Thời gian khởi động trung bình|500ms|20s|
  |Khả năng tối ưu phần cứng|Tối ưu nhờ cấp phát theo layer|Cố định khi khởi tạo|
  |Giao diện sử dụng|Khó tiếp cận hơn|Đơn giản trực quan|
  |Khả năng scale| Ez | Ko rõ |

# 2. Giới thiệu về Docker

  Docker là cả 1 hệ sinh thái xây dựng dựa trên công nghệ Container. Vậy đơn giản nó có công dụng ảo hoá, cụ thể những điều đáng quan tâm:
  - Với Docker, chúng ta có thể đóng gói mọi ứng dụng vd như webapp, backend, MySQL, BigData…thành các containers và có thể chạy ở “hầu hết” các môi trường vd như Linux, Mac, Window…
  - Docker Containers có một API cho phép quản trị các container từ bên ngoài. Giúp cho chúng ta có thể dễ dàng quản lí, thay đổi, chỉnh sửa các container.

## Hệ sinh thái Docker do Docker inc phát triển, các khái niệm

  **Docker engine:** Khi người ta nói đến “Docker” thì có thể hiểu đơn giản là họ đang nhắc đến Docker Engine. Docker Engine cung cấp đầy đủ các function để cho người sử dụng có thể làm việc được với Docker image và  Docker container. Để gọi đến các function này, người sử dụng có thể dùng gọi đến REST API của Docker, hoặc thực hiện thông qua giao diện conmand line (CLI – cấp cao hơn REST API). ví dụ: `docker run <image>` ... và nhiều câu lệnh khác.

  **Docker Toolbox**: đây là tool của Docker, được sử dụng  trên MacOS và Window

  **Docker Hub**: là nơi để đẩy các Docker Image lên. Push image lên theo đúng quy trình mà người ta sử dụng Docker.

  **Docker Trusted Registry (DTR)**: Nếu công ti của bạn cần bảo mật và chỉ muốn chia sẻ image ở nội bộ, hoặc chỉ cho những ai bạn muốn chia sẻ. Thì DTR như một Docker Hub phiên bản private. (đây là phiên bản mất phí, bạn sẽ trả tiền, và sẽ có người của công ti support cho bạn trong việc cài đặt và bảo trì hệ thống)

  **Docker Machine**: là công cụ giúp bạn cài đặt Docker Engine lên môi trường máy ảo một cách tự động, đồng thời cũng quản lí các host này với các câu lệnh của docker-machine (cái này hỗ trợ cả trên host thật, cloud…).

  **Docker Swarm**: is native clustering for Docker (theo docs.docker.com). Hiểu nôm na nó như một thằng trung gian giữa bạn và các Docker Host, nó tập trung các Docker Host về thành một mối. Và khi bạn làm việc thì bạn chỉ cần làm việc với cái “virtual host” mà Swarm tạo ra là ổn.

  **Docker Compose**:  Là một công cụ để định nghĩa một “multi-container application”. Ví dụ có một hệ thống gồm 2 image là apache và mysql. Trước khi ta sẽ phải run container mysql trước, rồi run thằng apache và link vào container đã chạy trước đó. Thì bây giờ ta chỉ cần viết một file docker-compose.yml để config hệ thống của chúng ta và chỉ sau một lệnh là cả hệ thống sẽ run theo cách mà nó đang run.

  **Dockerfile**: là một file chứa tập hợp các lệnh để Docker có thể đọc và thực hiện để đóng gói một image theo yêu cầu người dùng.

  **Docker Registry**: Chức năng tương tự như Docker Trusted Registry. Kho chứa images phiên bản open-sources (không mất phí).

  **Docker Cloud**: là hệ thống Paas cho phép bạn dễ dàng triển khai các app của mình lên môi trường cloud.

## Docker system gồm những thành phần nào ?
  Docker sử dụng kiến trúc client-server. Docker client sẽ liên lạc với các Docker daemon, các Docker daemon sẽ thực hiện các tác vụ build, run và distribuing các Docker container.  Cả Docker client và Docker daemon có thể chạy trên cùng 1 máy, hoặc có thể kết nối theo kiểu Docker client điều khiển các docker daemon giao tiếp với nhau thông qua socket hoặc RESTful API.

  - Docker Daemon

  Docker daemon chạy trên các máy host. Người dùng sẽ không tương tác trực tiếp với các daemon, mà thông qua Docker Client.

- Docker Client

  Là giao diện người dùng của Docker, nó cung cấp cho người dùng giao diện dòng lệnh và thực hiện phản hồi với các Docker Daemon.

  - Docker images

  Là một template chỉ cho phép đọc, ví dụ một image có thể chứa hệ điều hành Ubuntu và web app. Images được dùng để tạo Docker container. Docker cho phép chúng ta build và cập nhật các image có sẵn một cách cơ bản nhất, hoặc bạn có thể download Docker images của người khác.

  - Docker Container

  Docker container có nét giống với các directory. Một Docker container giữ mọi thứ chúng ta cần để chạy một app. Mỗi container được tạo từ Docker image. Docker container có thể có các trạng thái run, started, stopped, moved và deleted.

## Hoạt động của Docker ra sao ?
Một hệ thống Docker được thực thi với 3 bước chính :

`Build -> Push -> Pull,Run`

  - Build:
  Đầu tiên chúng ta sẽ tạo một dockerfile, trong dockerfile này chính là code của chúng ta.

  Dockerfile này sẽ được Build tại một máy tính đã cài đặt Docker Engine.

  Sau khi build ta sẽ thu được Container, trong Container này chứa bộ thư viện và ứng dụng của chúng ta.

  - Push:
  Sau khi có được Container, chúng ta thực hiện push Container này lên đám mây và lưu trữ ở đó.

  - Pull, Run:
  Giả sử một máy tính muốn sử dụng Container chúng ta đã push lên đám mây (máy đã cài Docker Engine) thì bắt buộc máy phải thực hiện việc Pull container này về máy. Sau đó thực hiện Run Container này.
