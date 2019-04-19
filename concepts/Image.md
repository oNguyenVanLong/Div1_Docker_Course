# Bich

- Trước hết project cần có ít nhất là Dockerfile

- Lệnh sau

  ```shell
  docker build --tag=depzai .
  # hoặc
  docker build --tag=depzai:pro . # mặc định tag = latest
  ```

  sẽ tiến hành build (giống như việc ta loay hoay ngồi cài môi trường bằng cơm vậy) project theo đúng trình tự ta viết trong Dockerfile.

- Tưởng tượng như khi nấu ăn, `Dockerfile` giống như công thức để nấu những món ăn ngon vậy

  Tuy nhiên cũng có trường hợp `Dockerfile` nát như tương, có rất nhiều câu lệnh thừa, chưa cache, ....

- Để xem danh sách image có trên máy ta:

  ```shell
  docker image ls
  ```

- Để xem thông tin chi tiết về ~~sản phẩm~~ image, vui lòng bấm:

  ```shell
  docker image inspect
  ```

- Xóa image đi, ta có thể dùng lệnh

  ```shell
  docker rmi <image_hash>
  ```

  `image_hash` có thể lấy từ lệnh ls trên.

  Một vài trường hợp image đang được sử dụng thì có thể thêm option `--force` hay gọn hơn là `-f
