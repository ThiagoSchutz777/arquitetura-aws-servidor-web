# arquitetura-aws-servidor-web
# Projeto de Arquitetura de Nuvem - Site Elástico na AWS

### Descrição do Projeto
Este projeto demonstra a construção e o deploy de um site estático usando uma arquitetura de alta disponibilidade, escalável e resiliente na AWS. A infraestrutura é totalmente automatizada, capaz de se recuperar de falhas e de se ajustar dinamicamente a picos de tráfego.

### Arquitetura
A arquitetura foi construída com o objetivo de ter redundância e alta disponibilidade em múltiplas Zonas de Disponibilidade.

### Serviços da AWS Utilizados
* **Amazon VPC:** Configuração de uma rede privada com sub-redes públicas.
* **Amazon EC2:** Servidores web para hospedar o conteúdo do site.
* **Elastic Load Balancer (ALB):** Distribuição de tráfego entre os servidores.
* **Auto Scaling Group:** Escalabilidade automática baseada em demanda.
* **Amazon S3:** Armazenamento do conteúdo do site.
* **AWS IAM:** Gerenciamento seguro de permissões para o acesso ao S3.
* **Launch Template:** Modelo para a automação da configuração de novas instâncias.

### Conceitos-Chave Demonstrados
* **Alta Disponibilidade:** A arquitetura é multizona, garantindo que o site permaneça online mesmo em caso de falha em uma Zona de Disponibilidade.
* **Escalabilidade Automática:** O sistema se ajusta à demanda do tráfego.
* **Tolerância a Falhas:** O Load Balancer e o Auto Scaling Group garantem a resiliência da aplicação.

### Como o Projeto Funciona
1.  O tráfego chega ao **Load Balancer**.
2.  O **Load Balancer** distribui o tráfego para as instâncias **EC2** que estão no **Auto Scaling Group**.
3.  O **Auto Scaling Group** gerencia a capacidade, garantindo que o número mínimo de instâncias seja mantido.
4.  Quando uma nova instância é iniciada, o script `user-data.sh` é executado, instalando o Apache2 e sincronizando o site do **S3**.
5.  O site é servido aos usuários.

O Script que fiz para automatizar a criação de novas instancias a partir de um modelo
#!/bin/bash

# Atualiza a lista de pacotes
sudo apt-get update -y
sudo apt-get upgrade -y

# Instala o servidor web Apache2
sudo apt-get install apache2 -y

# Instala o unzip e o curl (necessários para instalar o AWS CLI)
sudo apt-get install unzip curl -y

# Baixa o pacote de instalação do AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Descompacta o arquivo
unzip awscliv2.zip

# Instala o AWS CLI
sudo ./aws/install

# Habilita o Apache2 para iniciar automaticamente quando a instância for reiniciada
sudo systemctl enable apache2

# Inicia o serviço do Apache2
sudo systemctl start apache2

# Sincroniza os arquivos do seu bucket S3 para o diretório web do Apache2
sudo aws s3 sync s3://web-server-bkt  /var/www/html/
