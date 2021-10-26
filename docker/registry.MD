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

## Criando Autenticação
Caso você não queria que o seu sevidor fique público e que qualquer pessoa que possua ``ip`` e ``porta`` possa consultar o catálago, enviar ou baixar imagens, podemos criar um acesso para fazer autenticação com docker login, para isso vamos fazer os seguintes passos.

>Observação: Para utilizar a autenticação é necessário ter certificado

* Criando o arquivo de authenticação

    Vamos criar uma pasta chamada ``auth`` e usar o ``htpasswd`` de dentro da imagem do ``registry `` na versão ``2.7.0`` para gerar nosso acesso, a saída será dentro da nossa pasta ``auth`` com nome ``htpasswd``

    ```sh
    docker run --entrypoint htpasswd registry:2.7.0 -Bbn [usuario] [senha] > /auth/htpasswd
    ```

* Verificando o Htpasswd

    Vamos verificar se o arquivo foi gerado corretamente
    ```sh
    cat auth/htpasswd
    ```
    A saída deve ser algo assim
    ```
    usuario:$2y$10$ylpNdRc.n8b.pHqz/CB8gOK2pk9LNBqvmJmQ48QVHFdpal5D1dIkC
    ```

* Espelhando Autenticação Registry

    Agora que já temos nosso autenticação pronta precisamos espelhar ela dentro do nosso container registry via ``volumes``, podemos fazer isso adicionando ``-v`` no nosso comando, mas, também precisamos informar 3 variáveis de ambiente para nosso container e fazermos isso usando ``-e``, segue a linha de comando abaixo

    >docker run -d -p [PORTA_DOCKER]:[PORTA_SERVIDOR] --restart=always --name [my-registry] -v 'pwd'/auth:/auth -e "REGISTRY_AUTH=htpasswd" -e REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm"  -e "REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd" registry:2.71

* Logando no Servidor de Registro

    Vamos agora logar no nosso servidor de registro pra isso basta usarmos ``docker login`` e informar o ``ip do servidor`` : ``porta``
    
    >docker login [IP_SERVIDOR]:[PORTA]

    Informe a senha que será solicitada e uma mensagem de ``logado com sucesso`` deve aparecer, agora apenas usuários logados no servidor poderão gerenciar imagens.

## Docker Compose Registry

Vamos criar um ``docker-compose.yml`` para organizar as configurações do nosso container registry, assim facilitando o gerenciamento do container quando necessário

```yml
# versão 3 docker-compose
version: '3'

# serviços
services:
  # registro
  registry:
    # imagem base
    image: registry:2.7.0
    # nome do container
    container_name: registry
    # porta host 5000 : container 5000
    ports:
      - 5000:5000
    #variáveis de ambiente
    environment:
      # arquivo de registro
      - REGISTRY_AUTH=htpasswd
      - REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm
      # caminho do arquivo
      - REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd
      # caminho onde os arquivos devem ser salvos
      - REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY=/data
    volumes:
      # authenticacao
      - ./auth:/auth
      # registro e imagens
      - ./data:/data
    # sempre reiniciar
    restart: always
```