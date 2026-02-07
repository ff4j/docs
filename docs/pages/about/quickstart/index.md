# Quickstart

This page will guide you through the process of showcasing how to quickly start FF4j demo application.

## With docker

If you are reading this section, then the assumption is that you have docker installed on your
machine. The idea is to showcase a sample spring boot application that is using FF4j for feature
management. This will help you to understand how to use FF4j in a real world application and also to
get a feel of how it works.

!!! info "Pre-requisite"

      We assume you have docker or podman installed in your machine.

1. Run by pulling the latest image from docker hub

      ```shell title="Cammand to run ff4j sample spring boot application"
      docker run -p 8080:8080 ff4j/ff4j-sample-springboot2x:1.8.5
      ```

      ```shell title="Expected output in the terminal"
               _____  _____  _____     __ 
       _/ ____\/ ____\/  |  |   |__|
       \   __\\   __\/   |  |_  |  |
        |  |   |  | /    ^   /  |  |
        |__|   |__| \____   /\__|  |
                         |__\______|
      
      Brought to you by FF4j Developments team
      If you like us, consider to give a ⭐ on github, that helps s! https://github.com/ff4j/ff4j/stargazers
      The application should be available on http://localhost:8080
      15:30:17.803 INFO  org.ff4j.sample.Application                        : Starting Application v1.8.5 on 9a94abfcef56 with PID 1 (/app.jar started by root in /)
      15:30:17.810 INFO  org.ff4j.sample.Application                        : No active profile set, falling back to default profiles: default
      15:30:21.827 INFO  org.ff4j.sample.HomeController                     :  + Features and properties have been created for the sample.
      15:30:24.181 INFO  org.ff4j.sample.Application                        : Started Application in 7.275 seconds (JVM running for 8.759)
      ```

2. Access the application locally at http://localhost:8080/

> It's that simple! You have successfully started a sample FF4j spring boot application.

## With spring boot

If you are reading this section, then the assumption is that you have a spring boot application and
you want to integrate FF4j for feature management. The demo showcases a spring boot application that
uses [ff4j-spring-boot-starter-webmvc](https://github.com/ff4j/ff4j-spring-boot-starter-parent?tab=readme-ov-file#ff4j-spring-boot-starter-webmvc)
and boots the application. 

!!! info "Pre-requisite"

      We assume you have the following pre-requisites:

      - Java 17 or higher
      - Maven 3.6 or higher
      - Git


1. Clone the repository

      ```shell title="Command to clone the repository"
      git clone https://github.com/ff4j/ff4j-demo.git
      ```
   
2. Start the application

      ```shell title="Command to start the application"
      cd ff4j-demo/ff4j-sample-springboot2x
      mvn spring-boot:run
      ```
   
3. Access the application locally at http://localhost:8080/