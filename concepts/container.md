# Bich

- OK ta build xong image rồi, vậy giờ làm gì với nó?

- Từ một image, ta có thể sử dụng lệnh `docker run` để chạy nó

  Tuy nhiên ta không chạy trực tiếp những image, vì image chỉ có thể đọc (read-only)

  Thực tế ta đang tạo ra nhiều instance khác nhau, hoạt động 1 cách độc lập (gọi là container)

  Giống như trong Ruby, ta viết `class` vậy, xong ta dùng `.new` để tạo ra những instance của class.

  Những instance này tất nhiên sẽ có những dữ liệu mà image có, tuy nhiên khi chạy, chúng sinh ra dữ liệu mới (thay đổi state) thì hoàn toàn độc lập, chả liên quan gì với nhau.

- Hãy cùng chạy một image:

  ```shell
  docker run -it -p 3000:3000 depzai
  ```

- Kiểm tra trạng thái container

  ```shell
  docker ps
  ```

- Hết giờ đi về, cần tắt container đi thì ta dùng

  ```shell
  docker stop container 		# dừng container lại
  docker start container 		# Hôm sau đến bật lại container đó
  docker kill container 		# Ngứa mắt kill luôn
  docker restart container 	# Khi sửa file config, cần restart lại server
  ```

- `docker commit`

  Image read-only, không thể thay đổi được, tuy nhiên ta lại muốn lưu trạng thái của 1 container đang chạy lại, để từ đó tạo ra nhiều instance mới. Câu lệnh này giúp ta làm điều đó, nó sẽ tạo ra 1 image mới chứa toàn bộ dữ liệu của container lại thời điểm đó

  Từ đó ta có một cách hiểu mới về container và image:

  > Image là những snapshot, immuatable (bất biến, read-only) của container. Container là những running (hoặc stopped) instance của image

  Tham khảo từ https://stackoverflow.com/a/23667302
