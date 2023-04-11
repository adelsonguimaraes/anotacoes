# Integridade relacional Django

Agora vamos estudar sobre relacionamentos entre classes e sua integridade, o que ocorre quando tempo uma classe que precisa se relacionar com outra, como exemplo aqui tempo a classe `Course` que são os cursos, e agora iremos criar uma nova classe chamada `Student` que são os alunos e cada aluno irá se relacionar com um curso.

## 1. Criando a classe Student

Agora vamos adicionar o código abaixo que é referente a nossa classe `student`.

```py
class Student(ModelBase):
    name = models.CharField(max_length=100, null=False)
    course = models.ForeignKey(
        to='Course',
        on_delete=models.DO_NOTHING,
        db_column='id_course',
        null=False
    )

    class Meta:
        db_table = 'student'
        managed = True
```

Acima temos a nossa classe, vamos analisar um tipo de atríbuto novo que temos nessa classe.

```py
course = models.ForeignKey(
    to='Course',
    on_delete=models.DO_NOTHING,
    db_column='id_course',
    null=False
)
```
Aqui temos o atríbuto `course` que é do tipo `ForeugnKey`, ou seja, é do tipo chave estrangeira e referencia um relacionamento com a classe `Course`, nas configurações dizemos que `on_delete=models.DO_NOTHING` ou seja se o curso possuir estudantes relacionados a ele, `não poderá ser deletado`, também configuramos como será o nome do campo na tabela do banco de dados `db_column='id_course'`, se não fizermos essa configuração o Django irá colocar um nome automáticamente.
Com essa configuração desse atríbuto o Django entende que existe um relacionamento entre a classe `Couse` e `Student` e também irá refletir isso no banco de dados, assim a tabela de estudante recebendo a chave estrangeria de curso para cada registro.

Agora basta fazer o processo de migrations que aprendemos anteriormente para fazer com que nossa atualizações sejam refletidas no banco de dados, com isso terminamos o nosso estudo sobre `Integridade relacional`.