# Related Name Django

Agora vamos estudar sobre `Related Name`, assim como fizemos no estudo anterior o related name é utilizado para facilitar as consultas entre tabelas que possuem relacionamento, não utilizamos ele em `ManyToMany` pois a configuração de ponte que fazemos lá já faz essa função, então só vamos precisar usar ele em relacionamentos 1 para 1.

## 1. Aplicando Related Name

Vamos adicionar agora uma classe chamada `ClassRoom` que será relativa a sala onde cada estudante fica, nosso relacionamento será simples do tipo 1 para 1.

```py
class ClassRoom(ModelBase):
    name = models.CharField(max_length=100, null=False)
```
Agora vamos alterar nossa classe `Student` para receber o relacionamento com a classe `ClassRoom`.

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
        related_name="students"
    )

    class Meta:
        db_table = 'student'
        managed = True
```

Acima alteramos nossa classe adicionando o relacionamento novo, e dentro do relacionamento utilizamos um novo atributo chamado `related_name='students'`, essa atributo é a nossa ponte para quando quisermos consultar os estudantes relacionados a partir da turma, vamos ver como seria essa consulta.

```py
class_room = models.ClassRoom.objects.first()
for s in class_room.students.all()
    print(s.name)
```
No exemplo assim pegamos a primeira classe, e em seguida fazemos um laço que percorre todos os estudantes que estão naquela classe, podemos perceber que utilizamos a ponte `students` que é nosso `related_name`. Aqui finalizamos o nosso estudo sobre related name, nosso próximo estudo irá abordar consultas de dados com o Django, para ir para o próximo estudo clique no link a seguir.

[Próximo passo](./queries.MD) - Trabalhando com Queries (Consultas).