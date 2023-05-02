# Serializadores Django

Agora vamos iniciar o estudo sobre os `serializers`, esses são os responsáveis por converter nossos dados de modelo para dados json e de dados json para nosso modelo, além disso os serializers também servem para validar dados, o que deixa os códigos muito mais limpos e elegantes pois invés de tratarmos os dados com lógicas de validação, utilizamos os serializers para fazer isso para gente.

# 1. Criando os serializadores

Dentro do nosso aplicativo `basic` vamos criar um novo arquivo chamado `serializers.py`, em seguida dentro do arquivo vamos importar a classe de serializadores do django rest framework, também vamos importar do nosso app basic o arquivo de `models`.

```py
from rest_framework import serializers
from basic import models
...
```

Agora vamos criar a nossa classe de serialização baseado no nosso modelo, nesse caso aqui vamos nos basear na classe `Course`.

```py
class CourseSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Course
        fields = '__all__'
...
```
Por convenção criamos a classe com o mesmo nome do nosso modelo com adição de `Serializer`, assim sabemos que essa é a classe serializadora do nosso modelo, injetamos na nossa classe a dependência de `serializers.ModelSerializer` que é uma super classe do rest framework de serialização de modelo, com isso o django irá se basear no próprio modelo para saber os campos que existem para serem serializados e deserializados.

Na nossa classe interna `Meta` informamos qual o modelo que esse serializador irá usar `models.Course` e os camos que devem ser serializados, nesse caso vamos utilizar `'__all__'` informando que todos os campos do modelo devem ser serializado, mas também podemos passar uma lista `[]` informando os campos específicos.

Aqui finalizamos o nosso estudo sobre os sreializadores, nosso próximo passo é aprender sobre os `viewsets`, para seguir para o próximo estudo clique no link abaixo.

## 2. Adicionando objeto no serializador

Agora vamos ver como podemos pegar os dados de um objeto de um modelo estrangeiro dentro do nosso serializer, por padrão apenas o id estrangeiro é retornadom vejamos o modelo abaixo.

```py
from rest_framework import serializers
from basic import models

class CourseSerializer(serializers.ModelSerializer):
    teatcher = TeacherSerializer(source='teacher', read_only=True)

    class Meta:
        model = models.Course
        fields = '__all__'
```
Imagine que no nosso modelo de curso agora temos uma relação com a classe de professores, então nas nossas consultas comuns sempre irá vir o `id_teacher`, mas agora queremos saber os dados completos desse professor, isso é o que estamos fazendo no exemplo acima, criamos uma campo que referência o professor e setamos pra ele o serializador de professor, passando a informação de qual campo dentro da classe curso referencia e também informamos que o campo do serializador é apenas para leitura com `read_only=True`.
Também podemos fazer isso de forma inversa, ou seja podemos dentro de um serializador trazer toda a lista de items que tem relacionamento, por exemplo, agora vamos tratar o serializador de professor para trazer todos os cursos que são lecionados.

```py
from rest_framework import serializers
from basic import models

class TeacherSerializer(serializers.ModelSerializer):
    courses = CourseSerializer(source='course_set', many=True, read_only=True)

    class Meta:
        model = models.Teatcher
        fields = '__all__'
```
Assim como no modelo acima, aqui também configuramos o serializador para trazer dados externos, nesse caso ele irá trazer a lista de todos os cursos que tem relacionamento com esse professor.

Um ponto de observação aqui é que tanto para um caso quando para o outro é importante utilizar no viewset os métodos `selected_related` e `prefetech_related` na viewset como vamos estudar mais a frente, que evita que consultas desnecessárias sejam feitas.

## 3. Implementando o FlexFields

Acima vimos como podemos trazer objetos e listas relacionais no serializador, porém existe uma forma mais limpa e atualizada de fazer isso, utilizando uma biblioteca chamada `FlexFields`.

## 3.1 Instalando

Para começarmos precisamos instalar a biblioteca.

```sh
pip install drf-flex-fields
```
Lembrando de fazer o pip freeze também para atualizar o arquivo.

```sh
pip freeze > requirements.txt
```
Agora vamos atualizar nosso serializador com o que precisamos para utilizar o flex fields.

## 3.2 Implementando

```py
from rest_framework import serializers
from basic import models
from rest_flex_fields import FlexFieldModelSerializer

class TeacherSerializer(FlexFieldsModelSerializer):
    class Meta:
        model = models.Teatcher
        fields = '__all__'

    expandable_fields = {
        'courses': ('basic.CourseSerializer', {'source':'course_set', 'many': True})
    }
```
Removemos o campo cursos que tinhamos criado anteriormente, importamos a lib que instalamos `FlexFieldModelSerializer`, injetamos na nossa classe serializadora e adicionamos a configuração `expandable_fields`, onde configuramos o serializador externo, o campo relacional e dizemos que existe múltiplos relacionamentos.
Os campos flexíveis são interessantes porque ele só trará os campos expandidos que informarmos na consulta que queremos aquele campo expandido, vejamos a consulta abaixo.

> http://localhost:8000/api/teacher/expand=courses

No caso quando queremos expandir um objeto segue o modelo abaixo

```py
class CourseSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Course
        fields = '__all__'

    expandable_fields = {
        'teacher': ('basic.TeacherSerializer',)
    }
```
> http://localhost:8000/api/course/expanded=teacher

[Próximo passo](./viewset.MD) - Trabalhando com viewsets.