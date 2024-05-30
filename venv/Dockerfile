# Usar la imagen base oficial de Jenkins LTS
FROM jenkins/jenkins:lts

# Ejecutar Jenkins como root para instalar paquetes adicionales si es necesario
USER root

# Instalar sudo, wget y otros paquetes necesarios
RUN apt-get update && \
    apt-get install -y sudo apt-transport-https \
                       ca-certificates \
                       curl \
                       gnupg2 \
                       software-properties-common \
                       wget && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - && \
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" && \
    apt-get update && \
    apt-get install -y docker-ce-cli && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN groupadd docker
RUN usermod -aG docker jenkins

# Instalar Python y herramientas relacionadas
RUN apt-get update && \
    apt-get install -y python3 \
                       python3-pip \
                       python3-venv && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Crear un entorno virtual para Python y instalar pytest
RUN python3 -m venv /opt/venv && \
    /opt/venv/bin/pip install --upgrade pip && \
    /opt/venv/bin/pip install pytest

# Instalar Terraform
RUN wget -O terraform.zip https://releases.hashicorp.com/terraform/1.0.7/terraform_1.0.7_linux_amd64.zip && \
    unzip terraform.zip && \
    mv terraform /usr/local/bin/terraform && \
    rm terraform.zip
	
# Instalar AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf ./aws awscliv2.zip

RUN echo "jenkins ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

RUN usermod -aG sudo jenkins

# Cambiar de nuevo al usuario jenkins
USER jenkins

# Configurar entorno virtual en PATH
ENV PATH="/opt/venv/bin:$PATH"

# Exponer el puerto 8080 para la interfaz web de Jenkins y el puerto 50000 para los agentes
EXPOSE 8080
EXPOSE 50000