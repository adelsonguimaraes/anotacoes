# Celery e Redis

Agora vamos estudar sobre tarefas em segundo plano, utilizando a biblioteca celery para comunicar com o banco de dados Redis que será utilizado como um Broker, uma ferramenta de mensageria.

## 1. Quando aplicar

O python é uma linguagem de processamento assíncrono, ou seja, a execução acontecem em ordem, como uma fila, com isso queremos dizer que cada excução obrigatóriamente necessita ser finalizada para começar uma nova, sabendo disso, quando temos um processo muito longo, a sequência de execução fica em espera do termino do processo para poder seguir, dessa maneira quando temos uma tarefa que faça muitas consultas e processos o tempo de execução se torna mais demorado, por exemplo quando temos uma geração de relatório complexo que envolve muitos dados. 

Quando temos esse tipo de situação é interessante utilizar um processo em segundo plano, que o usuário inicie a execução e possa continuar utilizando o sistema sem precisar esperar pela resposta do processo, é nesse tipo de situação que podemos aplicar o celery com redis.

## 2. Instalações

Para poder utilizar no nosso projeto precisamos fazer a instalação das libs dentro do nosso projeto.

```sh
pip install celery redis
```
Também utilizamos o `gevent` que é um gerenciador de memória que é mais robusto que o padrão, então é importante ter ele configurado no projeto, também no caso de rodar o projeto em windows ele é obrigatório para que o worker funcione.

```sh
pip install gevent
```
No final da instalação faça o pip freeze para salvar as libs no documento `requirements.txt`.

```sh
pip freeze > requirements.txt
```

## 3. Configurações

Agora que já estamos com as bibliotecas necessárias intaladas vamos fazer as configurações no projeto, o primeiro passo é criar um arquivo no aplicativo principal `school` chamado `celery.py`, e adicionar as seguintes configurações.

```py
# celery.py

from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from kombu import Exchange, Queue

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'school.settings')

app = Celery('school')
app.congfig_from_object('django.conf:settings', namespace='CELERY')

app.conf.task_queues = (
    Queue('default', Exchange('default'), routing_key='default'),
)
app.autodiscover_tasks()
app.conf.timezone = 'America/Manaus'
```
Vamos analisar por partes o código que temos acima.

```py
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery
from kombu import Exchange, Queue
```
Aqui temos os imports do nosso código, precisamos importar o `os` para tert acesso as `enviroments`, importamos também lib que instalamos `Celery` e também precisamos das libs `Exchange` e `Queue` do modulo `kombu`, para podermos fazer as filas que serão executadas na `worker`.

```py
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'school.settings')
```
Nesse trecho utilizamos a `os` para adicionar um enviroment `DJANGO_SETTINGS_MODULE` no arquivo de `settings.py`, essa configuraçãos serve para que o work passe a poder acessar os recursos do Django e ter acesso a base de dados.

```py
app = Celery('school')
app.congfig_from_object('django.conf:settings', namespace='CELERY')
```
Criamos o nosso app de celery e damos o nome, aqui chamamos de `school`, em seguida adicionamos a configuração no app.

```py
app.conf.task_queues = (
    Queue('default', Exchange('default'), routing_key='default'),
)
```
Aqui criamos uma fila de tarefas e configuramos, chamamos ela de `default`, de acordo com a necessidade do sistema podemos criar várias filas que trabalharam de forma concorrente.

```py
app.autodiscover_tasks()
app.conf.timezone = 'America/Manaus'
```
Aqui chamamos um método chamado `.autodiscover_task()` que serve para descobrir de forma automática as tarefas configuradas do work, na linha seguinte configuramos o timezone de acordo com a necessidade.

## 4. Configurando o init

Agora precisamos configurar o nosso app celery dentro do arquivo `__init__.py` para que ele seja chamado quando o app iniciar.

```py
#__init__.py

from __future__ import absolute_import, unicode_literals
from .celery import app as celery_app

__all__ = ('celery_app',)
```

## 5. Adicionando Celery no settings

Para finalizar a configuração agora precisamos adicionar as configurações do nosso Celery dentro do arquivo `settings.py`.

```py
# settings.py
...

CELERY_BROKER_URL = 'redis://127:0.0.1:6379/0'
CELERY_ACCEPT_CONTENT = ['application/json']
CELERY_ENABLE_UTC = True
CELERY_TIMEZONE = 'America/Manaus'
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
```
Todas as configurações foram finalizadas.

## 6. Subindo o container docker redis

...