# Múltiplos relacionamentos Django

Agora vamos estudar sobre relacionamento múltiplo no Django, isso ocorre quando temos classes que se relacionam mais de uma vez com outras.

# 1. Aplicando múltiplo relacionamento

Para aplicar nosso múltiplo relacionamento vamos alterar a nossa classe `Student`, atualmente um estudante pode apenas estar ligado a um curso, mas agora queremos poder permitir que um estudante possa se relacionar com vários cursos, para isso vamos fazer a seguinte modificação.

```py
class Student(ModelBase):
    name = models.CharField(max_length=100, null=False)
    
    class Meta:
        db_table = 'student'
        managed = True
```

Basicamente aqui o que fazemos é remover o relacionamento estrangeiro com a classe `Course`, em seguida vamos fazer uma migration para atualizar o nosso banco de dados coma remoção desse atributo.

```sh
python manage.py makemigrations
python manage.py migrate
```
Nosso segundo passo agora é criar uma nova classe que será nossa classe de relacionamento, ou seja, a classe que liga os alunos com os seus cursos.

```py
class StudentCourse(ModelBase):
    student = models.ForeignKey(
        to="Student",
        db_column="id_student",
        on_delete=models.DO_NOTHING,
        null=False
    )
    course = models.ForeignKey(
        to="Course",
        db_column="id_course",
        on_delete=models.DO_NOTHING,
        null=False
    )

    class Meta:
        db_table = "student_course"
        managed = True
        unique_together = [('student', 'course')]
```
Aqui como já vimos anteriormente criamos uma classe com atributos com relacionamento com outra classe, a diferença aqui está na linha.

```py
unique_together = [('student', 'course')]
```
Dentro da nossa classe `Meta` temos essa configuração, que significa que os dois campos devem ser de combinação única, ou seja, a combinação do valor dos campos não poderá se repetir, o que traduzindo diz que um estudante só poderar estar matrículado em um curso uma única vez.

Agora precisamos novamente criar e rodar nossas migrations para que nossa nova classe de relacionamento seja refletida no banco de dados.

```sh
python manage.py makemigrations
python manage.py migrate
```
# 2. Alternativa

Uma alternativa para esse relacionamento é utilizar a automatização do Django, geralmente quando o relacionamento é simples e não possui atributos que serão manipulados além dos relacionamentos podemos utilizar essa forma, porém não teremos controles sobre a sua estrutura.
Nesse caso não criariamos a nossa classe de relacionamento, apenas alterariamos a classe `Student`, veja o modelo abaixo.

```py
class Student(ModelBase):
    name = models.CharField(max_length=100, null=False)
    students = models.ManyToManyField(
        to="Student"
    )

    class Meta:
        db_table = 'student'
```     
Como nesse caso estamos apenas exemplificando, não iremos criar às migrations. 

## 3. Alimentando a tabela relacional

Vamos agora fazer um pequeno script que irá alimentar a nossa tabela de relacionamento `StudentCourse`, para isso vamos abrir nossa aba `Python Console` no rodapé da nossa IDE pycharm, insirá o script a seguir e rode para executar a alimentação dos dados.

```py
from basic import models

students = models.Student.objects.all()
courses = models.Course.objects.all()

for s in students:
    for c in courses:
        cs = models.StudentCourse()
        cs.course = c
        cs.student = s
        cs.save()
```

## 4. Crian a ponte entre as classes

Agora vamos ver como criar uma "ponte" nas classes relacionadas para o acesso via django ser muito mais fácil, para isso vamos adicionar nas duas classe o que vimos mais acima o `ManyToMany`, a diferença aqui é que já temos a nossa classe associativa criada, e vamos informar para o Django qual é essa classe, assim ele não irá criar uma tabela automáticamente e usará a nossa.

```py
class Course(ModelBase):
    name = models.CharField(max_length=40, null=False)
    students = models.ManyToManyField(
        to="Student",
        through="StudentCourse"
    )

    class Meta:
        db_table = 'course'
        managed = True


class Student(ModelBase):
    name = models.CharField(max_length=100, null=False)
    courses = models.ManyToManyField(
        to="Course",
        through="StudentCourse"
    )

    class Meta:
        db_table = 'student'
        managed = True
```
Nossas classes ficarão dessa forma, perceba que dentro do ManyToMany temos duas configurações, o `to` que é onde referenciamos a classe de relacionamento e o `through` que aponta qual é a class associativa, com essa configuração agora podemos fazer as consultas de estudantes e cursos e cursos de alunos diretamente, segue um exemplo abaixo mostrando todos os alunos matriculados em um curso.

```py
from basic import models

course = models.Course
course = models.Course.objects.first()

for s in course.students.all():
    print(s.name)
```

Aqui finalizamos nosso estudo sobre `múltiplo relacionamento`, no nosso próximo estudo vamos entender como podemos fazer no caso de relacionamento de 1 para 1, com `Related Name`, para seguir para o próximo estudo clique no link a seguir.

[Próximo passo](./related_name.MD) - Trabalhando com Related Name.