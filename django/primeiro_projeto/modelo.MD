# Modelos Django

Continuando nosso estudo sobre Django, agora veremos o que são os modelos dentro da estrutura do projeto e como devem ser construídos.

## 1. Criando um modelo

Para começarmos a criação de nosso modelo primeiro vamos acessar o nosso novo aplicativo `basic` que criamos no estudo anterior, dentro do aplicativo haverá um arquivo chamado `models.py`, vamos abrir esse arquivo para edição no pycharm.
Antes de começarmos a criar código precisamos entender que os modelos no Django são a base da aplicação, tudo se baseia em modelos, por isso eles são muito importantes para a aplicação.

## 1.1 Adicionando modelo

Agora vamos adicionar nosso primeiro modelo dentro do nosso arquivo models, para isso adicione o seguinte código.

```py
from django.db import models

class Employee(models.Model):
...
```

O código acima cria uma classe com o nome de `Employee`, e dentro dos parenteses acontece uma coisa que no python chamamos de `injeção de dependências`, que nada mais é que uma `extensão de super classe`, a classe nesse caso que estamos injetando faz com que o django saíba que essa classe `deve se tornar uma tabela no banco de dados`, com isso o django passa a fazer esse `gerenciamento`, peceba que quando chamamos dentro do parenteses `models.Model` estamos usando o `models` que foi importado no topo do arquivo de dentro de `django.db` classe do django.

## 1.2 Adicioando os atributos do modelo

```py
...
class Employee(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=100, null=False)
    salary = models.DecimalField(max_digits=10, decimal_places=2, null=False)
...
```

Logo em abaixo da nossa classe vamos declarar os atributos, repare que é importante manter a identação do código (um tab para dentro abaixo da classe).
Sobre os atributos podemos observar que existem vários tipos de acordo com a necessidade do atributo.

Nosso primeiro atríbuto é o `id` como na maioria dos bancos de dados podemos ter um indentificador que é nossa chave primária, e aqui estamos fazendo essa configuração para que o Django possa fazer esse gerenciamento, para isso fazemos a declaração `models.AutoField(primary_key=True)`, com isso configuramos para que o Django faça o auto incremento desse campo e que considere ele como chave primária.

O segundo atributo é `name`, sabemos que name é do tipo texto (string), então configuramos o atríbuto para que o Django saiba como criar o campo corretamente no banco de dados.

```py
models.CharField(max_length=100, null=False)
```

Aqui estamos dizendo que o tipo do campo é `Char`, o tampo do campo será de `100` caractéres, e o campo não poderá ser `null`.

Nosso terceiro atributo é `salary`, este campo é referente a valores em moeda, que pode receber um valor quebrado, decimal.

```py
models.DecimalField(max_digits=10, decimal_places=2, null=False)
```
Aqui estamos configurando que o campo será do tipo `Decimal` com `10` dígitos e que não permite valor `nulo`.

## 1.3 Classe interna Meta

Agora vamos ver a classe Meta que é utilizada para fazer configurações que o django precisa para gerenciar o modelo.

```py
...
    salary = models.DecimalField(max_digits=10, decimal_places=2, null=False)

    class Meta
        db_table = 'employee'
        managed = True
```
É chamada de classe interna pois fica dentro da outra classe na mesma direção da identação dos atríbutos da classe, os atributos da classe `Meta` também devem ser identados com um tab para dentro.

Nosso primeiro atributo aqui é `db_table` que serve para dizer para o Django qual deve ser o nome da tabela referente a esse modelo no banco de dados.

O segundo atributo é `managed` que serve para dizer que queremos que o Django gerencie a tabela no banco de dados referente a esse modelo, com isso, tudo que mudarmos no modelo o Django irá refletir na tabela do banco de dados.