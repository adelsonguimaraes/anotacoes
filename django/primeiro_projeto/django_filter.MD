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
    filterset_class = filters.CourseFilter
```
Agora basta passamos na url o parâmetro que criamos no filtro para que o django filter faço o nosso filtro.

> localhost:8000/api/basic/course?name=matematica


## 4. Criando um filtro personalizado

Agora vamos criar um filtro personalizado que utilizada duas classes do `filterset` para gerar um novo filtro.

```py
from django_filters import filterset


class NumberRangeFilter(filterset.BaseRangeFilter, filterset.NumberFilter):
    pass

class CourseFilter(filterset.FilterSet):
    ...
    id_range = NumberRangeFilter(field_name='id', lookup_expr='range')
```
Primeiramente criamos uma classe que só servirá pra ser composta, ou seja, terá herança múltipla, isso é necessário para o filtro que exige o tipo `BaseRangeFilter` e o tipo `NumberFilter` que são classes do `filterset`, com isso tempo nossa classe `NumberRangeFilter` que apenas tem um `pass` pois não possui nenhum função específica.
Em seguida implementamos o nosso filtro `id_range` que recebe a nossa classe de filtro personalizada `NumberRangeFilter`, em seguidas informamos os campos necessários, o campo que deve ser usado para o filtro `field_name` que nesse caso recebe nosso campo `id`, e também temos o atributo `lookup_expr` que recebe a constante `range`.

Nosso filtro acima faz uma filtragem nos dados de curso por id, retornando dados em um range de de ids, ou seja, se colocarmos na pesquisa o id `4,50` nossa resposta será cursos do id 4 ao id 50, a chamada da nossa url será como mostrado abaixo.

> localhost:8000/api/basic/course?id_range=4,50

Também podemos criar um método nosso filtro onde podemos fazer uma consulta mais específica utilizando o manager.

```py
from django_filters import filterset

class CourseFilter(filterset.Filterset):
    ...
    name_or_teacher = filterset.CharFilter(method='filter_by_name_or_teacher')

    def filter_by_name_or_teacher(self, queryset, name, value):
        return queryset.filter(Q(name__icontains=value) | Q(teacher__name__icontains=value))
```
Acima temos um exemplo onde temos um filtro personalizado que utiliza um método customizado onde fazemos a filtragem por nome do curso ou nome de professor, isso recebendo o valor que é enviado na url como valor do nosso filtro, a chamada ficaria assim.

> localhost:8000/api/basic/course?name_or_teacher=matematica
