FROM maven:3.6.3-jdk-11 as mavenbuilder
WORKDIR /home/ubuntu
RUN apt-get update
RUN apt-get install -y git
RUN git clone https://github.com/Sudhamshetty7/hello-world-war.git /home/ubuntu
RUN mvn clean package

FROM tomcat:9.0
COPY --from=mavenbuilder /home/ubuntu/target/*.war /usr/local/tomcat/webapps/
EXPOSE 8080
