## Ứng dụng Docker trong framework Ruby On Rails

![](https://user-images.githubusercontent.com/49421807/60308227-1ccc8e00-9972-11e9-8227-e9879d3e3a06.png)

[Quickstart: Compose and Rails](https://docs.docker.com/compose/rails)

Tối ưu:

[1. Add spring container](https://github.com/jonleighton/spring-docker-example)

+ Step 1: Add gem, then install it
  ```
  $ gem 'spring-commands-rspec'
  $ gem 'spring-commands-rubocop'
  ```

  ```
  $ bundle install
  $ bundle exec spring binstub --all
  ```

+ Step 2: Add service to docker-compose.yml
  ```
    spring:
      container_name: spring
      build: .
      volumes:
        - .:/app
      restart: always
      command: bundle exec spring server
      # This ensures that the pid namespace is shared between the host
      # and the container. It's not necessary to be able to run spring
      # commands, but it is necessary for "spring status" and "spring stop"
      # to work properly.
      pid: host
  ```

+ Step 3: Config

  ```
  config/spring_client.rb
  ENV["SPRING_SOCKET"] = "tmp/spring.sock"
  ```

+ Note:

  Hãy luôn giữ cho container spring running


[2. Use Makefile](https://github.com/longnv-0623/Div1_Docker_Course/blob/master/source_code/Makefile)
