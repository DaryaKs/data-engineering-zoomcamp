# Docker Workflow, Git Integration, and Related Topics

This document provides a comprehensive summary of Docker workflows, Git integration, and additional technical insights based on the lecture.

---

## 1. Почему после удаления всех файлов с помощью `rm -rf /` и выхода из контейнера, при повторном запуске `docker run` всё возвращается?

Когда вы выполняете команду `docker run`, Docker использует базовый **имидж**, чтобы создать **новый контейнер**. Каждый раз, когда вы запускаете `docker run`, создаётся **абсолютно новый контейнер** на основе того же неизменённого имиджа.

- **Имидж** — это неизменяемый шаблон, который служит основой для создания контейнеров.
- **Контейнер** — это изолированная среда, созданная из имиджа. Изменения, которые вы вносите в контейнер, не затрагивают исходный имидж.

Когда вы выполняете `rm -rf /` в контейнере, вы удаляете файлы **только в этом контейнере**, а не в имидже. После выхода и повторного запуска команды `docker run`, Docker создаёт новый контейнер из **исходного имиджа**, который остаётся нетронутым.

---

## 2. Почему каждый запуск `docker run -it ubuntu bash` создаёт новый контейнер?

Да, каждый запуск `docker run` создаёт **новый контейнер**. Например:

```bash
docker run -it ubuntu bash
```
Каждый раз, когда вы запускаете эту команду:

1. Docker берёт базовый имидж ubuntu.
2. Создаёт новый изолированный контейнер на его основе.
3. Запускает команду /bin/bash внутри этого нового контейнера.

Для проверки можно использовать:

```bash
docker ps -a
```

Эта команда покажет все созданные контейнеры. Вы заметите, что каждый запуск docker run добавляет новый контейнер в список.

## 3. Как отличать контейнеры друг от друга?
Чтобы отличать контейнеры, можно использовать:

1. Имена контейнеров

- Docker автоматически назначает случайные имена контейнерам, но вы можете задать своё имя с помощью флага --name:
```bash
docker run -it --name my_container ubuntu bash
```
- После этого вы сможете запускать контейнер по имени:
```bash
docker start -ai my_container
```

2. Идентификаторы контейнеров (ID)

- У каждого контейнера есть уникальный ID, который можно найти с помощью:
```bash
docker ps -a
```

## 4. Если я сделала rm -rf / и вышла из контейнера, могу ли я снова открыть этот же контейнер?
Да, вы можете открыть тот же самый контейнер, если не удалили его. Для этого:

1. Найдите ID или имя контейнера:
```bash
docker ps -a
```

2. Запустите его:
```bash
docker start -ai <имя_или_ID_контейнера>
```

3. Если вы удалили ключевые системные файлы, контейнер может не запуститься, так как он станет неработоспособным.

## 5. Нужно ли делать commit, чтобы сохранить изменения?
- Если вы хотите сохранить внесённые изменения в контейнере (например, установили пакеты или удалили файлы), необходимо выполнить docker commit:
```bash
docker commit <имя_или_ID_контейнера> новый_имидж
```
- Это создаст новый имидж, который сохранит текущее состояние контейнера. Вы можете использовать этот имидж для создания новых контейнеров с сохранёнными изменениями.

## 6. Если я не сделала commit, сохранятся ли изменения в контейнере?
Изменения сохраняются в контейнере, пока вы его не удалите. Даже если вы выйдете из контейнера (exit), его текущее состояние сохраняется. Вы можете снова войти в контейнер с помощью:

```bash
docker start -ai <имя_или_ID_контейнера>
```
Однако, если вы удалите контейнер (docker rm), все изменения, внесённые в нём, будут утеряны.

## 7. Разница между docker run ubuntu и docker run -it ubuntu bash:
- docker run ubuntu
- - Запускает контейнер из образа ubuntu и выполняет команду, определённую в образе (обычно sh или ничего).
- - Контейнер завершается сразу после выполнения команды.

- docker run -it ubuntu bash
- - Запускает контейнер с включённым интерактивным режимом (-it) и командой /bin/bash.
- - Вы получаете доступ к терминалу контейнера.

# Docker Workflow and Key Commands
## Overview of Commands and Concepts

1. docker run image-name:

- Downloads the image (if not already available locally).
- Creates a new container from the image.
- Executes the default command defined in the image.
- If the default command is short-lived, the container stops after execution.

2. docker start container-name:

- Restarts an existing container that was previously created (and possibly stopped).
- Does not create a new container; reuses an old one with its existing state.
- By default, it runs in the background unless attached with -a.

3. docker run -it image-name:

- Downloads the image (if necessary), creates a new container, and starts it in interactive mode.
- The -it flag provides an interactive terminal, allowing direct interaction with the container.
- When you type exit, it stops the container.

4. Changes in the Container:

- Any changes made within a container (e.g., installing software, modifying files) apply only to that specific container and are not saved back to the original image.
- To preserve changes, you can commit the container to a new image using:
```bash
docker commit container-name new-image-name
```

- Important Note: Destructive changes, such as deleting critical parts of the filesystem (e.g., rm -rf / --no-preserve-root), will apply immediately to the container and render it unusable.

- - What does --no-preserve-root mean? Normally, the rm -rf / command protects the root directory from being deleted as a safeguard. The --no-preserve-root option disables this safeguard, allowing the deletion of the entire root filesystem, including critical directories like /bin, /lib, and /etc.

- - After running such a command, essential components required to start or interact with the container will be missing. Attempting to restart the container will fail because these components are critical for its operation.

5. Reversing or Recovering Changes:

- "They cannot be reversed without committing or restoring from an image" means that changes (including destructive ones) are isolated to the container and do not affect the original image. However, once a container is damaged, the only way to "reverse" the state is:

- 1. By creating a new container from the original image.

- 2. By committing the damaged container (if possible) and attempting to repair it.
Example Recovery Workflow:

- 2. - If you accidentally damage the container, you can commit its state (even if damaged):
```bash
docker commit container-name damaged-image
```

- 2. - Run a new container from the committed image to inspect and repair:
```bash
docker run -it damaged-image /bin/sh
```

- 2. - Alternatively, if you have a working backup image, simply create a new container:
```bash
docker run -it backup-image /bin/bash
```

6. Where Docker Stores Images:

- Docker stores images in a central directory managed by the Docker daemon. Common locations are:

- - Linux: /var/lib/docker

- - Mac (Docker Desktop): Inside a virtual machine (accessible via Docker Desktop settings).

- - Windows (Docker Desktop): Inside a virtual machine (accessible via Docker Desktop settings).

- Within this directory, images are stored using the overlay2 storage driver (or another driver based on your configuration).

- How Docker Uses Local Images:

- - The first time you run docker run image-name, the image is downloaded from a remote registry and stored locally.

- - Subsequent runs of docker run image-name use the locally stored image without re-downloading it.

- - This ensures that containers created from the same image are independent of one another, as changes in one container do not affect the original image or other containers.

7. docker stop container-name:

- Stops a running container gracefully (sending a SIGTERM signal).

# Using a Dockerfile

# # Logic of a Dockerfile

A Dockerfile defines a series of instructions to build a Docker image. Here's the typical structure:

1. Base Image:
```bash
FROM python:3.9
```

2. Set Working Directory:
```bash
WORKDIR /app
```

3. Copy Files:
```bash
COPY . /app
```

4. Install Dependencies:
```bash
RUN pip install --no-cache-dir pandas psycopg2
```

5. Expose Ports (Optional):
```bash
EXPOSE 5000
```

6. Define the Default Command:
```bash
CMD ["python", "app.py"]
```

## File Location and Structure

- Place the Dockerfile in the root of your project.

- Example structure:
```bash
my_project/
├── Dockerfile
├── app.py
├── requirements.txt
└── other_files/
```

## Building and Running the Image

- Build the image:
```bash
docker build -t my-python-app .
```

- Run the container:
```bash
docker run -it my-python-app
```

# Docker Compose

## What is docker-compose up?

docker-compose up is a command to start and run multi-container applications defined in a docker-compose.yml file. It:

- Builds images if necessary.
- Starts all services defined in the file.
- Manages networking between containers.

Example docker-compose.yml File
```bash
version: '3.9'
services:
  app:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
    volumes:
      - .:/app
    command: python app.py
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydatabase
    ports:
      - "5432:5432"
```

Usage

1. Start services:
```
docker-compose up
```

2. Stop services:
```bash
docker-compose down
```

# Git Integration

## Connecting VS Code to a GitHub Repository

1. Install Git:
```bash
git --version
```
Install it if not available.

2. Configure Git:
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

3. Clone your repository:
```bash
git clone <repository-url>
```

4. Configure VS Code for Git:

- Install the Git extension in VS Code.
- Open your repository folder in VS Code.
- Use the Source Control panel to commit, push, and pull changes.

Creating Empty Folders

Git does not track empty folders. To include a folder, create a placeholder file:
```bash
touch folder_name/.gitkeep
```

Then add, commit, and push:
```bash
git add folder_name/.gitkeep
git commit -m "Add empty folder folder_name"
git push
```

# Understanding Bash

- What is Bash?

- - Bash is a command-line shell used to interact with the operating system.

- - It allows running commands, managing files, and installing packages.

- Why Use Bash in Docker?

+ Containers like python:3.9 start directly into Python by default. Using Bash (--entrypoint /bin/bash) gives full control to execute system-level commands, such as:
```bash
pip install pandas psycopg2
```

# Container Persistence and Reuse

- Running:
```bash
docker run -it python:3.9
```
Creates a new container each time.

- To reuse an old container:
```bash
docker start -ai container_name
```
You cannot override --entrypoint with docker start.

- Run with override --entrypoint:
```bash
docker run -it --entrypoint=bash python:3.9
```

# Running Windows on macOS with Docker

Docker on macOS cannot natively run Windows containers because they require the Windows kernel. However, Linux images like Ubuntu work because Docker Desktop uses a lightweight Linux VM.

# Example Debugging Session

print('hello world')
hello world
import os
print(os.environ)
help(os)

# Differences Between docker run ubuntu and **docker run -it ubuntu bash

- **docker run ubuntu** runs the default command (non-interactive).

- **docker run -it ubuntu bash** provides an interactive Bash shell.
