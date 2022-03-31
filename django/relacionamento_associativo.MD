# Criando relacionamento associativo
Aqui vamos ver como criar um relacionamento associativo no Django, para quando tivermos tabelas associativas.

Existem duas formas de fazer o relacionamento associativo, uma delas é permitindo o gerenciamento pelo pŕoprio ``Django``, com isso ele irá criar a tabela associativa e todos os campos seguiram a nomeclatura padrão.

A outra forma é nós criarmos uma classe associativa manualmente, a vantagem dessa segunda forma é que temos o total controle do nome dos campos e também podemos criar outros campos alémo dos ids, caso exista a necessidade de sinalizar nessa associação se está ativo ou precisa saber a data daquela associação, temos controle sobre isso.

Vamos fazer aqui da segunda maneira, ela é mais completa e interessante do que a primeira forma.

## Criando a classe associativa
Vamos primeira mente imaginar um cenário para que possamos seguir com a criação da nossa classe associativa, abaixo temos o modelo das duas tabelas que precisam ser associadas.

- Categoria

    | id | nome    |
    |----|---------|
    | 1  | Ação    |
    | 2  | Comédia |

- Filme

    | id | nome              |
    |----|-------------------|
    | 1  | Duro de Matar     |
    | 2  | As Branquelas     |

Preciamos criar a associação entre a tabela Categoria e Filme, para isso vamos criar nossa classe associativa, segue abaixo o código.

```python
class categoria_filme(models.Model):
    categoria = models.ForeignKey(
        to = 'Categoria',
        on_delete = models.DO_NOTHING,
        db_column = 'id_course',
        null = False
    )
    filme = models.ForeignKey(
        to = 'Filme',
        on_delete = models.DO_NOTHING,
        db_column = 'id_course',
        null = False
    )
    data_cadastro = models.DateTime(null=False, auto_now_add=True)

    class Meta:
        db_table = 'categoria_filme'
        managed = True
        unique_together = [('categoria', 'filme')]

```

Pronto, criamos a nossa classe associativa, como foi dito anteriormente temos total liberdade sobre a nomeclatura e campos, nesse caso adiionamos mais um campo que não existiria se deixassemos a gerencia com o Django, nosso campo data_cadastro que é um DateTime e no momento do cadastro automaticamente o Django irá preencher com data e hora.

## Adicionando Many To Many a classe filme

Agora vamos adicionar dentro da nossa classe de filme a relação com a tabela assossiativa, chamado de Many to Many no Django, isso vai facilitar para buscar associações de filme com categorias e também adicionar categorias aos filmes.

Segue abaixo o trecho de código para adicionar dentro da classe de filme.

```python

class Filme(models.Model):
    
    ...

    categorias = models.ManyToManyField(
        to='Categoria',
        through='CategoriaFilme'
    )
```

Pronto agora nossa classe filme está integrada com a classe associativa, também é possível fazer isso na classe categoria, mas aqui vamos apenas deixar na classe filme, agora vamos criar a migração dessa atualização.

# Criando a Migration
Para criar a migration da nossa modificação para Classe Associativa, vamos utilizar o comando abaixo.

```sh
python manage.py makemigrations
```

Pronto agora vamos rodar nossa migração e atualizar o banco.

```sh
python manage.py migrate
```

Se tudo deu certo nosso banco de dados foi atualizado corretamente, assim finalizamos a nossa criação de relacionamento associativo.