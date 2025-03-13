# rag
Build A RAG FROM SCRACH

## Centos 安装docker(基于通义千问回答)
1. 查看centos 版本号

   cat /etc/centos-release

2. 卸载旧的docker

    sudo yum remove docker \
                    docker-client \
                    docker-client-latest \
                    docker-common \
                    docker-latest \
                    docker-latest-logrotate \
                    docker-logrotate \
                    docker-engine
3. 安装 yum 管理工具和安装Docker

    sudo yum install -y yum-utils

4. 添加Docker的官方仓库

    sudo yum-config-manager \
        --add-repo \
        https://download.docker.com/linux/centos/docker-ce.repo

5. 安装最新版本Docker引擎和contained

    sudo yum install docker-ce docker-ce-cli containerd.io

6. 启动Docker

    sudo systemctl start docker
    sudo systemctl enable docker

7. 使用国内镜像加速(解决了 ragflow拉取失败的问题)

    编辑或创建 /etc/docker/daemon.json 文件
    {"registry-mirrors": ["https://docker.1ms.run","https://n9lsncgn.mirror.aliyuncs.com"]}


## ragflow 安装(ragflow 41G 左右, + docker + ollama + deepseek-r1:1.5b MEMORY 15左右)
    参考链接: https://ragflow.io/docs/dev/
    * CPU ≥ 4 cores (x86);
    * RAM ≥ 16 GB;
    * Disk ≥ 50 GB;
    * Docker ≥ 24.0.0 & Docker Compose ≥ v2.26.1.

1. ensure vm_max_map_count
    ```
    sysctl vm.max_map_count
    sudo sysctl -w vm.max_map_count=262144
    ```
2. clone the repo:
    ```
    $ git clone https://github.com/infiniflow/ragflow.git
    $ cd ragflow/docker
    ```
3. Use the pre-built Docker images and start up the server:

    $ docker compose -f docker-compose.yml up -d

    注意 .env 配置的修改 RAGFLOW_IMAGE 

4. 重启Docker服务使配置生效

    sudo systemctl daemon-reload
    sudo systemctl restart docker

# Docker 安装ollama
    (curl -fsSL https://ollama.com/install.sh | sh) [官网](https://ollama.com/download/linux)命令安装速度巨慢,改为docker安装

    [参考地址:](https://github.com/ollama/ollama/blob/main/docs/docker.md)

1. CPU only
    ```
    docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
    docker exec -it ollama ollama pull deepseek-r1:1.5b
    docker exec -it ollama ollama run deepseek-r1:1.5b
    ```
    参考地址: (https://ragflow.io/docs/dev/deploy_local_llm)
    Ensure ollama is listening on all IP address:
    ```
    sudo ss -tunlp |grep 11434
    ```
    Ensure Ollama is accessible

        ```
        sudo docker exec -it ragflow-server bash 
        
        ```
        then curl http://host.docker.internal:11434/
        Ollama is running