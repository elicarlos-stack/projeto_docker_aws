# 🙋‍♂️ Olá, meu nome é Elicarlos, Bem-vindo ao Meu Perfil no GitHub! 

## Sobre Mim
Sou estudante de Segurança da Informação, e atualmente estou desenvolvendo habilidades em Linux, AWS e automação de processos na Compass.Uol.

## Projetos
- DevSecOps - Docker e Wordpress na AWS.

## Contato
- <a href="https://www.linkedin.com/in/elicarlos-amorim"><img src="https://github.com/user-attachments/assets/f29b36c0-ca83-4864-8e95-d8d27506b274" width="20"> LinkedIn</a>
- <a href="https://github.com/elicarlos-stack"><img src="https://github.com/user-attachments/assets/e2064dea-f32c-4782-a75d-2273f010e24f" width="20"> GitHub</a>

<hr>

<div align="center">
 <img src="https://github.com/user-attachments/assets/eeb8aec4-21cc-454a-9c00-81d2b52d1ded" width="500px" hight="400">

 <img src="https://github.com/user-attachments/assets/5f1ff0b7-4dce-4a0b-b0f9-11de88c85c44" width="500px" hight="400">
</div>

# <h1 align="center">Configuração de Servidor Web com Monitoramento</h1>

<p align="center"><img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=GREEN&style=for-the-badge"/></p>


## :pushpin: Descrição do projeto:
<div style="text-align: justify">
DevSecOps - Docker</div>

## :hammer: Ferramentas e Tecnologias Utilizadas:
- **Linux (Debian12)**
- **AWS Cloud**
- **Vscode**
- **Docker**
- **Wordpress**

# Índice

1. [Etapa 1: Configuração do Ambiente](#etapa-1-configuração-do-ambiente)
2. [Configuração do Servidor](##etapa-2-configuração-do-servidor)
3. [Monitoramento e Notificações](##etapa-3-monitoramento-e-notificações)
4. [Automação e Testes](##etapa-4-automação-e-testes)


# Etapa 1: Configuração do Ambiente :hammer_and_wrench:
Antes de criarmos a nossa Instância EC2 primeiramente vamos criar uma VPC na AWS com 2 subnets públicas e 2 subnets privadas. A VPC é um serviço da AWS que permite criar uma rede virtual isolada dentro da infraestrutura da AWS, para isto com o console aberto da Amazon AWS localize na barra de pesquisar por VPC para criar a VPC logo após criaremos as subnets, depois clique em create VPC ou selecione Your VPCs (suas VPCs) e criar VPC.


<img src="img/img_vpc1.png" alt="Imagem inicial EC2" width="800px" hight="600px" />


Em VPC setting você pode escolher por criar através do VPC only ou VPC and more (Este modo permite criar a VPC junto com recursos adicionais necessários, como sub-redes, tabelas de rotas, gateways da Internet e gateways NAT). Neste exemplo escolheremos VPC only para criar de forma manual, após escolher informe um nome para vpc ex: "wp-docker-dev" e o bloco IPV4 CIDR para a VPC neste exemplo usei 172.18.0.0/20. as outras opções pode deixar como default e finalize clicando em criar VPC (Create VPC).

<img src="img/img_vpc2.png" alt="" width="800px" 
hight="600px" />
<img src="img/img_vpc3.png" alt="" width="800px" 
hight="600px" />


<div>Agora criaremos 2 subnets privadas e 2 subnets públicas conforme a imagem abaixo. Para isso, na console da AWS, vá para VPC e clique em Subnets -> Create Subnet. Selecione a VPC criada anteriormente com o nome "wp-docker-dev". Crie cada uma das subnets com os seguintes nomes: "wp-docker-dev-public01", "wp-docker-dev-public02", "wp-docker-dev-private01" e "wp-docker-dev-private02". Selecione a zona de disponibilidade para cada subnet, preferencialmente uma em cada zona. Informe o bloco CIDR da subnet IPv4. para criar outras subnets basta clicar adicionar nova subnet (em Add new subnet).</div>

- wp-docker-dev-public01 = 172.18.0.0/24
  
- wp-docker-dev-public02 = 172.18.1.0/24
  
- wp-docker-dev-private01 = 172.18.8.0/24
  
- wp-docker-dev-private02 = 172.18.9.0/24

> [!NOTE]
> As tags são opcionais, mas recomenda-se atribuir nomes a elas, pois ajudam a categorizar e organizar seus recursos na AWS.

<div>
<img src="img/img_sub1.png" alt="" />

<img src="img/img_sub2.png" alt="" />

<img src="img/img_sub3.png" alt="" />

</div>

Na imagem abaixo, você pode ver as subnets que foram criadas. Elas são duas subnets privadas e duas subnets públicas, nomeadas como "dev-web-public01", "dev-web-public02", "dev-web-private01" e "dev-web-private02", cada uma associada a uma zona de disponibilidade diferente.<br>

<img src="img/img_sub4.png" alt="" />

Quando uma VPC é criada, uma tabela de roteamento já vem configurada por padrão. No entanto, para que nossas subnets funcionem como subnets públicas, criaremos uma nova tabela de roteamento específica para as subnets públicas.

primeiramente criaremos um internet gateway. ir em internet gateway, informe um nome para seu internet gateway por exemplo "igw-wp-docker01" e clique em create internet gateway. apos a criação teremos que associar a uma vpc.

<img src="img/igw-wp-docker01.png" alt="" />

para associar selecione o internet gateway criado -> actions -> attach to VPC -> selecione a VPC e assossiar.

<img src="img/igw-wp-docker01-2.png" alt="" />

Agora, vamos criar a tabela de roteamento. Acesse a seção "Route Tables" na console da AWS e clique em "Create Route Table". Informe um nome, por exemplo, "rtb-docker-public", selecione a VPC "wp-docker-dev" e clique em "Create Route Table".

<img src="img/rtb-docker-public.png" alt="" />

Após a criação da tabela de roteamento, com a tabela de roteamento selecionada, vá para a aba "Subnet Associations". Clique em "Edit Subnet Associations", selecione as subnets públicas criadas e salve as alterações.

<img src="img/associate-subnet.png" alt="" />
<img src="img/associate-subnet1.png" alt="" />

Agora em Routes, vamos editar a tabela de roteamento (Edit Routes). Clique em Edit e adicione uma nova rota. Para isso, clique em Add Route, insira 0.0.0.0/0 no campo Destination e selecione o Internet Gateway criado anteriormente no campo Target. Salve as mudanças.

<img src="img/edit-routes-public.png" alt="" />



--- fazer depois NAT Gateway -----

Agora para criar o NAT gateway para a subnet privada ter acesso a internet. para isto ir em VPC no menu lateral em NAT gateway clique criar NAT gateway, informe um nome, selecione uma subnet public, aloque um Elastic Ip allocation ID. 
logo apos ir em Rote tables para criar a tabela de roteamento para o NET gateway e assossiar as subnets privadas.

De um nome para a route table privada exemplo: "rtb-private-wordpress" selecione a nossa vpc "wp-docker-dev"

<img src="img/rtb_private.png" alt="" />

Agora assossiar a subnet privadas.

<img src="img/associate_private.png" alt="" />

Agora iremos editar em Routes para que elas tenham acesso a internet. ir em "edite routes" adicionar a rota para NAT gateway.

<img src="img/associate_private1.png" alt="" />


---- FIM Nat Gateway -----

Vamos criar um grupo de segurança(Security group), nosso grupo de segurança irá atua como firewall virtual para as instâncias do EC2 para controlar o tráfego de entrada e de saída. 
Para criar na console da AWS pesquise por EC2 ou digite na barra de pesquisa, procure por "Network & Security na lateral esquerda depois "criar security group" (Create security group), 
Informe um nome, descrição é opcional e clique em criar.
<br>
<img src="img/securityGP.png" alt=""><br>
<img src="img/securityGP1.png" alt=""><br>
<img src="img/securityGP2.png" alt=""><br>





# Criar o RDS 
para criar o RDS no console da AWS pesquise por database, mostrar mais (show more) escolha e clique m Aurora and RDS
"create database", deixe como standard create, escolha o banco MySQL, em templates pode escolher Free tier 
em "Availability and durability" selecione "Single-AZ DB instance deployment" sem A-Z.
em "Settings" nome da instancia do banco de dados de um nome exemplo: db-wordpress, em "Credential Settings" deixe como admin, "self managed" digite uma senha e confirme a senha no campo abaixo. em "Instance configuration" escolha "burstable classes" e selecione o tipo "db.t3.micro", deixe "Storage" como padrão, "virtual private cloud(VPC) selecione a vpc criada: wp-docker-dev.
as outras configurações deixe padrão, em "Aditional configuration" database options coloque um nome para o banco de dados exemplo: site_wordpress. logo após cheque as informações e clique em criar banco de dados.

na janela a seguir aguarde a confirmação da criação do banco.




# Criar um novo grupo de segurança para o RDS para uma organização da infraestrutura:
Essa abordagem é útil para separar permissões e gerenciá-las de forma mais estruturada.
siga os passos. acesse o console do AWS EC2. No menu lateral em "Security" selecinar "Security groups".
Crie um novo grupo de segurança, por exemplo crie com o nome: my-rds-connection.
selecione a VPC em que nossa instancia se encontra com o nome "wp-docker-dev".

Adicione as seguintes regras de entrada ao grupo de segurança em "Inbound" 
Tipo: MySQL/Aurora (porta 3306). Em Origem: Selecione o grupo de segurança da sua instância EC2 para permitir que ela se conecte ao RDS. 
Em "Outbounds" regras de saida deixe padrao por enquanto. clique em criar grupo de segurança.

Importante lembre-se de:
Garantir que tanto o RDS quanto a instância EC2 estejam na mesma VPC para que possam se comunicar.
Se você estiver acessando o RDS de fora (como do seu computador local), será necessário adicionar o IP público ou 0.0.0.0/0 às regras de entrada temporariamente (não recomendado).



## :heavy_check_mark: Criar uma instancia EC2 :computer:

Para criar uma instância EC2 na AWS, acesse a console da AWS e pesquise por EC2 ou vá até "Instances". Em seguida, procure por "Launch instance".

<img src="img/img_ec2.png" alt="" />
<img src="img/ec2_instance1.png" alt="" />

Nesta etapa, adicione as Tags, caso necessário. Escolha a imagem AMI; neste projeto, usaremos o Ubuntu Server 24.04. No tipo da instância, deixe como t2.micro.Crie uma key pair para acesso remoto via SSH. Selecione a VPC "wp-docker-dev" criada anteriormente e a subnet pública. Em Security Group, crie um caso não tenha. Para criar, vá em EC2 Security Group, clique em Create Security Group, informe um nome; por exemplo, "my-wp-dev". A descrição pode ser adicionada para facilitar a identificação, se necessário. Selecione a VPC "wp-docker-dev" e clique em Create Security Group. Deixe as outras opções como padrão por enquanto e clique em "Create Instance" (Launch Instance).

<img src="img/ec2_instance2.png" alt="" />

Em "Key pair" caso nao tenha criado uma chave clique para criar, usaremos esta chave para conectarmos a instancia via SSH. Escolha um nome para achave deixe o tipo criptografia como padrão (RSA) e formato do arquivo como <b>.pem</b> conforme a imagem abaixo e clique em criar chave (create key pair).
<img src="img/chave.png" alt="" />

Verifique se as informações estão corretas e clique em criar instância (Launch instance). 

<img src="img/ec2_instance3.png" alt="" />

<div>Agora para associar um Security Group que permita tráfego HTTP (porta 80) e SSH na (porta 22) 
edite o security group permitindo regras de entrada de SSH e HTTP. 
Em security group selecione o security group criado "MyDevWeb" na aba Inbound rules 
clique em Edite inbound rules. adicione o type como HTTP, source selecione MyIP, 
adicione novamente para type SSH source MyIP e Salve(save rules).</div>

<br>

❗ (Obs: como o IP da instância está publico ao selecionar o Source como MyIP pode estar diferente da imagem mostrada aqui).

<br>

<img src="img/securitygroupsh.png" alt="" />

Apos isto realize o teste acessando o servidor via SSH. Abra um cliente SSH como o PUTTY ou o VSCODE utilizando o terminal bash instalado. neste exemplo usei o Vscode. Localize a pasta com seu arquivo de chave privada **.pem**. A chave é usada para iniciar esta instância. Execute este comando, se necessário, para garantir que sua chave não seja visível publicamente.
```chmod 400 "chave01.pem"``` Na console da AWS selecione sua instância criada e com botão direito va em conect para conectar-se a instância. 

<img src="img/selecioneInstance.png" alt="">

Agora selecione a aba SSH e copie ou digite o comando para conetar a instancia via SSH no terminal. Conecte-se à sua instância usando seu IP público (Public IPv4 address).

<img src="img/acessossh2.png" alt="" />

Exemplo:

```
ssh -i "chave.pem" admin@"IP_Public_IPv4_da_instância"
```
na tela do terminal confirme com "yes" e acesse a maquina.

<img src="img/conectInstance.png" alt="">

<img src="img/acessossh.png" alt="">

<hr>



#Atualizar a lista de pacotes e instalar dependencias necesarias
sudo apt update 
sudo apt install -y curl gnupg2 ca-certificates lsb-release

#adicionar a chave GPG oficial do docker
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian bullseye stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#atualizar novamente
sudo apt update 

#Instalar o docker 
sudo apt install docker-ce docker-ce-cli containerd.io -y

#verificar se o docker foi instalado e a versão 
sudo docker --version

#para teste rode um container hello-word
sudo docker run hello-world

#verifique o teste com docker ps e ps -a
docker ps 
docker ps -a 

#baixar ultima versao do docker-compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

#criar um diretorio para armazenar nosso arquivo docker-compose.yml
sudo nano /data/projeto-wordpress/docker-compose.yml

#digite o script abaixo e salve o arquivo com ctrl + o

services:
  wordpress:
    image: wordpress
    restart: always
    container_name: wordpress
    ports:
      - "8080:80" # para acessar o WordPress no navegador por http://localhost:8080
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress:/var/www/html
    depends_on:
      - db

  db:
    image: mysql:8.0
    restart: always
    container_name: wordpress_db
    ports:
      - "3307:3306"
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:


#execute o docker compose
sudo docker-compose up -d

#verifique os containers 
docker ps 
docker ps -a








