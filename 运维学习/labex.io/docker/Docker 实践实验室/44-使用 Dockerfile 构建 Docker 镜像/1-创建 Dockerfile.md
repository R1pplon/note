# 创建 Dockerfile

在此步骤中，你将创建一个 Dockerfile 来定义航天器软件系统的环境。

1. 在 `~/project` 目录中创建一个名为 `Dockerfile` 的新文件。
2. 将以下内容添加到 `Dockerfile`：

```dockerfile
# Use an official Python runtime as the base image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

3. 在 `~/project` 目录中创建一个 `requirements.txt` 文件。添加航天器软件所需的 Python 包。
