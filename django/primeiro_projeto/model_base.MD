# Model Base (Herança) Django

Agora no nosso estudo vamos falar sobre `herança`, o conceito basicamente é aplicado quando temos uma classe que chamamos de `super` e outras classes que `extendem suas própriedades e funções`, isso ocorre bastante com as classes do django como já vimos até aqui onde injetamos classes para usar suas funções, porém nesse cenário iremos criar a nossa própria super classe para aplicar a herança.

# 1. Criando Model Base

Geralmente quando temos propriedades que são muito comuns em várias classes como `id`, `data de criação`, `ativos` e `afins`, criamos uma classe que chamamos de `Model Base`, essa será nossa super classe e todas as outras que tiverem necessidade iram herdar delas.

```py
class ModelBase(models.Model):
    id = models.AutoField(primary_key=True)
    created_at = models.DateTimeField(null=False, auto_now_add=True)
    modified_at = models.DateTimeField(null=False, auto_now=True)
    active = models.BooleanField(null=False, default=True)

    class Meta:
        abstract = True
```

Criamos a nossa nova classe no topo do arquivo `models`, dentro da classe interna `Meta` configuramos que a classe é `abstract`, quando dizemos qua classe é abstrata o Django não irá gerar a mesma no banco de dados e ela só existirá no Django.
O segundo passo para nossa modificação é alterar nossas demais classes removendo todos os campos que já estão na super classe (ModelBase) e alterar a injeção da herança de `models.Model` para `ModelBase`, como podemos ver na classe a baixo.

```py
class Course(ModelBase):
    name = models.CharField(max_length=40, null=False)

    class Meta:
        db_table = 'course'
        managed = True
```

Assim aplicamos nosso conceito de herança, criando a nossa super classe `ModelBase` e injetando ela nas demais classes para herdar seus atríbutos e funções, interessante observar que nossa classe `ModelBase` injeta como dependência de herança a `models.Model`, então assim todas as outras classes que injetarem nossa super class também estarão injetando os comportamentos de `models.Model`.