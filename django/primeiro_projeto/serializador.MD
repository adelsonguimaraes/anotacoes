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

[Próximo passo](./viewset.MD) - Trabalhando com viewsets.