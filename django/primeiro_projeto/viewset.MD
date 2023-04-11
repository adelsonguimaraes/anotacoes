# Viewsets Django

Agora vamos estudar sobre os viewsets, aqui no caso vamos utilizar o rest framework para montar nossas classes. Os viewsets são as classes onde se localizam os métodos `HTTP(get, post, patch e outros)`.

# 1. Criando viewsets

Para criarmos nossos viewsets vamos até nosso aplicativo e vamos criar um arquivo chamado `viewsets.py`, agora dentro do arquivo vamos importar do rest framework a classe `viewsets`, assim também como precisamos importar do nosso aplicativo os `models` e os `serializers`.

```py
from rest_framework import viewsets
from basic import models, serializers
```

Em seguida vamos criar a nossa classe.

```py
class CourseViewSet(viewsets.ModelViewSet):
    queryset = models.Course.objects.all()
    serializer_class = serializers.CourseSerializer
```
Acima criamos nossa classe de viewset de curso, assim como fizemos no serializer aqui seguimos a mesma convenção do nome da nossa classe ser o nome do modelo mais `ViewSet`, assim sabemos que esse é o viewset do nosso modelo, injetamos na nossa classe o `viewsets.ModelViewset` que é a super classe vinda do rest framework que diz que nosso viewset depende de modelo.
Dentro do viewset temos dois atributos necessários para funcionar, o primeiro é o `queryset` que o django usa para armazenar a resposta da consulta no banco de dados e o `serializer_class` que é a classe serializadora do nosso modelo.
Podemos perceber que o `queryset` recebe o seguinte `models.Course.objects.all()`, isso caracteriza uma consulta nos dados utilizando o modelo, que é a forma que fazemos consultas com o Django, no final podemos ver o `all()` que significa que estamos consultando todos os dados da tabela, existem outros tipos de consultas que iremos ver com o tempo.

Aqui finalizamos o nosso estudo sobre as viewsets, nosso próximo passo é estudar sobre as `urls`, para seguir para o próximo estudo clique no link a seguir.

[Próximo passo](./urls.MD) - Trabalhando com urls.