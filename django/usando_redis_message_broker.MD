# Usando Celery Redis para Message Broker
Aqui nessa sessão vamos ver como utilizar o ``Celery`` em conjunto com o ``Redis``.

O Celery é um pacote python utilizado para gerenciamento de filas e também para fazer agendamentos, aqui no nosso caso vamos usar o gerenciamento de filas.

O Redis é um banco de dados ``NoSql`` que disponibiliza de várias ferramentas, na maioria das vezes utilizado como um banco de Cache, isso por que tem uma alta performace devido trabalhar com memória flash, o que permite fazer consultas numa velocidade muito alta.
Hoje utilizaremos a parte de pub/sub (mensageria) do Redis, trabalhando como um Message Broker.

Com a união dessas duas poderosas tecnologias vamos montar um recurso com websocket para requisiçoes mais demoradas, a saída partirá do Django que irá ativar o nosso broker que ficará trabalhando em segundo plano, de imediato a resposta é enviada para o front que por sua vez irá abrir uma conexão javascript websocket com nosso broker e receberá o feedback em tempo real.

# Instalando as ferramentas
Para começar vamos instalar as ferramentas necessárias no nosso projeto.

```sh
pip install celery redis gevent
```

Instalamos aqui o celery que já explicamos a cima, o pacote redis do python pra poder comunicamos com o redis e o geevent que é um gerenciador de pool de eventos e memória, sua função é melhorara performance.

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

# Criando o arquivo celery
Agora que já instalamos os pacotes e temos nosso container docker com redis funcionando, vamos criar dentro do nosso aplicativo principal do projeto um arquivo chamado ``celery.py``.

No topo desse arquivo vamos fazer as importações que serão necessárias para nosso processo.

```python
from __future__ absolute_import, unicode_literals
import os
from celery import Celery
from kombu import Exchange, Queue
```

Agora vamos adicionar as configurações para que as ``workers`` do Celery consigam acessar e usar os recursos do Django, isso possibilita que uma ``worker`` faça por exemplo acesso a uma base dados via Django.

```python
# setando a variavel de ambiente, settings do app
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'app.settings')

# instanciando o app do Celery
app = Celery('app')

# carregando as configurações do Django para nosso app Celery
app.config_from_object('django.conf:settings', namespace='CELERY')

# configurando as filas
app.conf.task_queues = (
    Queue('default', Exchange('default'), routing_key='default')
)

# configurando o Celery para descobrir automaticamente as tarefas
app.autodiscover_tasks()

# configurando o timezone
app.conf.timezone = 'America/Manaus'
```