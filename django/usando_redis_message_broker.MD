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
    Queue('default', Exchange('default'), routing_key='default'),
)

# configurando o Celery para descobrir automaticamente as tarefas
app.autodiscover_tasks()

# configurando o timezone
app.conf.timezone = 'America/Manaus'
```

# Adiciando o Celery para ser iniciado
Agora precisamos dentro do arquivo ``__init__.py`` configurar nosso Celary para ser iniciado, para isso basta adicionar o código abaixo.

```python
from __future__ import absolute_import, unicode_literals
from .celery import app as celery_app

__all__ = ('celery_app',)
```

# Configurando as variáveis do Celery no settings
Vamos configurar as variáveis de ambiente do Celery dentro do nosso ``settings.py``.

```python
# url do broker, no final o barra zero é o caminho para o banco de dados
# padrão do redis que guarda a mensageria recebida
CELERY_BROKER_URL = 'redis://127.0.0.1:6379/0'
# configurando para aceitar conteúdos no formato JSOn
CELERY_ACCEPT_CONTENT = ['application/json']
# habilitando UTC
CELERY_ENABLE_UTC = True
# setando a timezone
CELERY_TIMEZONE = 'America/Manaus'
# configurando serializer da task
CELERY_TASK_SERIALIZER = 'json'
# configurando serializer do result
CELERY_RESULT_SERIALIZER = 'json'
```

# Levantando um Worker do Celery
Bom agora que todo nosso ambiente já está configurado para trabalhar com o Celery, vamos subir nosso worker, para isso vamos rodar a linha de código abaixo no terminal.

```sh
celery -A app worker --loglevel=DEBUG --pool=gevent -Q default
```
Vamos analizar nosso comando
1. celery - Chamando o celery
2. -A app - O nome do nosso aplicativo onde está o celery.py
3. worker - Invocando um worker
4. --loglevel - Ativando o log para nível de DEBUG
5. --pool=gevent - Informando que nosso pool de thread será gerido pelo gevent
6. -Q default - O nome da nossa Queue(lista) que configuramos em celery.py

Se tudo ocorreu corretamente nosso seu terminal devem aparecer informações parecidas com essas.

```sh
 -------------- celery@user v5.2.6 (dawn-chorus)
--- ***** ----- 
-- ******* ---- Linux-5.4.0-104-generic-x86_64-with-glibc2.31 2022-04-09 14:42:08
- *** --- * --- 
- ** ---------- [config]
- ** ---------- .> app:         sale:0x7ffa9ab91120
- ** ---------- .> transport:   redis://127.0.0.1:6379/0
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 8 (gevent)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
--- ***** ----- 
 -------------- [queues]
                .> default          exchange=default(direct) key=default

```

Se você viu algo parecido com isso no seu terminal, seu Worker do Celery está funcionando e pronto parece receber mensagens.

# Criando uma tarefa
Agora tudo estudo está funcionando perfeitamente, vamos criar nossa ``task``, para isso dentro da sua aplicação no mesmo local onde criou o arquivo ``celery.py``, vamos criar um arquivo chamado ``tasks.py``.

Vamos incialmente chamar no topo do arquivo o import que vamos precisar utrilizar.

```python
from celery import shared_task
import time
```

E agora vamos implementar nosso método que referencia nossa função assíncrona

```python
# annotation da task passando o nome da nossa lista
@shared_task(queue='default')
def alunos_por_idade():
    pass
```

Agora dentro do arquivo ``actions.py`` vamos fazer nossa função lógica que vai usar nosso método da task do celery.

```python
class AlunoActions:
    @staticmethod
    def alunos_por_idade():
        # consultamos a lista de alunos
        results = models.Aluno.objects.all()
        # formatamos em string cada registro separado por vírgula saltando uma linha
        results = map(lambda item: f"{results['nome']}, {results['idade']}\n", results)
        # abrimos o arquivo
        with open('alunos_por_idade.txt', 'a') as file:
            # escrevmos as linhas no arquivo
            for line in results:
                file.writelines(line)
            # adicionando um time sleep apenas para simular uma demora no processo
            time.sleep(5)
```

Agora lá nosso método da task celery chamamos essa função da action substitundo o pass.

```python
# annotation da task passando o nome da nossa lista
@shared_task(queue='default')
def alunos_por_idade():
    actions.AlunoActions.alunos_por_idade()
```

Agora precisamos apenas chamar nosso método de task dentro do nosso viewset no endpoint que irá executar essa atividade.

```python
# adicionando a importação de tasks no topo
from app import tasks
# para gerar execption
from kombu.exceptions import OperationalError


class AlunoModelViewSet(viewsets):
    queryet = models.Aluno.objects.all()
    serializer_class = serializers.AlunoSerializer()
    
    # criando um metodo para ser nosso endpoint
    @action(methods=['GET'], detail=False)
    def alunos_por_idade(self, request, *args, **kwargs):
        try:
            # chamando o método da task, aplicando como assincrono,
            # como não temos parametros passamos um array vazio []
            tasks.alunos_por_idade.aplly_async([])
        except OperationalError as error:
            raise Exception(f'Broker connection error {error}')
        return Response(status=200)
```

Show, agora vamos novamente startar nossa worker para poder fazer o nosso teste, então rode o comando no temrinal.

```sh
celery -A app worker --loglevel=DEBUG --pool=gevent -Q default
```

Agora vamos acessa na url nosso endpoint para disparar nosso worker, o comportamento esperado é que ele retorne imediatamente o resultado como ``status = 200`` assim como configuramos ali e em background ele fique executando nossa task na worker, acesse a url

```sh
http://localhost:8000/app/aluno/alunos_por_idade/
```
Se tudo deu certo aconteceu o que comentamos acima, sua resposta foi 200 e nosso worker está executando nossa task em segundo plano, para termos certeza disso precisamos verificar na raiz do projeto se o arquivo alunos.txt foi criado e se aos poucos ele está recebendo conteúdo, ficará algo como

```txt
Pedro, 14
João, 12
```

# Websocket
Agora que já temos todo o ambiente com Celery e Redis configurado, vamos para a segunda parte da nosso estudo que envolve a utilização de websockets, para isso vamos começar instalando dois pacotes que serão necessários.

```sh
pip install channels channels-redis djangp-cors-headers
```
Agora dentro de ``settings.py`` precisamos adicionar nosso channels como um ``INTALED_APPS``.

```python
INSTALLED_APPS = [
    ...
    'channels',
]
```
Também precisamos adicionar nosso cors-headers, porém como uma MIDDLEWARE.

```python
MIDDLEWARE = [
    ...
    'corsheaders.middleware.CorsMiddleware',
]
```

Vamos setar nosso aceite para cors

```python
CORS_ORIGIN_ALLOW_ALL = True
CORS_ALLOW_METHODS = default_methods
```

Também vamos adicionar dentro de ``settings.py`` a seguinte configuração.

```python
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            'hosts': [('127.0.0.1', 6379,)]
        }
    }
}
```

Ainda no mesmo arquivo vamos adicionar a configuração para nosso ASGI que vamos criar mais abaixo.

```python
ASGI_APPLICATION = 'app.asgi.application'
```

Com as configurações do channels feitas e pronto para o uso, agora vamos precisa dentro da nossa aplicação no mesmo local onde criamos o ``tasks.py`` criar um arquivo chamado ``consumers.py``, esse arquivo vai ser onde ficaram nossos channels ws.

```python
# imports que iremos precisar
from channels.generic.websocket import AsyncJsonWebsocketConsumer

# nossa classe chat
class ChatConsumer(AsyncJsonWebsocketConsumer):

    # nossa função de conexão assincrona
    async def connect(self):
        # aceitando uma conexao
        await self.accept()
        # adicionando conexão ao grupo chat
        await self.channel_layer.group_add('chat', self.channel_name)

    # função para quando haver desconexão
    async def disconnect(self, code):
        # removendo a conexão do grupo chat
        await self.channel_layer.groud_discard('chat', self.channel_name)

    
    # nosso metodo para envio de mensagens
    async def group_message(self, event):
        # chamamos o metodo send_json para enviar o que vier no context
        await self.send_json(content=event['content'])

    # nosso metodo para receber mensagens
    async def receive_json(self, event, **kwargs):
        # quando receber uma mensagem vai enviar para todos os outros conectados
        await self.channel_layer.group_send('chat', {
            'type': 'group.message',
            'content': event
        })
```

Próximo passo é criar nossas rotas de websocket, nosso arquivo deve ficar dentro do aplicativo principal, lá no mesmo lugar onde fica nosso settings, vamos chamos de ``routing.py``

```python
# imports
from django.urls import path
from basic import consumers

# nossas rotas
urlrouter = [
    path('chat/', consumers.ChatConsumer.as_asgi())
]
```
Agora precisamos configurar dentro do nosso arquivo ``asgi.py`` para além das rotas http também aceitar as rotas ws.

Altere a linha

```python
application = get_asgi_application()
```

para

```python
application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    "websocket": URLRouter(routing.urlrouter)
})
```

Pronto, se tudo deu certo quando mandarmos iniciar o servidor uma nova mensagem irá nos avisar que o nosso ASGI está ativa e pronto para conexões ws.

```sh
April 10, 2022 - 03:00:39
Django version 4.0.3, using settings 'app.settings'
Starting ASGI/Channels version 3.0.4 development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Peceba na linha
> Starting ASGI/Channels...

Se você viu essa mensagem tudo está configurado corretamente.

# Testando WebSocket
Agora temos toda a configuração do websocket implementada, vamos fazer um teste pra ver se realmente tudo está funcionando.
Para ficar mais dinâmico vamos utilizar o chrome que temos certeza que conseguirá conectar com nosso canal.

No console do chrome cole o seguinte código, faça isso em duas abas para simular duas pessoas conectadas no servidor ws.

```ts
ws = new WebSocket('ws://127.0.0.1:8000/chat/')
ws.onmessage = (data) => {
    console.log('mensagem:')
    console.log(data['data'])
}
```

Agora para mandar uma mensagem para o servidor e ele devolver para as conexões vamos usar a função ``send``

```ts
// convertendo para JSON que é o formato que nosso servidor espera
msg = JSON.stringify({message: 'Teste de mensagem'})
// enviando
ws.send(msg)
```

Se tudo deu certo deve ter sido impresso no seu console a mensagem.

```ts
mensagem:
{"message": "Teste de mensagem"}
```

# Avançando um pouco mais
Agora vamos avançar um pouco mais, nosso próximo estudo é unir o que fizemos com o Celery e o que fizemos com Websocket, para começarmos a fazer isso precisamos criar um arquivo chamado ``helpers.py`` dentro do nosso aplicativo, mesmo local onde criamos nossos arquivos ``tasks.py`` e ``consumers.py``, e dentro do nosso arquivos vamos criar um método para enviar nossas menssagens que são providas da aplicação, ou seja, de dentro da aplicação pra fora.

```python
# imports
from asgiref.sync import async_to_sync
from channels.layers import get_channel_layer

#
```