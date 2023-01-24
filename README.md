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



