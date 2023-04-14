# Queries (consultas) Django

Agora no nosso estudo vamos ver sobre `queries` no Django, que nada mais são que consultas no banco de dados usando a estrutura do próprio django.

## 1. Com funciona as queries

Diferente de outros frameworks todo organismos do django se baseia nos modelos, com as queries não é diferente, quando queremos fazer consultas nos dados, partimos de um modelo e utilizamos uma ferramenta chamada `Managers` que são responsáveis por fazer as consultas padrões do django, com eles podemos listar, consultar por atributos, fazer filtros, ordenação, selecionar os campos que devem ser consultados e muito mais.

## 2. Listando
Vamos ver agora como é simples fazer uma consulta que lista todos os itens de uma tabela utilzando o manager do django.

```py
models.Student.objects.all()
```
Acima temos a consulta que irá trazer uma lista com todos os alunos que estiverem no banco de dados, perceba que a nossa consulta começa acessando o modelo `Student`, em seguida acessamos o manager(objects), e apartir dele chamamos uma função de consulta `all()`, o manager retorna um objeto interável que ele chamada de queryset, ou seja um objeto que é a query não executada, e apartir do momento que chamamos uma função de consulta como o all() ele faz a consulta no banco de dados.

## 3. Pegando primeiro item

Para pegar o primero item que tivermos no banco podemos usar a função `.first()` do manager, isso irá trazer apenas o primeiro dado.

```py
models.Student.objects.first()
```

Quando esse comando é executado podemos ver que apenas um registro é retornado na consulta.


## 4. Consultando um item por propriedade

Podemos também fazer uma consulta por propriedade usando a função `.get()` do manager, importante dizer que essa função irá trazer apenas um dado na consulta, a primeira ocorrência que atender a consulta.

```py
models.Student.objects.get(name='Joao')
```
A nossa consulta acima irá retorna um item do banco de dados se encontrar algum estudante que tenha o nome de Joao, mesmo que exista mais de um Joao ela irá trazer apenas a primeira ocorrência.

## 5. Ordenando a Consulta

Quando fazemos uma consulta de dados muitas vezes queremos poder ordenar a disposição dos dados, isso é muito útil em vários momentos quando precisamos mostrar os dados de uma forma ordernada seja por nome, idade, data ou um campo de valor por exemplo, para isso vamos usar a função `order_by()` do manager.

```py
models.Student.objects.order_by('name')
```
Acima estamos fazendo uma ordenação da lista de estudantes pelo nome de forma crescente ou seja do A para o Z, podemos ainda fazer a ordenação de forma decrescente do Z para o A, pra isso basta colocar um sinal de `-` na frente do nome do campo que estamos utilizando para ordernar.

```py
models.Student.objects.order_by('-name')
```
## 6. Selecionando os valores

Nós também podemos fazer uma seleção dos campos que queremos mostrar em uma consulta, naturalmente as consultas trazem todos os campos, na maioria dos cenários não precisamos de todos os dados então escolhemos aqueles que devem ser retornados na consulta, para isso utilizamos a função `values()` do manager.

```py
models.Student.objects.values('id', 'name')
```
Na consulta acima utilizamos o values e informamos que das colunas que existem queremos apenas o `id` e o `name`, com isso ele irá trazer a lista dos estudantes apenas com os campos selecionados.

## 7. Filtrando dados

Uma função muito importante quando trabalhamos com dados é o filtro, para que possamos consultar dados com mais precisão, no manager também temos uma função chamada `filter()` que faz uma consulta mais complexa nos dados e retorna uma lista com os dados onde ele encontra o valor, é muito parecido com o que o método `get()` que vimos antes, porém o get retornava apenas a primeira ocorrência da consulta, no caso do filter irá retornar uma lista com as ocorrências encontradas.

```py
models.Student.objects.filter(name__icontains='Joao')
```
Na consulta acima estamos filtrando todos os estudantes que no nome contenha Joao, aqui utilizamos uma constante isolada do manager `__icontains` ela faz o papel de verificar se em alguma parte do campo contém a palavra que informamos.

## 8. Constantes de consulta (Field Lookups)

O manager é uma ferramenta muito poderosa e complexa, e dentro das suas consultas existem as constantes isoladas, que são parâmetros internos da consulta que utilizamos quando queremos algum comportamento especial na consulta, acima vimos o exemplo do `__ìcontains` que verifica se a nossa pesquisa contém dentro do campo consultado.
Essas constantes refletem consultas com `Where` na base de dados, essa é uma das coisas mais usadas nas consultas, para verificar mais sobre as contanstes visite o link da documentação onde explicam mais a fundo sobre elas.

> https://docs.djangoproject.com/en/4.2/topics/db/queries/#field-lookups

