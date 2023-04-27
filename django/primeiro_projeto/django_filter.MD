# Django Filter

Agora vamos ver sobre uma outra ferramenta muito interessante que complementa bastante as funcionalidades chamada `Django Filter`.

## 1. Configurando

Precisamos inicialmente instalar a biblioteca no nosso projeto, para isso precisamos rodar o comando abaixo no terminal.

```sh
pip install django_filter
```
Em seguida precisamos ir no `settings.py` na parte de `INSTALLED_APPS` e adicionar como aplicativo.

```py
INSTALLED_APPS = [
    ...
    'django_filters',
]
```
E também precisamos adicionar uma configuração na sessão `REST_FRAMEWORK`.

```py
...
REST_FRAMEWORK = {
    ...
    'DEFAULT_FILTER_BACKENDS': (
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.OrderingFilter',
    )
}
```

## 2. Criando o arquivo

Agora precisamos criar um novo arquivo dentro do nosso app chamado `filters.py`, onde vai ficar nosso filtros.

```py
from django_filters import filterset
from basic import models

class CourseFilter(filterset.FilterSet):
    name = filterset.CharFilter(lookup_expr='icontains')
    
    class Meta:
        model = models.Course
        fields = ['name']
```
Acima temos o nosso código do filters, importamos `filterset` de `django_filters`, e também os models de basic, como padrão criamos uma classe para filtros de curso e injetamos o filterset, em seguida criamos o campo `name` que recebe de filterset o tipo `CharFilter` e dentro dele configuramos qual o lookup vamos utilizar, para lembramos, os lookups são as constantes dos managers que estudamos antes, então aqui adicionamos `icontains` que como vimos antes, verifica se o termo que passamos contém dentro do texto do campo, na classe meta informamos o modelo que é referente ao esse filtro e os campos que estão sendo manipulados.

## 3. Adicionando ao viewset

O próximo passo para que o nosso filtro funcione corretamente é injetá-lo no viewset de curso.

```py
class CourseModelViewSet(viewsets.ModelViewSet):
    queryset = models.Course.objects.all()
    serializer_class = serializers.CourseSerializer
    filter_class = filters.CourseFilter
```
Agora basta passamos na url o parâmetro que criamos no filtro para que o django filter faço o nosso filtro.

> localhost:8000/api/basic/course?name=matematica