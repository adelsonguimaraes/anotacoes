# Managers Django

Quando falamos sobre as queries do django falamos sobre os métodos que precisamos utilizar que vem do `manager`, que são utilizados para fazermos as consultas, agora vamos estudar como podemos fazer os nossos métodos personalizados, isso é possível para que, quando houverem consultas muito complexas e extensas, possamos transforma-las em métodos do manager da classe.

## 1. Criando o arquivo de managers

Para iniciar nossa criação de métodos precisamos criar um novo arquivo na pasta do app, cada aplicativo tem seu próprio manager que é relacionado aos modelos que estão presentes nesse app, então vamos criar um arquivo chamado `manage.py` em `basic/`.

## 2. Criando uma consulta no manager

Agora que temos o nosso arquivo vamos criar nossa consulta.

```py
from django.db.models import Manager, F, Count

class StudentManager(Manager):
    def total_students_per_age(self):
        return self.get_queryset().values('age').annotate(
            total=Count('*')
        ).values('age', 'total')
``` 
No código acima criamos uma class `Manager` para o estudante, no padrão que já vinhamos fazendo nas classes anteriores, precisamos importar também uma super classe chamada `Manager` de `django.db.models`, depois injetamos ela na nossa classe, dentro da nossa classe criamos nosso primeiro método chamado `total_students_per_age` que relfete o contexto do que iremos fazer, aqui usamos um método interno do `Manager` chamado `get_queryset` para pegar o queryset da classe, em seguida fazer uma consulta agrupando por idade, e calculando o total de estudantes, por ultimo fazemos um `.values` retornando apenas a idade e o total.
Em seguida preciamos adicionar o seguinte código no modelo de estudante.

```py
...
from basic import managers
...
objects = managers.StudentManager()
...
```
Pronto agora nosso manager customizado foi inserido no manager do nosso modelo e podemos usá-lo assim como usamos os demais métodos padrões do manager, vamos ver como chamar abaixo.

```py
from basic import models

models.Student.objects.total_students_per_ager()
```
