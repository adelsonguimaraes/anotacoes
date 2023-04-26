# Viewsets Django

Agora vamos estudar sobre os viewsets, aqui no caso vamos utilizar o rest framework para montar nossas classes. Os viewsets são as classes onde se localizam os métodos `HTTP(get, post, patch e outros)`.

## 1. Criando viewsets

Para criarmos nossos viewsets vamos até nosso aplicativo e vamos criar um arquivo chamado `viewsets.py`, agora dentro do arquivo vamos importar do rest framework a classe `viewsets`, assim também como precisamos importar do nosso aplicativo os `models` e os `serializers`.

```py
from rest_framework import viewsets
from basic import models, serializers
```

Em seguida vamos criar a nossa classe.

```py
class CourseModelViewSet(viewsets.ModelViewSet):
    queryset = models.Course.objects.all()
    serializer_class = serializers.CourseSerializer
```
Acima criamos nossa classe de viewset de curso, assim como fizemos no serializer aqui seguimos a mesma convenção do nome da nossa classe ser o nome do modelo mais `ViewSet`, assim sabemos que esse é o viewset do nosso modelo, injetamos na nossa classe o `viewsets.ModelViewset` que é a super classe vinda do rest framework que diz que nosso viewset depende de modelo.
Dentro do viewset temos dois atributos necessários para funcionar, o primeiro é o `queryset` que o django usa para armazenar a resposta da consulta no banco de dados e o `serializer_class` que é a classe serializadora do nosso modelo.
Podemos perceber que o `queryset` recebe o seguinte `models.Course.objects.all()`, isso caracteriza uma consulta nos dados utilizando o modelo, que é a forma que fazemos consultas com o Django, no final podemos ver o `all()` que significa que estamos consultando todos os dados da tabela, existem outros tipos de consultas que iremos ver com o tempo.
## 2. Criando métodos personalizados

Dentro de um viewset podemos criar endpoints personalizados de acordo com a necessidade do sistema, por exemplo, vamos criar um método para retornar uma lista de cursos por nome.

```py
...
from rest_framework.decorators import action

class CourseModelViewSet(viewsets.ModelViewSet):
    queryset = models.Course.objects.all()
    serializer_class = serializers.CourseSerializer

    @action(methods=['GET'], detail=False)
    def get_by_name(self, request, *args, **kwargs):
        name = request.query_params.get('name')
        self.queryset = self.get_queryset().filter(name__icontains=name)
        return super(CourseModelViewSet, self).list(request, *args, **kwargs)
```
Vamos analisar o que foi feito, inicialmente temos uma nova importação para fazer relativo ao decorator `action` que serve para dizer qual o verbo http que nosso método irá usar, colocamos o decorator na linha acima do nosso método.

```py
...
from rest_framework.decorators import action
...
@action(methods=['GET'], detail=False)
    def get_by_name(self, request, *args, **kwargs):
...
```
A cima podemos ainda ver o nosso método, por padrão do django ele espera esse parametros, em seguida pegamos o parámetro `name` que foi passado via url, para isso utilizamos o `request` e fazemos um get nos parámetros, pegando o campo nome.

```py
...
    name = request.query_params.get('name')
...
```
Agora que temos o nome que foi enviado na requisição fazemos um filtro usando a queryset da classe, buscando por cursos que contenham o nome e sobre-escrevemos a queryset da classe.

```py
...
self.queryset = self.get_queryset().filter(name__icontains=name)
...
```
Agora que a nossa queryset já foi atualizada, chamamos o método listar da superclasse, passamos os parámetros e retornamos.

```py
...
return super(CourseModelViewSet, self).list(request, *args, **kwargs)
```
Para acessar o nosso novo endpoint apenas adicionarmos ele no final da url

> http://localhost:8000/api/school/basic/get_by_name

Aqui finalizamos o nosso estudo sobre as viewsets, nosso próximo passo é estudar sobre as `urls`, para seguir para o próximo estudo clique no link a seguir.

[Próximo passo](./urls.MD) - Trabalhando com urls.