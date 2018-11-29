# 使用Python官方镜像作为镜像的基础
FROM python:2.7-slim

# 设置工作空间为/app
WORKDIR /app

# 把当前目录下的文件拷贝到 容器里的/app里
ADD . /app

# 安装requirements.txt中指定的依赖
RUN pip install -r requirements.txt

# 开放80端口
EXPOSE 80

# 设置 NAME 这个环境变量
ENV NAME World

# 当容器启动时，运行app.py
CMD ["python", "app.py"]

# --------------------- 
# 作者：无始之名 
# 来源：CSDN 
# 原文：https://blog.csdn.net/u011499747/article/details/73693384 
# 版权声明：本文为博主原创文章，转载请附上博文链接！
