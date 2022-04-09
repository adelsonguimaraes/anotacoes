# Usando Celery Redis para Message Broker
Aqui nessa sessão vamos ver como utilizar o ``Celery`` em conjunto com o ``Redis``.

O Celery é um pacote python utilizado para gerenciamento de filas e também para fazer agendamentos, aqui no nosso caso vamos usar o gerenciamento de filas.

O Redis é um banco de dados ``NoSql`` que disponibiliza de várias ferramentas, na maioria das vezes utilizado como um banco de Cache, isso por que tem uma alta performace devido trabalhar com memória flash, o que permite fazer consultas numa velocidade muito alta.
Hoje utilizaremos a parte de pub/sub (mensageria) do Redis, trabalhando como um Message Broker.

Com a união dessas duas poderosas tecnologias vamos montar um recurso com websocket para requisiçoes mais demoradas, a saída partirá do Django que irá ativar o nosso broker que ficará trabalhando em segundo plano, de imediato a resposta é enviada para o front que por sua vez irá abrir uma conexão javascript websocket com nosso broker e receberá o feedback em tempo real.

# Instalando as ferramentas
Para começar vamos instalar as ferramentas necessárias no nosso projeto.

1. Celery
   
    ```sh
    pip install celery redis
    ```
2. GEvent

    ```sh
    pip install gevent
    ```
# Container Docker com Redis
Para simplificar nossa vida vamos usar o Docker para subir um banco de dados Redis na nossa máquina, segue abaixo o docker-compose para isso.

```yml
version: "3"
services: 

    redis:
    image: redis
    ports:
      - 6379:6379
```

Se você ainda não sabe como usar o Docker e Docker-Compose aqui tem um link com um conteúdo das docs oficiais.

Instalando docker
> https://docs.docker.com/engine/install/

Instalando docker-compose
> https://docs.docker.com/compose/install/