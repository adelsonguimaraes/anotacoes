# Utilizando Docker Registry

Nesse arquivo faço anotações de como utilizar o docker registry, com o objetivo de criar seu próprio servidor de registro de imagens docker dentro de um servidor.

- Baixar imagem registry oficial
- Configurar o container
- Iniciar o container
- Taguiar uma imagem local para o servidor
- Configurar daemon local para permitir enviar para sevidor 'não' https
- Enviar imagem para o servidor registry
- Consultar catálogo de imagens no servidor

## Docker Pull
Vamos baixar a imagem do Registry do ``Docker HUB`` dentro do nosso Servidor, para isso utilizamos o comando:
>docker pull registry

## Configurações para iniciar
Uma explicação detalha de cada parte da nossa linha de comando para iniciar o nosso servidor registry.

- docker run - comando pra iniciar
- -d - modo detach / segundo plano
- -p PORTA_DOCKER:PORTA_SERVIDOR - expondo a porta DOCKER para porta do SERVIDOR
- --restart=always - manter a imagem ativa sempre
- --name registry - adicionando um nome pro container
- registry - nome da imagem a ser utlizado

## Iniciando o container registry
Agora que temos todas as nossas configurações vamos iniciar o servidor
>docker run -d -p [PORTA_DOCKER]:[PORTA_SERVIDOR] --restart=always --name [my-registry] registry


## Taguiando imagem para o servidor
Agora que temos o nosso registry servidor rodando, precisamos ir ao local cliente onde temos a imagem
que queremos subir para para o nosso registry e taguiar ela corretamente para enviar ao servidor
fazemos isso utilizando o comando:
>docker image tag [ID_IMAGEM] [IP_SEVIDOR]:[PORTA]/[NOME_IMAGEM]:[VERSAO]

## Configurando Daemon Local
O docker tem uma segurança para fazer push apenas para locais com https, porém podemos configurar
o daemon do docker para aceitar envios para servidores não https, para isso fazemos o seguinte

- Windows

    Utilizando algum editor de texto acessamos o caminho %userprofile%\.docker/daemon.json e adicionamos o ip do servidor e porta em ``insecure-registries``
    >"insecure-registries": ["IP_SEVIDOR:PORTA"]

- Linux

    Acessamos o daemo usando
    >nano /etc/docker/daemon.json

    Adicionamos o conteudo
    >{ "insecure-registries": ["IP_SEVIDOR:PORTA"] }

## Enviando a Imagem
Agora que nosso docker local esta configurado e pronto pra enviar a imagem para o servidor de registro
utilizamos o seguinte comando
>docker push [IP_SERVIDOR]:[PORTA]/[NOME_IMAGEM]:VERSAO

## Consultando nosso catalago de images
Podemos consultar o catálogo de images que possuímos no nosso servidor utilizando o comando
>curl [IP_SERVIDOR]:[PORTA]/v2/_catalog


## Baixando imagem registry servidor de images
Agora que temos uma imagem dentro nosso servidor registry, podemos fazer pull e baixar a imagem para nossa máquina, para isso utilizamos o seguinte comando.
>docker pull [IP_SERVIDOR]:[PORTA]/[NOME_IMAGEM]:[VERSAO]