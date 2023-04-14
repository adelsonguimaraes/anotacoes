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