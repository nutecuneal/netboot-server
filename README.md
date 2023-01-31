# Netboot Server

## Sumário

- [Netboot Server](#netboot-server)
  - [Sumário](#sumário)
  - [Como funciona um boot pela rede?](#como-funciona-um-boot-pela-rede)
    - [Requisitos](#requisitos)
  - [Instalação](#instalação)
    - [Instalando o Debian primeiro](#instalando-o-debian-primeiro)
    - [Instalando omv-extras](#instalando-omv-extras)
  - [Configurando](#configurando)
    - [SSH](#ssh)
    - [Montando um Sistema de Arquivos](#montando-um-sistema-de-arquivos)
    - [Instalando o Docker](#instalando-o-docker)
    - [Criação dos containers](#criação-dos-containers)
      - [Diretamente pelo servidor](#diretamente-pelo-servidor)

## Como funciona um boot pela rede?

O boot pela rede funciona utilizando PXE (Preboot execution environment) permitindo que a máquina cliente possa fazer o boot de algum sistema para uso ou para instalação em seu disco rigído.

### Requisitos

- Placa de rede compativel com Lan boot e PXE.
- Servidor TFTP para armazenamento das imagens e responsável por entregar elas para a máquina cliente.
- Servidor DHCP que permita network booting e aponte para o servidor TFTP na rede.
- Docker

## Instalação

Para iniciar precisamos ter uma máquina que será o servidor. Para isso será usado o OMV (OpenMediaVault), que é uma distribuição open source projeta para NAS (Network Attached Storage) e utiliza por baixo o debian. Com ele podemos ter vários serviços extras com a adição de plugins, e todo o gerenciamento como a configuração podem ser feitos através de uma página web.

Tem duas formas de instalar o OMV, a primeira é com a imagem disponibilizada no site do OMV e a outra é instalando o debian somente com o minimo e depois instalar o OMV.

Para realizar pela primeira forma, faça o download da imagem pronta: [https://www.openmediavault.org/](https://www.openmediavault.org/)

Qualquer dúvida durante o passo a passo de instalação, consulte a documentação: [https://docs.openmediavault.org/en/stable/](https://docs.openmediavault.org/en/stable/)

<br>

### Instalando o Debian primeiro

Pela segunda forma faça o download da imagem minima do debian através do site oficial: [https://www.debian.org/index.pt.html](https://www.debian.org/index.pt.html)

***Obs: O passo a passo de instalação do debian vai ser igual ao do OMV, a diferença é que o debian vai permitir o particionamento dos discos manualmente.***

Após o sistema instalado entre como root. Ao entrar:

```bash 
# Atualize os pacotes

apt update

# Instale 

apt install -y wget gnupg
```

Agora será nescessário adicionar as chaves do OMV para que o repósitorio seja adicionado e posteriormente a instalação do OMV.

Para utilizar o repositório mais atual, acesse essa parte da documentação: [https://docs.openmediavault.org/en/stable/installation/on_debian.html](https://docs.openmediavault.org/en/stable/installation/on_debian.html)

***Obs: Na documentação aparece o comando "omv-confdbadm populate", mas ao tentar executar vai reclamar como erro, pode prosseguir sem ele, o OMV já foi instalado com suscesso.***

### Instalando omv-extras

Por padrão o OMV já disponibiliza vários plugins, mas para adicionar muitos outros e também permitir que instalação do Docker, devemos instalar o omv-extras.

Pegue a linha de comando do script de instalação: [https://wiki.omv-extras.org/](https://wiki.omv-extras.org/)

Entre no servidor com o usuário root, cole a linha de comando e aperte enter. Aguarde até que a instalação esteja concluida.

## Configurando

Após o OMV ser instalado com sucesso, acesse a página web que vai permitir fazer as configurações: http://ip-servidor

O primeiro login é admin e a senha: openmediavault. Ao entrar é recomendado alterar a senha.

***Obs: Caso tenha algum problema com a senha ou com a placa de rede, você pode entrar no servidor e digitar o comando "omv-firstaid", ele lhe permite fazer algumas mudanças.***

Dentro da interface Web, você pode adicionar itens na sua dashboard clicando no icone de pessoa e selecionando dashboard.

Alguns ajustes são necessários após a instalação:

1. Altere o tempo de logout automático em Workbench
2. Verifique se o TimeZone esta correto.
3. Verifique a interface de rede.
4. Verifique se os discos foram detectados em disks.

### SSH

Por padrão o OMV permite o acesso via ssh ao servidor somente com o usuário root e não com o usuário criado na instalação. Para alterar isso você pode entrar no servidor pela máquina fisica ou via ssh com root somente essa vez. Ao logar e abrir o terminal faça:

```bash
# Adicione o usuário no grupo do ssh e do sudo

usermod -a -G sudo ssh nome-do-usuário
```
Após o comando retornar sem erro, saia do servidor e acesse o sistema do OMV pela página web:

1. Clique em Serviços.
2. Selecione SSH.
3. Desmarque: *Permit root login*
4. Clique em salvar.
5. Caso apareça a mensagem "aplicar alterações" clique em aplicar.
6. Agora teste o acesso ssh com o usuario criado no momento da instalação. 


### Montando um Sistema de Arquivos

Após constar que os discos foram montados corretamente, é preciso montar um sistema de arquivos no disco para começar a ter acesso a ele. 

Para isso acesse: *Discos*

1. Acesse a área de Sistema de arquivos.
2. Selecione a partição do HD.
3. Clique no icone de +
4. Escolha ***criar*** ou se já tiver o sistema de arquivos escolha ***montar***.
     - Criar
       - Selecione o Dispositivo.
       - Escolha o sistema de arquivos que deseja usar.
       - Clique em salvar.
       - Agora é preciso montar sistema de arquivos.
     - Montar
       - Selecione o sistema de arquivos.
       - Selecione uma porcentagem de aviso quando a partição estiver quase cheia.
       - Clique em Salvar.

Depois que o sistema de arquivos foi montado, você precisa criar uma pasta compartilhada para que os serviços no sistema consigam acessar a partição do HD. Para isso:

1. Selecione a área de Pastas Compartilhadas.
2. Clique no icone +
3. Atribua um nome a pasta.
4. Selecione o sistema de arquivos que você montou anteriormente.
5. Em Relative Path será como o nome da pasta ficará criada dentro do servidor. Ex.: isos
6. Clique em salvar.

Agora já temos uma pasta com um sistema de arquivos montado.

### Instalando o Docker

Para instalar o docker pelo OMV, faça:

1. Acesse o OMV pela página web
2. Clique em *Sistema / omv-extras*
3. Dentro de omv-extras, clique em docker e depois em instalar.
4. Aguarde a instalação finalizar.


### Criação dos containers

A criação dos containers podem ser realizadas de duas formas: **Diretamente no servidor** ou pelo **Portainer**.

#### Diretamente pelo servidor

1. Acesse o servidor via SSH com o usuário criado no momento da instalação e não como root.
2. Use o comando: <code>sudo docker run</code> , e passe o restante dos argumentos para a criação do container.
3. Você também pode utilizar o docker-compose para criar os containers.
4. Para ver se os containers estão rodando use: <code>sudo docker ps</code>

Qualquer dúvida sobre como criar um container ou como gerenciar um container, acesse a documentação do docker: (https://docs.docker.com/get-started/).
