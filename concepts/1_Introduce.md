# 1. Bài toán

**Bài toán 1:**

  + Bạn mới nhận được dự án, bạn muốn cài cắm nó và chạy thử nó lên xem có gì hay ho ở trong đó.

  + Tuy nhiên, bạn lại e ngại việc cài đặt thêm các software, package sẽ ảnh hưởng tới những chương trình đang chạy trong máy của bạn.

  + Ví dụ:
    + project_1 đang hoạt động ổn định với mysql 5.7
    + project_2 lại khuyến cáo với mysql 8.0
    + Thế làm gì bây giờ, cài song song hai phiên bản mysql à, hay cài version 7.5,hay version cài 8.0 ?

**Bài toán 2:**

  + Bạn được phân bổ vào dự án mới, hào khí ngút trời, thế như chẻ tre vào happy coding.
  + Đọc README.md một hồi thấy dự án đó sử dụng chao ôi sao nhiều thế, nào ruby, mysql, MongoDB, redis, sidekiq ... kèm theo một tá thư viện package đi kèm ...
  + Ngồi hì hục google cả buổi sáng, cài cắm từng phần mềm một kèm theo phiên bản bao nhiêu, lỗi lên lỗi xuống.
  + Rồi tụt mood (yaoming)

-> Sau đây, chúng ta sẽ điểm qua một vài công nghệ đã được sinh ra để giải quyết những vấn đề trên.


# 2. Ảo hóa

  + Để giải quyết bài toán 1 thì chúng ta có thể nghĩ đến ảo hoá. Một cách dễ hiểu nhất thì đây là giải pháp khi ta chỉ có một bộ máy tính đang cài windows nhưng muốn có thêm MacOS hay Ubuntu để code. Nhưng thưc ra ưu điểm của nó còn khá nhiều.

  + Các công nghệ máy ảo VM ra đời, được chia làm 2 mảng chính:  Hypervisor với Containerization

# 2. Công nghệ Hypervisor

+ `Hypervisor` là ảo hỏa nằm ở tầng Hardware (phần cứng), tức là mô phỏng phần cứng và chạy những OS con trên phần cứng đó. Ví dụ điển hình là Virtual Box, VMware, BlueStack..v..v

+ Để giải quyết bài toán 1, chúng ta có thể dùng Virtual Box cài một máy ảo trên máy thật mà chúng ta đang dùng, rồi setup dự án lên máy ảo đó. Như vậy dự án mới sẽ chạy mà không ảnh hưởng tới dự án cũ.

+ Thế nhưng bài toán 2 thì vẫn chưa giải quyết được.

+ Hơn nữa, khi sử dụng Virtual Box để cài đặt máy ảo thì chúng ta cần phân phát ngay từ đầu một lượng tài nguyên nhất định cho máy ảo. Nếu như máy ảo đó chưa sử dụng hết thì cũng không tái sử dụng được, đó là một sự lãng phí.

# 3. Công nghệ Containerization

  Ở bước tiếp theo, ra đời công nghệ `Containerization`

  + Đây là công nghệ ảo hóa nằm ở tầng Operation System (hệ điều hành). Ví dụ đơn giản là máy bạn đã cài một OS, sau đó phần ảo hoá sẽ được cài như 1 ứng dụng chạy trong OS đó.

## Hoạt động như thế nào

  + Thay vì phân chia tài nguyên rạch ròi như công nghệ Hypervisor thì tất cả các máy ảo sẽ dùng chung tài nguyên với máy mẹ. Khi nào máy ảo cần tài nguyên thì cung cấp, cần bao nhiêu thì cấp bấy nhiêu. Như vậy sẽ tránh lãng phí.

## Container là gì?
  + Hơn nữa, khi đó hệ thống sẽ được đóng gói thành các contaner.
  + Mỗi một container là một máy ảo.
  + Cũng có thể  ví von mỗi một container là một chiếc thùng, và chiếc thùng đó:
    + Chứa các phần mềm và cài đặt cần thiết mà phần mềm cần để có thể chạy được.
    + Không bị các yếu tố liên quan đến môi trường hệ thống làm ảnh hưởng tới.
    + Cũng như không làm ảnh hưởng tới các phần còn lại của hệ thống.
  + Thay vì phải cài đặt hàng tá những software hay package cần thiết như trước, bạn chỉ cần dựng các container đã được config sẵn lên và chạy dự án ở trong đó.
  + Ví dụ: Dự án của bạn sẽ bao gồm 3 container mysql, php, redis. Mỗi  container sẽ làm nhiệm vụ riêng biệt.

-> Như vậy bài toán 2 đã được giải quyết.

  + Công nghệ này cũng được ông lớn Google sử dụng rộng rãi
  ![](https://user-images.githubusercontent.com/49421807/59021343-d3f34f00-8875-11e9-92e4-4d86fbb3116a.png)

## So sánh

  |   |Containerization|Hypervisor|
  |---|---------|----------|
  |Thời gian khởi động trung bình|500ms|20s|
  |Khả năng tối ưu phần cứng|Tối ưu nhờ cấp phát theo layer|Cố định khi khởi tạo|
  |Giao diện sử dụng|Khó tiếp cận hơn|Đơn giản trực quan|
  |Khả năng scale| Ez | Ko rõ |

# 4. Giới thiệu về Docker

## Lịch sử hình thành
  + Công nghệ ảo hóa (vitualization) thì ta có thể dùng công cụ Vitualbox hay VMware thế còn đối với containerlization thì dùng gì đây ? Google họ dùng gì ?

  + Oh mình không biết được, mỗi một ông lớn có một cách để áp dụng công nghệ này và họ private source code.

  + Gần đây, mà cũng lâu rồi 😄 Có một công ty tiến hành public source code của họ về công nghệ này, họ tung ra sản phẩm mang tên là Docker và nhận được nhiều sự chú ý.
  ![](https://camo.githubusercontent.com/e93591edba8c651f251d3b24717097d77d705c34/68747470733a2f2f7669626c6f2e617369612f75706c6f6164732f35623162623362342d663362382d343138332d383533612d6431316630663461343939372e706e67)

  + Như vậy, Docker là một phần mềm, giúp xây dựng ra các môi trường ảo hóa, trên công nghệ Containerization bằng cách tạo ra các container.

  + Với Docker, chúng ta có thể đóng gói mọi ứng dụng vd như webapp, backend, MySQL, BigData…thành các containers và có thể chạy ở “hầu hết” các môi trường vd như Linux, Mac, Window…

  + Docker Containers có một API cho phép quản trị các container từ bên ngoài. Giúp cho chúng ta có thể dễ dàng quản lí, thay đổi, chỉnh sửa các container.

## Các khái niệm

  ![](https://camo.githubusercontent.com/957fbc8b45fc596089690cb9186100224b270e97/68747470733a2f2f696d616765732e7669626c6f2e617369612f37353164373531322d633965372d343461352d626535362d3662316666393039366164662e706e67)

+ **Basic:**

  + `Container:` Đã giải thích ở trên

  + `Image:` Có thể hiểu Docker image như khung xương giúp tạo nên hình hài cho container. Nếu nói với phong cách lập trình hướng đối tượng, Docker image là class, còn container là thực thể (instance, thể hiện) của class đó.

  + `Dockerfile:` Là một file chứa tập hợp các lệnh để Docker có thể đọc và thực hiện để đóng gói một image theo yêu cầu người dùng.

  + `Docker Hub:` Là nơi để lưu trữ các docker image, mọi người có thể push image lên đây để lưu trữ hoặc chia sẻ và tải image từ đây về.

  + `Docker Compose:` Khi dự án sử dụng nhiều container để chuyên biệt hóa nhiệm vụ cho từng container thì cần một số cấu hình để  các container có thể liên hệ và truyền dữ liệu với nhau. Vì khi đó ta dùng `docker-compose` để cấu hình những thông số này.

+ **Advanced:**

  + `Docker engine:` Khi người ta nói đến “Docker” thì có thể hiểu đơn giản là họ đang nhắc đến Docker Engine. Docker Engine cung cấp đầy đủ các function để cho người sử dụng có thể làm việc được với Docker image và  Docker container. Để gọi đến các function này, người sử dụng có thể dùng gọi đến REST API của Docker, hoặc thực hiện thông qua giao diện conmand line (CLI – cấp cao hơn REST API). ví dụ: `docker run <image>` ... và nhiều câu lệnh khác.

  + `Docker Toolbox:` đây là tool của Docker, được sử dụng  trên MacOS và Window

  + `Docker Trusted Registry (DTR):` Nếu công ti của bạn cần bảo mật và chỉ muốn chia sẻ image ở nội bộ, hoặc chỉ cho những ai bạn muốn chia sẻ. Thì DTR như một Docker Hub phiên bản private. (đây là phiên bản mất phí, bạn sẽ trả tiền, và sẽ có người của công ti support cho bạn trong việc cài đặt và bảo trì hệ thống)

  + `Docker Machine:` là công cụ giúp bạn cài đặt Docker Engine lên môi trường máy ảo một cách tự động, đồng thời cũng quản lí các host này với các câu lệnh của docker-machine (cái này hỗ trợ cả trên host thật, cloud…).

  + `Docker Swarm:` is native clustering for Docker (theo docs.docker.com). Hiểu nôm na nó như một thằng trung gian giữa bạn và các Docker Host, nó tập trung các Docker Host về thành một mối. Và khi bạn làm việc thì bạn chỉ cần làm việc với cái “virtual host” mà Swarm tạo ra là ổn.

  + `Docker Registry:` Chức năng tương tự như Docker Trusted Registry. Kho chứa images phiên bản open-sources (không mất phí).

  + `Docker Cloud:` là hệ thống Paas cho phép bạn dễ dàng triển khai các app của mình lên môi trường cloud.

## Docker system gồm những thành phần nào ?

+ Docker sử dụng kiến trúc client-server. Docker client sẽ liên lạc với các Docker daemon, các Docker daemon sẽ thực hiện các tác vụ build, run và distribuing các Docker container.  Cả Docker client và Docker daemon có thể chạy trên cùng 1 máy, hoặc có thể kết nối theo kiểu Docker client điều khiển các docker daemon giao tiếp với nhau thông qua socket hoặc RESTful API.

  - `Docker Daemon`

  Docker daemon chạy trên các máy host. Người dùng sẽ không tương tác trực tiếp với các daemon, mà thông qua Docker Client.

  - `Docker Client`

  Là giao diện người dùng của Docker, nó cung cấp cho người dùng giao diện dòng lệnh và thực hiện phản hồi với các Docker Daemon.

  - `Docker images`

  Là một template chỉ cho phép đọc, ví dụ một image có thể chứa hệ điều hành Ubuntu và web app. Images được dùng để tạo Docker container. Docker cho phép chúng ta build và cập nhật các image có sẵn một cách cơ bản nhất, hoặc bạn có thể download Docker images của người khác.

  - `Docker Container`

  Docker container có nét giống với các directory. Một Docker container giữ mọi thứ chúng ta cần để chạy một app. Mỗi container được tạo từ Docker image. Docker container có thể có các trạng thái run, started, stopped, moved và deleted.

## 5. Hoạt động của Docker ra sao ?

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
