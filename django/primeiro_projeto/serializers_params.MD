# Serializer Params Django

Quando trabalhamos com parâmetros passados via requisição podemos tratar os dados recebidos para verificar se atendem requisitos esperados, o que evita erros inesperados quando tentarmos acessar.

## 1. Criando o arquivo

Primeiramente vamos criar um arquivo onde ficarão nossas classes de Serializer Params, vamos criar na raiz do nosso app `basic` o arquivo chamado `serializers_params.py`.

## 2. Criando serializador

Agora vamos adicionar o conteúdo do nosso arquivo.

```py
from rest_framework import serializers


class CourseGetByNameSerializer(serializers.Serializer):
    name = serializers.CharField(required=True, max_length=64)
```
Acima temos o conteúdo do nosso serializer, nosso método que lista cursos por nome recebe um parâmetro via requisição que é o `name`, então aqui configuramos ele dizendo que é obrigatório o tamanho máximo, agora vamos alterar nosso método no viewset e usar o nosso serializador.

```py
...
from basic import serializers_params
...
def get_by_name(self, request, *args, **kwargs):
        result = serializers_params.CourseGetByNameSerializer(data=request.query_params, context={'request': request})
        result.is_valid(raise_exception=True)
        self.queryset = self.get_queryset().filter(name__icontains=result.validated_data.get('name'))
        return super(CourseModelViewSet, self).list(request, *args, **kwargs) 
```
Vamos analisar o que foi modificado, primeiramente importamos nosso serializador

```py
from basic import serializers_params
```
Depois alteramos a forma que pegávamos o parâmetro `name` para uma forma usando nosso serializador.

```py
result = serializers_params.CourseGetByNameSerializer(data=request.query_params, context={'request': request})
```
Depois chamamos o método `is_valid` do serializador que retorna se os parâmetros enviados são válidos para as configurações do serializador, se não, retorna uma exceção, após isso seguimos o que já havia apenas mudando a parte onde passamos o dado agora validador para o list do superclasse.

```py
return super(CourseModelViewSet, self).list(request, *args, **kwargs) 
```
Nosso estudo sobre serializer_params finalizou aqui, nosso próximo estudo é sobre Django Filter, para ir para o próximo estudo clique no link a seguir.

[Próximo passo](./django_filter.MD) - Trabalhando com Django Filter.