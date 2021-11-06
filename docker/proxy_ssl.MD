# Docker com Nginx-Proxy & Lets Encrypt
Repositório de exemplo para configuração de um servidor proxy de gerenciamento para containers com ``Nginx-Proxy`` e Ceritificação SSL com ``Let's Encrypt``.

## Nginx-Proxy

O ``Nginx-Proxy`` é uma configuração para fazer ``proxy reverso`` escutando em uma porta como a por padrão 80 e direcionando para dominios ou sub-dominios.

Por exemplo o domínio ``hello.com`` o servidor ``Nginx`` escuta essa chamada na porta 80 do IP do servidor ``127.0.0.1:80`` então ele redireciona para o conteúdo correto.

O mesmo para um subdomínio ``web.hello.com`` agora o usuário solicita o conteúdo de ``web`` e não da raiz, novamente ``Nginx`` escutará o pedido e redirecionará a solicitação para o conteúdo correto, isso é o proxy reverso, trabalho que o ``Nginx-Proxy`` fará para nós automaticamente.

## Let's Encrypt

O ``Let's Encrypt`` é um serviço gratuito para geração de certificados SSL temporários, ter um certificado aumenta a segurança de informações de um site, assim também como garante ao usuário segurança e disponibilidade de acesso ao uso de recursos como Câmera, Geolocalização e outros.

Nesse modelo vamos utilizar uma configuração que será responsável por criar automaticamente certificados para nossos proxys, assim também como automatizará a manutenção dos mesmos.

## Criar rede docker

O primeiro passo que precisamos fazer é criar uma rede docker que será nossa rede principal, para que nosso ``Nginx-Proxy`` funcione corretamente precisamos que todos os containers que desejamos utilzar subdomínio estejam na mesma rede, isso porque o nosso container de proxy passa a escutar automaticamente nosso docker, e quando o container atender a aos requitos de expor a porta 80 e possuir variavel de ambiente ``VIRTUAL_HOST``, ele irá de foram automática configurar o próxy para este container.

Utilizamos o seguinte comando para criar nossa rede docker.
```sh
docker network create nginx-proxy
```
Fique a vontade para escolher o nome da rede, importante apenas se atentar para informar ela corretamente nos arquivos de ``docker-compose``.

## Docker-Compose
Agora vamos configurar nosso docker compose, vamos separar em partes, o container ``nginx-proxy``, o container ``letsencrypt``, nossas ``aps`` e o ``rodapé``

1. Container Nginx-Proxy
    
    ```yml
    version: '3'

    services:

    # nome do nosso servico
    nginx-proxy:
        # imagem que vamos utilizar
        image: jwilder/nginx-proxy:alpine
        # nome do nosso container
        container_name: nginx-proxy
        # portas http e https
        ports:
        - "80:80"
        - "443:443"
        # volumes nomeados
        volumes:
        - conf:/etc/nginx/conf.d
        - vhost:/etc/nginx/vhost.d
        - html:/usr/share/nginx/html
        - dhparam:/etc/nginx/dhparam
        - certs:/etc/nginx/certs:ro
        - /var/run/docker.sock:/tmp/docker.sock:ro
        # labels
        labels:
        - "com.github.jrcs.letsencrypt_nginx_proxy_companion.nginx_proxy"
        # sempre reiniciar quase pare o serviço
        restart: always
    ```

2. Container Let's Encrypt

    ```yml
    # nome do nosso serviço
    letsencrypt:
        # imagem que vamos usar
        image: jrcs/letsencrypt-nginx-proxy-companion
        # nome do nosso container
        container_name: nginx-proxy-le
        # espera pelo nginx-proxy para subir
        depends_on:
        - nginx-proxy
        # volumes
        volumes:
        - vhost:/etc/nginx/vhost.d
        - html:/usr/share/nginx/html
        - dhparam:/etc/nginx/dhparam:ro
        - certs:/etc/nginx/certs
        - acme:/etc/acme.sh
        - /var/run/docker.sock:/var/run/docker.sock:ro
        # reinicia sempre que parar o serviço
        restart: always
    ```

3. Aplicações

    Aqui vamos ter dois containers de exemplo com php rodando cada uma app simples que com uma página html com a frase ``Dominio 1 funcionando`` e ``Dominio 2 funcionando``, esse é apenas um modelo, você pode usar as mesma configurações que verá aqui nessa sessão pra configurar qualquer container com app que tiver, lembrando sempre de ter a porta 80 exposta e as Variáveis de ambiente ``VIRTUAL_HOST`` e ``LETSENCRYPT_HOST`` configuradas para que nossos serviços de proxy e ssl possam configura-los corretamente.

    ```yml
    # servico
    webserver1:
        # imagem
        image: php:7.2-apache
        # nome do container
        container_name: web1
        # expondo a porta 80 para o proxy
        expose:
        - 80
        environment:
        # variavel de ambiente pro certificado
        LETSENCRYPT_HOST: web1.hello.com.br
        # variavel de ambiente pro proxy
        VIRTUAL_HOST: web1.hello.com.br
        # caminho do conteudo do site
        volumes:
        - ./html1:/var/www/html
    
    # serviço
    webserver2:
        # imagem
        image: php:7.2-apache
        # nomde do container
        container_name: web2
        # expondo a porta 80
        expose:
        - 80
        environment:
        # variavel de ambiente pro certificado
        LETSENCRYPT_HOST: web2.hello.com.br
        # variavel de ambiente pro proxy
        VIRTUAL_HOST: web2.hello.com.br
        # caminho do conteudo do site
        volumes:
        - ./html2:/var/www/html
    ```

4. Rodapé

    No final do arquivo vamos nomear os volumes que será utilizados nos containers e setar a nossa rede ``nginx-proxy`` que criamos no primeiro passo, lembre-se é importante que todos os containers que devem ser atendidos pelo serviço de proxy estejam nessa mesma rede, caso contrário o proxy não conseguirá escurar o container.

    ```yml
    # volumes utilizados pelo proxy e ssl
    volumes:
        conf:
        vhost:
        html:
        dhparam:
        certs:
        acme:

    # setando a rede que criamos
    # lembre-se de adicionar em seus próximos containers essa parte de rede para que ele esteja na mesma rede do proxy e assim visível para o mesmo poder configurá-lo
    networks:
        default:
            external:
            name: nginx-proxy
    ```

## Importante
Caso esteja fazendo esse processo em uma VPS com um dominio real, é importante além dessa configuração apontar cada subdominio para o seu IP via o painel de gerenciamento de DNS do servidor onde seu domínio foi comprado.

## Docker Compose Up
Vamos agora utlizar o comando para subir nosso containers, se tudo correr como esperado o docker deve levantar cada container como configuramos e agora você poderá acessar pelo seu navegador os endereços dos subdomínos que configuramos ``web1.hello.com`` e ``web2.hello.com``, lembrando que se estiver utilizando em sua VPS precisa substituir hello.com pelo seu domínio real tanto nos arquivos quando na hora de acessar via navegador.

Se tudo deu certo agora você pode acessar esses endereços e eles estarão funcionando com certificado SSL ou seja com ``https`` no endereço.

## Link do projeto original
Nesse repositório você escontrará além de toda essa documentação o projeto modelo
https://github.com/adelsonguimaraes/