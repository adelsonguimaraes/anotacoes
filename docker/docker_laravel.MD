# Dokernerizando Aplicação Laravel
Aqui vamos ver o processo para criar uma imagem docker de uma aplicação Laravel, vamos supor que você já tenha uma aplicação em laravel e esteja dentro da pasta do seu projeto.

## Configurando o arquivo .env do Laravel
Vamos iniciar configurando nossas variáveis de ambiente no arquivo .env, o Larevel cria seu próprio .env então vamos acesso e fazer as configurações, vamos ressaltar aqui apenas as variáveis que são importantes para nosso docker-compose mais tarde.

* Porta do Servidor Web

    Vamos criar uma variável de ambiente para mais tarde fornecer para um servidor como o ``Apache`` ou ``Nginx``
    ```sh
    ...
    APP_SERVER_PORT=7000
    ```
* Conexão Banco de Dados
    
    Vamos configurar nossas variáveis do banco de dados, nesse caso vamos fazer pro mysql.


    ```sh
    DB_CONNECTION=mysql # driver
    DB_HOST=db # nome do servido docker
    DB_PORT=3306 # porta do container
    DB_PORT_EXPOSE=33060 # porta espelho no host via container docker
    DB_DATABASE=meudb # banco de dados
    DB_USERNAME=meuuser # usuario
    DB_PASSWORD=minhasenha # senha
    DB_ROOT_PASSWORD=senharoot # senha root
    ```

    Aqui adicionamos uma variável chamada ``DB_PORT_EXPOSE`` que recebe ``33060``, assim podemos escolher uma outra porta do nosso host pra espelhar a porta do container caso deseje deixar a porta padrão ``3306`` livre para outro uso.

## Criando Dockerfile
Como queremos ter a nossa imagem da aplicação personalizada, vamos criar um ``Dockerfile`` para que possamos buildar a nossa imagem.

* Instrução Bash

    No topo do nosso arquivo Dockerfile vamos adicionar a instrução ``#!/bin/bash`` para que os comandos das sessões ``RUN`` sejam executados com o bash quando necessário, isso vai evitar erros e dores de cabeça na hora da compilação.

    ```dockerfile
    #!/bin/bash
    ```

* Imagem base

    Vamos adicionar a imagem que desejamos usar como base para nossa aplicação utilizando a notação ``FROM`` e especificar o nome da nossa imagem com a notação ``LABEL``.
    ```dockerfile
    # imagem base php para nginx
    FROM php:7.4-fpm-alpine AS apidevbarber
    # nome da nossa imagem
    LABEL name="php-app"
    ```

* Sinalizar local de trabalho 

    Agora vamos sinalizar o local de trabalho ``WORKDIR`` dentro do nosso container
    ```dockerfile
    # fique a vontade pra escolher o caminho que preferir
    WORKDIR /var/www
    ```

* Atualizar os Repositórios e Instalar Pacotes

    Vamos atualizar nossa imagem Alpine para ter acesso a todos os pacotes que precisamos, e em seguida vamos instalar todos eles

    ```dockerfile
    # atualizando os repos do alpine
    RUN apk update \
    # movendo o php ini
    && mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    # instalando pacotes que precisamos pro funcionamento do Larevel e outros recursos
    && apk add \
    git \
    curl \
    libpng-dev \
    oniguruma-dev \
    libxml2-dev \
    zip \
    unzip \
    bash \
    # limpando os arquivos de cache para deixar a imagem mais leve
    && rm -f /var/cache/apk/* \
    && rm -rf /tmp/pear/
    ```

* Instalar extensões PHP

    Vamos novamente com a notação ``RUN`` instalar as extensões do PHP necessárias para que nosso Laravel funcione corretamente

    ```dockerfile
    RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd
    ```

*  Baixando a última versão do Composer
    
    Agora vamos adicionar ao nosso projeto a última versão do ``Composer``

    ```dockerfile
    RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
    ```

* Copiando os arquivos da aplicação pra Imagem

    Vamos copiar os arquivos da nossa aplicação para dentro da nossa imagem, como estamos dentro da raiz do projeto vamos apenas usar o ``./`` para referenciar a raiz e apontar para o caminho dentro do container ``/var/www`` 

    ```dockerfile
    COPY . /var/www/
    ```

* Criando um usuário e dando permissões

    Vamos criar um usuário com permissões pra manipular nossas pastas dentro da aplicação

    ```dockerfile
    RUN addgroup www-data www-data \
    && chown -R www-data:www-data \
    /var/www/storage \
    /var/www/bootstrap/cache
    ```

* Expondo a porta

    Vamos expor as porta da nossa aplicação para que o nosso servidor possa acessar e replicar

    ```dockerfile
    EXPOSE 9000
    ```

* Executando o PHP quando o container subir

    Vamos sinalizar pro docker que vamos executar o nosso PHP assim que o container subir utilizando a notação ``CMD``

    ```dockerfile
    CMD ["php-fpm"]
    ```

## Criando docker-compose
Agora vamos criar um docker-compose para gerenciar nossas imagens, vamos considerar que vamos ter um servidor ``Nginx``, que vai rodar em um outro container e vai escutar a porta do nosso ``php`` e espelhar na porta ``80`` e também vamor ter um banco de dados ``mariadb``

* Versão do docker-compose

    Aqui vamos usar a versão 3 do docker-file e ele vai ficar bem no topo do nosso arquivo

    ```yml
    version: "3"
    ```

* Serviços

    Vamos ter 3 serviços como comentamos antes, nosso ``php-app``, nosso servidor ``nginx`` e nosso banco de dados ``mariadb`` agora vamos implementar eles no nosso arquivo

    ```yml
    services:
        db:
            # imagem base
            image: mariadb
            # nome do container
            container_name: devbarber-db
            # nossas variáveis de ambiente
            # pegando os valores definidos em .env
            environment:
            - ALLOW_EMPTY_PASSOWORD=yes
            - MARIADB_USER=${DB_USERNAME}
            - MARIADB_DATABASE=${DB_DATABASE}
            - MARIADB_PASSWORD=${DB_PASSWORD}
            - MARIADB_ROOT_PASSWORD=${DB_ROOT_PASSWORD}
            ports:
            # nossa porta exposta é a que vamos usar pra conectar via host, a porta interna é a que o laravel se conectará
            - ${DB_PORT_EXPOSE}:${DB_PORT} 
            volumes:
            # aqui definimos um caminho pra colocar arquivos .sql
            - ./docker-compose/mysql/dump:/docker-entrypoint-initdb.d
            - ./docker-compose/mysql/data:/var/lib/mysql # local dos dados do banco

        app:
            # informando que essa imagem precisa ser buildada
            build: .
            # nossa imagem compilada
            image: php-app
            # nome do container
            container_name: php-app
            # container sempre ativo
            restart: always
            # local dos volumes do arquivo
            volumes:
            - .:/var/www
            # o seviço só começa quando db estiver pronto
            depends_on: 
            - db

        nginx:
            # imagem base nginx
            image: nginx:1.21.3-alpine
            # nome do container
            container_name: php-app-nginx
            # arqui usamos nossa variavel da porta do server pra espelhar a porta 80 do container
            ports:
            - ${APP_SERVER_PORT}:80
            volumes:
            # pegangos os dados da aplicação
            - .:/var/www
            # aqui colocamos um arquivo de configuração do servidor nginx
            - ./docker-compose/nginx:/etc/nginx/conf.d
            depends_on:
            # espera o app ficar pronto
            - app
    ```

## Configuração Nginx
Precisamos criar nosso arquivo de configuração do Nginx para enviar para o container como vimos ali em volumes, criamos a pasta ``docker-compose``, dentro dela a pasta ``nginx`` e dentro criamos o arquivo ``server.conf`` e colocamos o seguinte conteúdo

```ini
server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /var/www/public;
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```

## Docker Compose UP
Agora que tudo está configurado vamos iniciar nosso processo utilizando ``docker-compose``, vamos buildar nosso ``php-app`` a partir do nosso ``Dockerfile``, subir o container de ``mariadb``, subir o container de ``php-app`` e por fim o container de ``nginx``, caso ele não encontre as imagens base localmente, irá fazer o pull de ``Docker Hub``.

```bash
docker-compose up -d
```

## Acessando o Sevidor
Se sua aplicação lárevel possui uma rota web para ``/`` então agora basta acessar o endereço do ``ip do sevidor`` : ``7000`` que foi a porta que definimos para nosso servidor nas variáveis de ambiente.