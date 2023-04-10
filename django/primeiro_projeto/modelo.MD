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
    created_at = models.DateTimeField(null=False, auto_now_add=True)
    modified_at = models.DateTimeField(null=False, auto_now=True)
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

Nosso quarto atributo é chamado de `created_at` e serve para guardar a data do momento em que o dado foi criado.

```py
models.DateTimeField(null=False, auto_now_add=True)
```
Acima vemos que o campo não pode ser nulo e também vemos a configuração `auto_now_add=True` que significa que o Django automáticamente irá inserir a data atual no momento que o registro for inserido no banco.

Nosso quinto atributo se chama `modified_at` e é referente a data da última atualização do registro no banco de dados.

```py
models.DateTimeField(null=False, auto_now=True)
```
Aqui podemos ver uma configuração muito parecida com o campo anterior, a diferença aqui é que a configuração de auto irá adicionar sempre que houver uma alteração nos dados, e no caso anterior a data é inserida apenas no momento da criação do registro.

## 1.3 Classe interna Meta

Agora vamos ver a classe Meta que é utilizada para fazer configurações que o django precisa para gerenciar o modelo.

```py
...
    modified_at = models.DateTimeField(null=False, auto_now=True)

    class Meta
        db_table = 'employee'
        managed = True
```
É chamada de classe interna pois fica dentro da outra classe na mesma direção da identação dos atríbutos da classe, os atributos da classe `Meta` também devem ser identados com um tab para dentro.

Nosso primeiro atributo aqui é `db_table` que serve para dizer para o Django qual deve ser o nome da tabela referente a esse modelo no banco de dados.

O segundo atributo é `managed` que serve para dizer que queremos que o Django gerencie a tabela no banco de dados referente a esse modelo, com isso, tudo que mudarmos no modelo o Django irá refletir na tabela do banco de dados.

## 2. Criando as migrações

No Django quando queremos refletir os modelos ou modificações dos mesmos no banco de dados criamos `migrations`, com elas o django consegue gerenciar as modificações que são feitas nos modelos e atualizar o banco de dados pra que os campos das tabelas fiquem alinhadas.
Para gerar os arquivos de migrations rodamos o seguinte comando no terminal do pycharm.

```sh
python manage.py makemigrations
```
Após rodar o comando um novo arquivo será gerado dentro de `basic/migrations`, esse arquivo é responsável por fazer as modificações dos modelos desse aplicativo, quando modificamos alguma coisa no modelo seja adicionando, modificando ou removendo, precisamos rodar novamente este comando e gerar novos arquivos de gerenciamento.

## 3. Exibindo as migrações

Para podermos ver as migrations do projeto e saber o que já está sincronizado no banco e o que falta sincronizar, podemos rodar o comando a seguir.

```sh
python manage.py showmigrations
```
Uma lista será mostrada no terminal separando as migrations por aplicativo, na lista do nosso aplicativo basic será possível ver a migration que acabamos de gerar chamada de `[ ] 0001_initial`, também é possível ver várias migrations de outros aplicativos e de gerenciamento do Django, podemos perceber ainda que ao lado de cada migration existe um check que mostra se a migration foi executada ou não.

## 4. Executando as migrações

Nossas migrações ainda não causaram efeito no nosso banco de dados, para que isso ocorra precisamos rodar outro comando.

```sh
python manage.py migrate
```
No fim da execução o Django irá criar e atualizar o banco de dados com as tabelas referentes aos modelos referenciados nas migrations, tanto as próprias tabelas do django quando as tabelas referentes aos nossos modelos.

## 5. Conectando ao banco de dados com Pycharm

Para verificar isso podemos acessar a aba lateral direita do pycharm com o nome `database`, clicar no botão com sinal de `+`, navegar na opção `Data Source` e escolher a opção `SQLite`, uma janela irá abrir, nela vamos configurar nosso banco de dados.
Na raiz do projeto foi criado um novo arquivo chamado `db.sqlite3` quando rodamos o comando `migrate`, esse arquivo é o nosso banco de dados em `SQLite` esse nome pode ser modificado no arquivo `sale/settings`, no campo nome pode ser colocado o nome que você achar melhor pro contexto aqui vamos chamar apenas de `db`, no campo `file` clique nos três pontos e encontre o nosso arquivo `db.sqlite3` e selecione, agora na parte de baixo você verá uma mensagem `Download missing driver files`, que significa que a IDE ainda não possui o drive necessário para conectar com o banco de dados, podemos resolver isso apenas clicando no botão `download`, a IDE irá baixar o drive, por fim basta clicar em `Test Connection` para que o pycharm faça o teste de conexão, se aparecer a mensagem de sucesso, tudo deu certo, clique em `apply` e em seguida `OK`.

Agora você irá ver na aba de DataBase que irá aparecer um banco com nome `db` clique no ícone de `>` para expandir as opções faça o mesmo para `main` e `tables`, aqui veremos todas as tabelas existentes no nosso banco de dados, podemos ver as tabelas de gerenciamento que o django utiliza, essas não devemos mexer, e também veremos a nossa tabela chamada `employee`.

## 6. Alterando o modelo

Aprendemos até aqui como fazemos para criar um modelo e refletir essa criação no banco de dados, também mencionamos que é possível não só fazer essa sincronia na criação nas também em modificações, sejam elas alterações ou remoções.

Agora vamos ver como funciona esse processo para quando precisamos fazer uma alteração no nosso modelo.

```py
class Employee(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=100, null=False)
    salary = models.DecimalField(max_digits=10, decimal_places=2, null=False)
    gender = models.CharField(max_length=1, null=False)
    
    class Meta:
        db_table = 'employee'
        managed = True
```

Acima adicionamos no nosso modelo um campo chamado `gender`, agora para refletir isso nosso banco de dados vamos novamente rodar o comando para criar a nova `migration`.

```sh
python manage.py makemigration
```

Em seguida vamos rodar novamente o comando para mostrar as migrations que foram geradas.

```sh
python manage.py showmigrations
```

Podemos verificar novamente na lista do nosso aplicativo `basic` que existe uma migration nova que ainda não foi executada chamada `0002_employee_gender`, para executar nossa migration rodamos novamente o comando.

```sh
python manage.py migrate
```
Agora podemos acessar pela aba database nossa tabela e verificar os campos dela expandindo pelo icone `>` e também expandir `columns` podemos verificar que um novo campo chamado `gender` foi criado, se caso não tenha aparecido o campo pode ser que falte atualizar a visão dos dados, para isso basta no topo da aba clicar no ícone de refresh.

Aqui finalizamos o nosso estudo sobre modelos, agora nosso próximo passo é entender sobre `serializers`, para seguir para o próximo estudo clique no link abaixo.

[Próximo passo](./serializador.MD) - Trabalhando com serializadores.