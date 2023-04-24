# Queries (consultas) Django

Agora no nosso estudo vamos ver sobre `queries` no Django, que nada mais são que consultas no banco de dados usando a estrutura do próprio django.

## 1. Com funciona as queries

Diferente de outros frameworks todo organismos do django se baseia nos modelos, com as queries não é diferente, quando queremos fazer consultas nos dados, partimos de um modelo e utilizamos uma ferramenta chamada `Managers` que são responsáveis por fazer as consultas padrões do django, com eles podemos listar, consultar por atributos, fazer filtros, ordenação, selecionar os campos que devem ser consultados e muito mais.

## 2. Listando
Vamos ver agora como é simples fazer uma consulta que lista todos os itens de uma tabela utilzando o manager do django.

```py
from basic import models

models.Student.objects.all()
```
Acima temos a consulta que irá trazer uma lista com todos os alunos que estiverem no banco de dados, perceba que a nossa consulta começa acessando o modelo `Student`, em seguida acessamos o manager(objects), e apartir dele chamamos uma função de consulta `all()`, o manager retorna um objeto interável que ele chamada de queryset, ou seja um objeto que é a query não executada, e apartir do momento que chamamos uma função de consulta como o all() ele faz a consulta no banco de dados, importante ainda dizer que o resultado da nossa consulta traz uma lista de objetos do tipo do modelo que estamos utilizando

## 3. Pegando primeiro item

Para pegar o primero item que tivermos no banco podemos usar a função `.first()` do manager, isso irá trazer apenas o primeiro dado.

```py
from basic import models

models.Student.objects.first()
```

Quando esse comando é executado podemos ver que apenas um registro é retornado na consulta.


## 4. Consultando um item por propriedade

Podemos também fazer uma consulta por propriedade usando a função `.get()` do manager, importante dizer que essa função irá trazer apenas um dado na consulta, a primeira ocorrência que atender a consulta.

```py
from basic import models

models.Student.objects.get(name='Joao')
```
A nossa consulta acima irá retorna um item do banco de dados se encontrar algum estudante que tenha o nome de Joao, mesmo que exista mais de um Joao ela irá trazer apenas a primeira ocorrência.

## 5. Ordenando a Consulta

Quando fazemos uma consulta de dados muitas vezes queremos poder ordenar a disposição dos dados, isso é muito útil em vários momentos quando precisamos mostrar os dados de uma forma ordernada seja por nome, idade, data ou um campo de valor por exemplo, para isso vamos usar a função `order_by()` do manager.

```py
from basic import models

models.Student.objects.order_by('name')
```
Acima estamos fazendo uma ordenação da lista de estudantes pelo nome de forma crescente ou seja do A para o Z, podemos ainda fazer a ordenação de forma decrescente do Z para o A, pra isso basta colocar um sinal de `-` na frente do nome do campo que estamos utilizando para ordernar.

```py
from basic import models

models.Student.objects.order_by('-name')
```
## 6. Selecionando os valores

Nós também podemos fazer uma seleção dos campos que queremos mostrar em uma consulta, naturalmente as consultas trazem todos os campos, na maioria dos cenários não precisamos de todos os dados então escolhemos aqueles que devem ser retornados na consulta, para isso utilizamos a função `values()` do manager.

```py
from basic import models

models.Student.objects.values('id', 'name')
```
Na consulta acima utilizamos o values e informamos que das colunas que existem queremos apenas o `id` e o `name`, com isso ele irá trazer a lista dos estudantes apenas com os campos selecionados, um ponto a ressaltar aqui é que quando utilizamos o values diferente da lista que ele traz no uso do método `all()` que retorna uma lista de objetos da classe, aqui nossa lista é de dicionários do python.

## 7. Filtrando dados

Uma função muito importante quando trabalhamos com dados é o filtro, para que possamos consultar dados com mais precisão, no manager também temos uma função chamada `filter()` que faz uma consulta mais complexa nos dados e retorna uma lista com os dados onde ele encontra o valor, é muito parecido com o que o método `get()` que vimos antes, porém o get retornava apenas a primeira ocorrência da consulta, no caso do filter irá retornar uma lista com as ocorrências encontradas.

```py
from basic import models

models.Student.objects.filter(name__icontains='Joao')
```
Na consulta acima estamos filtrando todos os estudantes que no nome contenha Joao, aqui utilizamos uma constante isolada do manager `__icontains` ela faz o papel de verificar se em alguma parte do campo contém a palavra que informamos.

## 8. Constantes de consulta (Field Lookups)

O manager é uma ferramenta muito poderosa e complexa, e dentro das suas consultas existem as constantes isoladas, que são parâmetros internos da consulta que utilizamos quando queremos algum comportamento especial na consulta, acima vimos o exemplo do `__ìcontains` que verifica se a nossa pesquisa contém dentro do campo consultado.
Essas constantes refletem consultas com `Where` na base de dados, essa é uma das coisas mais usadas nas consultas, para verificar mais sobre as contanstes visite o link da documentação onde explicam mais a fundo sobre elas.

> https://docs.djangoproject.com/en/4.2/topics/db/queries/#field-lookups

## 9. Encadeando consultas

Agora vamos ver que podemos encadear os métodos do manager, o que é muito importante quando precisamos fazer consultas que são mais complexas, abaixo um exemplo.

```py
from basic import models

models.Student.objects.filter(name__icontains='Joao').values('id', 'name').order_by('-id')
```
Acima temos uma consulta mais complexa que junta métodos do manager que vimos anteriormente, o que estamos dizendo aqui é que queremos filtrar os estudantes que tem o nome Joao, pegar apenas os campos id e name e ordenar de forma decrescente pelo id.

## 10. Fazendo consultas com condição "OU"

Agora vamos ver como podemos fazer consultas onde queremos utilizar o ou, por exemplo, em uma consulta queremos trazer uma lista onde o nome do estudante pode ser Joao ou Pedro, por padrão as consultas são feitas com "&&", ou seja, as condições são aninhadas, todas as condições precisarão ser atendidas para o dado ser considerado, no exemplo que demos acima isso seria um problema, pois, não é possível um estudante se chamar Joao e Pedro o mesmo tempo, é ai que entra a utilização do "ou".

```py
from basic import models
from django.db.models import Q

models.Student.objects.filter(Q(name='Joao') | Q(name='Pedro'))
```
Agora podemos reparar que para fazer o uso do "ou" preciamos importar uma lib chamada `Q` que vem do pacote `django.db.models`, em seguida utilizamos ela dentro do nosso filtro, antes apenas colocaríamos `name='Joao'`, agora envolvemos isso com o `Q` e fica `Q(name='Joao')`, além disso para nosso "ou" ficar completo precisamos colocar a segunda condição então separamos com o símbolo `|(pipe)` e fica assim `| Q(name='Pedro')`, com isso nossa consulta diz que queremos uma lista de estudantes que o nome pode ser Joao ou Pedro.

Ainda falando sobre o método `Q()` ele também permite que façamos as condições de forma separada, e permite que seja possível seta se o operar deve ser um `AND(&&)` ou um `OR(||)`, vejamos um exemplo.

```py
from basic import models
from django.db.models import Q

conditions = Q()
conditions.add(Q(name='Joao'), Q.OR)
conditions.add(Q(name='Pedro'), Q.OR)
conditions.add(Q(idade__lte='25', Q.AND))

models.Student.objects.filter(conditions)
```
No código acima montamos a condicional separadamente dizendo o operador pra cada uma delas, isso ajuda muito quando preciamos ter condicionais dinâmicas.

## 11. Fazendo consultas excluindo dados

Agora vamos ver uma forma de consulta que funciona de forma inversa ao filter, ou seja, no filter passamos condições que queremos que estejam, no caso do exclude passamos condições que queremos que estejam nos dados mas que queremos que a consulte ignore, por exemplo, podemos fazer uma consulta mandando listar todos os estudantes, porém, dizendo pra ignorar todos que tenham o nome Joao.

```py
from basic import models

models.Student.objects.exclude(name__icontains='Joao')
```
Agora nossa consulta gera uma lista de estudantes, porém não traz nenhum que tenha o nome Joao, isso porque mandandos ele excluir essa informação da consulta.

## 12. Adicionando coluna na consulta (annotate)

Agora vamos ver como podemos adicionar colunas na nossa consulta, isso é interessante quando queremos adicionar campos que não existem no modelo, ou até mesmo modificar um nome por outro, para isso vamos precisar importar um novo método chamado `Value` do módulo `django.db.models` ele será necessário para associar o valor ao campo, abaixo vamos ver um exemplo para entender melhor.

```py
from basic import models
from django.db.models import Value

models.Student.objects.annotate(turno=Value('Matutino')).values('id', 'name', 'turno')
```
Acima agora vemos o nosso modelo de consulta, estamos utilizando um método do manager chamado `annotate` que é justamente o que precisamos para criar ou alterar nome de campos, dentro dele podemos perceber o seguinte `turno=Value('Matutino')`, estamos dizendo que um campo turno receber o método `Value()` que importamos, e dentro do método estamos informando o valor `Matutino`, ou seja nosso campo irá se chamar `turno` e o seu valor será `Matutino`, em seguida precisamos utilizar o método `values()` do manager selecionando os campos `id`, `name` e o nosso campo `turno`, utilizar esse método é opcional, utilizamos aqui pra saída dos daos ser mais limpa, se não usarmos ele a consulta trará todos os campos do modelo mais o nosso campo `turno`.

## 13. Utilizando expressões

Seguindo a mesma linha que fizemos a cima, agora vamos ver como fazer expressões na consulta, para isso vamos utilizar o conhecimento que vimos acima mais um método chamado `ExpressionWrapper` que vamos importar de `django.db.models`.

```py
from basic import models
from django.db.models import Value, ExpressionWrapper, FloatField

models.Student.objects.annotate(soma=ExpressionWrapper(Value(10) + Value(10), FloatField())).values('id', 'name', 'soma')
```
Podemos perceber que o que vimos no tópico anterior continuam sendo usados aqui, além disso que precisamos importar mais dois métodos, `ExpressionWrapper()` e `FloatField()`, o primeiro serve pra envolver nossa expressão, dentro dele que colocamos a expressão e temos um atributo chamado `output_field=` que precisamos usar para dizer qual será o tipo de saída da nossa expressão, no caso utilizamos o `FloatField()` pra sinalizar que será um float.

## 14. Utilizando campos do modelo no annotate

Até agora vimos como fazer campos extras com `annotate` e também vimos como fazer expressões matemáticas, utilizando o `Value`que serve para setar valores estáticos, agora vamos ver como fazer tudo isso porém usando campos do próximo modelo, para fazer isso vamos importar o método `F()` de `django.db.models`, com ele podemos acessar os campos do modelo.

```py
from basic import models
from django.db.models import Value, ExpressionWrapper, FloatField, F

models.Student.objects.annotate(soma=ExpressionWrapper(Value(10) + F('idade'), FloatField())).values('id', 'name', 'soma')
```
Acima temos o mesmo exemplo que utilizamos no tópico anterior, porém agora importamos o método `F()` dentro da nossa expressão apontamos que vamos somar o valor 10 com o campo idade `Value(10) + F('idade')`, assim no campo soma que criamos retornará a idade de cada aluno com + 10 de valor somado, deixando a nossa expressão dinâmica de acordo com os dados da base.

## 15. Utilizando Case

Agora vamos ver como podemos utilizar método `Case()` que tem a mesma função de um `Switch Case` que fazemos em programação, nesse exemplo vamos consultar o sexo do estudante e caso seja `M` vamos retornar `Masculino`, do contrário teremos o valor `default` que será o feminino.

```py
from basic import models
from django.db.models import Value, Case, When

models.Student.objects.annotate(
    gender_name=Case(
        When(gender='M', then=Value("Masculino")),
        default=Value("Feminino")
    )
).values('name', 'gender_name')
```
No código acima temos temos dois novos imports o `Case` e o `When` que são necessário pra fazer nosso cenário de case, agora dentro do nosso annotate temos o `gender_name` que recebe o nosso `Case` e dentro do nosso case temos o `When` que recebe a possível opção de o `gender=M` se isso for verdadeiro então ele chama o `then` que retorna o valor `Masculino`, podemos ter vários `When` caso existam mais opções, em seguida temos o valor default caso nenhuma opção seja atendida, ele é chamado e retorna o valor `Feminino`.

## 16. Consultas com Join

Um recurso muito usado quando estamos trabalhando com bancos relacionais como o Postgres, é fazer consultas utilizando o `join`, isso nada mais é que poder consultar informações de uma outra classe que está relacionada com a nossa classe, vejamos um exemplo simples abaixo.

```py
from basic import models
models.Student.objects.values('name', 'class_room__name')
```
No modelo acima temos uma consulta que busca pelo nome do estudante e pelo nome da class_room onde esté estudante estuda, lembrando como já estudamos anteriormente, estamos utilizando a ponte que liga nossa classe com classe estrangeira, nesse caso o nome da nossa ponte é `class_room` que é um atributo da nossa classe estudante que referencia a `class_room` como uma classe estrangeira, esse é o cenário onde o relacionamento é 1 para 1 e não temos uma tabela de relacionamento, dessa forma atravessamos a ponte para acessar o atributo desejado da outra classe `__name`, assim como as contantes, utilizamos dois underlines seguidos para acessar os atributos.

A ponte interliga as classes, e quando acessamos outra classe também temos acesso a ponte delas caso exista, por exemplo, vamos imaginar que na nossa tabela `class_room` exista uma relação com outra classe chamada `discipline` e queremos saber qual o nome da disciplica que o estudante estuda, vejamos o exemplo abaixo.

```py
from basic import models
models.Student.objects.values('name', 'class_room__name', 'class_room__discipline__name')
```
Nesse nosso exemplo tempo um campo nosso além dos que já estavam no exemplo anterior, `class_room__discipline__name`, podemos perceber aqui agora que primeiro usamos a ponte `class_room` para acessar a ponte de relacionamento de lá `_discipline`, nosso estudante não se relaciona com a disciplina, mas `class_room` tem relação, a partir do momento que atravessamos a ponte para lá, temos acesso a todos os atributos da classe, incluindo a ponte chamada `__discipline`, em seguida por essa ponte, acessamos o atríbuto `__name` que é a informação que queremos.

Também podemos fazer consulta no sentido contrário da ponte, ou seja, acima vimos como pegar as class_rooms onde os estudantes estavam, e agora queremos pegar todos os estudantes que estão em uma determinada class_room, vejamos o código abaixo.

```py
from basic import models
models.ClassRoom.objects.values('name', 'students__name')
```
No código acima estamos fazendo a consulta pelo nosso modelo `ClassRoom`, pegando o nome da turma e o nome de cada estudante que está relacionado com está turma, importante reparar no seguinte, utilizamos a ponte presente no modelo `Student` que chamamos de `related_name` onde amarramos um nome para o relacionamento entre `ClassRoom` e `Student` chamado `students`, uma boa prática para ser usada no related name é dar o mesmo nome da classe que está sendo aplicado adicionando um `s` no final do nome, vejamos abaixo pra ficar mais claro a classe Student.

```py
class Student(ModelBase):
    name = models.CharField(max_length=100, null=False)
    courses = models.ManyToManyField(
        to="Course",
        through="StudentCourse"
    )
    class_room = models.ForeignKey(
        to="ClassRoom",
        db_column="id_classroom",
        on_delete=models.DO_NOTHING,
        null=False,
        related_name="employees"
    )
    

    class Meta:
        db_table = 'student'
        managed = True
```
Acima temos o cenário de como seria o modelo da classe Student, podemos observar o relacionamento com `class_room`, e o atributo `related_name` com o valor `employees` que é nome da ponte que o acesso estrangeiro devo utilizar ou seja, quando queremos acessar os estudantes a partir de turma devemos usar `related_name` como a ponte para isso.

## Utilizando Selected Related

Quando fazemos uma consulta que envolve mais de uma consulta na mesma tabela, por exemplo, quando queremos pegar as matérias de um estudante, existem dois modelos(tabelas) envolvidos nessa consulta, o modelo de estudante e o modelo de matérias, nesse cenário o django pode fazer uma consulta na tabela de matéria, pra cada estudante que existir, isso é ruim, torna a consulta lenta, e pode ser resolvido com apenas uma query, ou seja, com apenas uma consulta, para que o rest framework possa atuar dessa maneira, podemos passar um método do `Manager` chamado `selected_ralated()`, dessa forma o Django irá evitar fazer consultas repetidas, vamos ver o exemplo abaixo.

```py
from basic import models

models.Student.objects.selected_related('subject').filter(subject__name__icontains="matemática").
```
No exemplo a cima estamos consultando via o modelo de estudantes todos os que tem a matéria matemática, podemos perceber que estamos chamando o método `.selected_related('subject')`, informando qual o relacionamento que temos na consulta, para evitar a múltipla consulta sem necessidade.
Outra coisa que é um benefício de usar o verbo, é a questão de `LazyLoad`, em cenários onde temos uma consulta que irá retornar outros objetos dentro, usar o `selected_related` é uma boa prática, assim se tem a garantia de que a resposta dos dados será como esperado.

## Utilizando Prefetch Related

Assim como vimos acima, o uso do `prefetch_related` tem a mesma funcionalidade, a diferença é que usamos em consultas onde o resultado será uma lista, por exemplo, acima fizemo um consulta para pegar a matéria de cada estudante, aqui vamos fazer uma consulta para pegar todos os alunos estão estudando em uma determinada matéria, nesse caso a consulta irá começar pelo lado da matéria, vejamos o exemplo abaixo.

```py
from basic import models

models.Subject.objects.prefetch_related('student').filter(name__icontains="Português").values('name', 'student_name')
```
Acima no nosso exemplo a partir do modelo de matéria, estamos fazendo uma consulta onde informamos a relação com estudante `.prefetch_related('student')`, novamente evitando consultas não necessárias.

## Utilizando Lpad

Agora no nosso estudo vamos ver uma função do django utilizada para incrementar dados em uma String, chamada `LPad`, vejamos o exemplo abaixo.

```py
from basic import models
from django.db.models import CharField
from django.db.models.functions import Cast, F, Value, LPad

models.Student.objects.annotate(
    code=LPad(
        Cast(F('id'), output_field=CharField()),
        5, Value('0')
    )
)
```
No exemplo acima, fizemos uma consulta no modelo de estudantes, criamos uma anotação onde temos um campo chamado `code` que recebe nossa função `LPad`, essa função basicamente espera uma `expressão`, um `tamanho` e uma `string`, a expresão usamos o `Cast(F('id'), output_field=CharField())` que está pegando nosso id e convertendo para string, o tamanho é `5` e a string é `Value('0')`, traduzindo tudo isso estamos dizendo que nosso campo `code` será o id + 5 zeros a esquerda.

## Utilizando Upper e Lower

Um exemplo das funções `Upper` e `Lower`.

```py
from basic import models
from django.db.models.functions import Upper, Lower

models.Student.ojects.annotate(
    _upper=Upper('name'),
    _lower=Lower('name')
).values('name', '_upper', '_lower')
```

## Utilizando Aggregate e agrupadores (Max, Min, Avg, Min, Sum, Count)

Agora vamos ver um método do manager que serve para usarmos com agrupadores numéricos, esses agrupadores trazem sempre um valor final na sua consulta, `Max` traz o maior valor entre os listados, `Min` traz o menor, `Avg` traz a média, `Sum` a soma total, e o `Count` conta a quantidade total, vejamos os exemplos abaixo

* Max - Pegando a maior idade
```py
from django.db.models import Max, Min, Avg, Count, Sum
from basic import models

models.Student.objects.aggregate(max=Max('age'))
```

* Min - Pegando a menor idade
```py
from basic import models

models.Student.objects.aggregate(min=Min('age'))
```

* Avg - Pegando a idade média
```py
from basic import models

models.Student.objects.aggregate(avg=Avg('age'))
```

* Sum - Soma total das idades
```py
from basic import models

models.Student.objects.aggregate(sum=Sum('age'))
```

* Count - Pegando a quantidade total das idades
```py
from basic import models

models.Student.objects.aggregate(count=Count('age'))
```
Também podemos usar as funções acima com o annotate, utilizamos o aggregate em casos que queremos apenas retornar o total e nenhum dado a mais, mas por exemplo se quisermos retornar a quantidade de alunos por uma matéria podemos usar o annotate pra isso.

```py
from basic import models

models.Subjects.objects.prefetch_related('student').annotate(
    total=Sum('student__id')
).values('name', 'total')
```

No exemplo acima conseguimos a resposta da consulta trazendo o nome de cada matéria e o total de estudantes.