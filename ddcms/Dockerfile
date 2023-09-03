# 使用 CentOS 7.9 作为基础镜像
FROM centos:centos7.9.2009

ARG DB_USERNAME=""
ARG DB_PASSWORD=""
ARG DB_NAME=""
ARG SECRET=""
ARG USER_NAME=""
ARG USER_PASSWORD=""
ARG PRIVATE_KEY=""
ARG ACCOUNT_ADDRESS=""
ARG PRODUCT_ADDRESS=""
ARG DATA_SCHEMA_ADDRESS=""
ARG NODE0=""
ARG NODE1=""

# 复制 JDK 8 的离线压缩包到容器中
ADD jdk-8u281-linux-x64.tar.gz /usr/local/
ADD DDCMS-Service/ /opt/DDCMS-Service/
# 解压 JDK 压缩包并配置 Java 环境
RUN mv /usr/local/jdk1.8.0_281 /usr/local/java
ENV JAVA_HOME=/usr/local/java
ENV PATH=$PATH:$JAVA_HOME/bin

# 清理临时文件
RUN rm -f /tmp/jdk-8u281-linux-x64.tar.gz

# 配置DDCMS-Service
WORKDIR /opt/DDCMS-Service/
RUN ./gradlew bootJar
RUN cd dist/conf/ && cp config-example.toml config.toml && \
         cp application-example.yml application.yml
RUN sed -i "s/username: test/username: $DB_USERNAME/g" /opt/DDCMS-Service/dist/conf/application.yml && \
	sed -i "s/dbName/$DB_NAME/g" /opt/DDCMS-Service/dist/conf/application.yml && \
	sed -i "s/127.0.0.1/ddcms-mysql/g" /opt/DDCMS-Service/dist/conf/application.yml && \
	sed -i "s/admin-password: ''/bak/g" /opt/DDCMS-Service/dist/conf/application.yml && \
	sed -i "s/password:/password: $DB_PASSWORD/g" /opt/DDCMS-Service/dist/conf/application.yml && \ 
	sed -i "s/secret: \"\"/secret: '$SECRET'/g" /opt/DDCMS-Service/dist/conf/application.yml && \ 
	sed -i "s/admin-private-key: ''/admin-private-key: \"$PRIVATE_KEY\"/g" /opt/DDCMS-Service/dist/conf/application.yml  &&  \
	sed -i "s/account-contract: \"\"/account-contract: \"$ACCOUNT_ADDRESS\" /g" /opt/DDCMS-Service/dist/conf/application.yml  &&  \
	sed -i "s/product-contract: \"\"/product-contract: \"$PRODUCT_ADDRESS\"/g" /opt/DDCMS-Service/dist/conf/application.yml && \
	sed -i "s/dataSchema-contract: \"\"/dataSchema-contract: \"$DATA_SCHEMA_ADDRESS\"/g" /opt/DDCMS-Service/dist/conf/application.yml  && \
	sed -i "s/bak/admin-password: '$USER_PASSWORD'/g" /opt/DDCMS-Service/dist/conf/application.yml && \ 
	sed -i "s/127.0.0.1:20200/$NODE0:20200/g" /opt/DDCMS-Service/dist/conf/config.toml && \
	sed -i "s/127.0.0.1:20201/$NODE1:20201/g" /opt/DDCMS-Service/dist/conf/config.toml
ADD ca.crt dist/conf/ca.crt
ADD sdk.crt dist/conf/sdk.crt
ADD sdk.key dist/conf/sdk.key

EXPOSE 10880
WORKDIR /opt/DDCMS-Service/dist
# 创建自定义启动脚本
CMD ["java", "-jar", "ddcms_0.0.1-SNAPSHOT.jar", "--spring.config.name=application", "--spring.config.location=classpath:/,file:/opt/DDCMS-Service/dist/conf/"]
