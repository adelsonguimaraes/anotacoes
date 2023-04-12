# Migration populativa Django

Nesse estudo vamos aprender sobre `migrations populativas`, quando necessitamos que dados sejam inseridos desde o nascimento da classe, criamos uma migration populativa, o papel da mesma é inserir dados iniciais em nosso banco de dados, um exemplo é quando temos uma classe que trata de estados, podemos popular a tabela de estados com uma migration.

# 1. Criando a migration populativa

A criação de uma migration populativa ocorre de forma totalmente manual, podemos criar a mesma do zero ou copiar de outra migration e apenas alterar o que é necessário, podemos consumir dados de um array ou até mesmo de um arquivo json por exemplo. Abaixo temos nosso exemplo de migration.

```py
from django.db import migrations


def create_marital_status(apss, schema_editor):
    marital_status_list = ["CASADO", "SOLTEIRO", "DIVORCIADO", "VIÚVO"]
    MaritalStatus = apss.get_registered_model('basic', 'MaritalStatus')

    for ms in marital_status_list:
        marital_status = MaritalStatus()
        marital_status.name = ms
        marital_status.save()


class Migration(migrations.Migration):
    dependencies = [
        ('basic', '0001_initial')
    ]

    operations = [
        migrations.RunPython(create_marital_status)
    ]

```

O primeiro passo foi fazer uma cópia do arquivo `basic/migrations/0001_initial.py` e chamar de `0002_initial.py`, em seguida adicionamos todo o código acima dentro desse aquivo, aqui temos uma função que irá fazer todo o processo de salvar os dados, perceba que nossos dados é uma lista simples.

```py
marital_status_list = ["CASADO", "SOLTEIRO", "DIVORCIADO", "VIÚVO"]
```
Poderia ser um arquivo json, mas como os dados são mais simples aqui, não houve a necessidade. Pegamos o modelo que queremos salvar e fazer a lógica de loop salvando cada um dos dados da nossa lista.
Dentro da classe `Migration` podemos observar que é necessário referenciar a migration anterior a nossa.

```py
class Migration(migrations.Migration):
    dependencies = [
        ('basic', '0001_initial')
    ]

    operations = [
        migrations.RunPython(create_marital_status)
    ]
```

Isso é importante para que o Django saiba qual sequência que deve ser executa, e por fim temos uma função da migration que executa nossa função.
Na sequência se rodarmos o comando.

```sh
python manage.py showmigrations
```
Podemos observar que a nossa migration agora aparece na lista como não executada, depois bastar rodar o comando.

```sh
python manage.py migrate
```
Nossa migration será executada e a tabela do campo será alimentada.