# Receivers Django

Agora vamos estudar sobre as receivers, que são gatilhos que podemos criar para eventos que acontevem no modelo, por exemplo, podemos pegar o evento de salvar de um modelo, e quando esteve evento ocorrer podemos fazer alguma ação.

## 1. Criando arquivo

Primeiramente precisamos criar um arquivo dentro de `basic` um arquivo chamado `receivers.py`.

```py
from django.db.models.signals import post_save
from django.dispatch import receiver
from basic import models

@receiver(post_save, sender=models.Course, dispatch_uid='create_file_course', weak=False)
def create_file_state():
    with open('course.txt', 'a') as files:
        file.write('f{instance.id}|{instance.name}')
```
No código acima criamos nosso receiver, associamos ao modelo de curso, precisamos fazer alguns imports como podemos ver no topo do arquivo, o primeiro deles foi `post_save` que é um método pré-definido, representa o evento que ocorre quando salvamos em um modelo, também importamos o `receiver` que é uma anotação onde fazemos as configurações, o primeiro parámetro é referente ao método que será usado, neste caso o `post_save`, em seguida qual o sender, que representa o modelo nesse caso `models.Course`, precisamos também informar um `uid` para identificar esse receiver, e por útlimo temos o parâmetro `weak=False` que é para evitar que o python mate o receiver antes de ser executado, aqui podemos apenas usr o mesmo nome do nosso método.
Em seguida temos o método, que será executado quando o evento configurado acontecer, nesse caso sempre que o método salvar do modelo curso for chamado vamos executar esse método que irá criar ou editar um arquivo chamado `course.txt` e irá escrever o `id` e  `nome` que estão presentes na nossa instância.

## 2. Configurando o receiver no apps

Para finalizar nossa configuração e deixar o nosso receiver pronto para ser usado, precisamos editar o arquivo chamado `apss.py` que está na raiz da nossa pasta, dentro deles iremos sobreescrever o método chamado `ready`, vejamos abaixo o que precisa ser adicionado no final do arquivo.

```py
...
def ready(self):
    import basic.receivers
    super(BasicConfig, self).ready()
```
Pronto, com isso configuramos para que nossas receivers sejam chamadas, para testarmos e ver o resultado basta fazer uma chamada para o endpoint de curso com um método POST para criar um novo curso ou até mesmo um PATCH para editar um curso existente.

## 3. Evento Pre-Save

Como foi dito antes podemos utilizar vários métodos que são relativos a eventos para fazer nosso gatilho, no exemplo anterior utilizamos o post_save que é o evento que ocorre logo após salvar, nesse caso não poderiamos por exemplo salvar na mesma classe se fosse preciso adicionar dados, pois isso causaria uma recursividade, poderiamos salvar em outra modelo ou como fizemos em um arquivo, quando precisamos alterar alguma informação dentro do modelo antes de salvar devemos utilizar um evento que aconteça antes do save, para isso podemos utilizar o método `pre_save`, vamos ver abaixo um exemplo

```py
from django.db.models.signals import pre_save
from django.dispatch import receiver
from basic import models

@receiver(pre_save, sender=models.Course, dispatch_uid='update_course_name', weak=False)
def update_course_name(instance, **kwargs):
    instance.name = instance.name + '_teste'
```
No exemplo acima criamos um receiver para atualizar o nome do curso quando o método `save` for chamado, o gatilho acontecerá antes dos dados serem salvos, com isso nosso método que adiciona ao nome do curso `_teste` será executado antes de salvar, e quando salvar a alteração do nome já está presente.