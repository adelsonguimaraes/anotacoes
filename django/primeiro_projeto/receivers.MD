# Receivers Django

Agora vamos estudar sobre as receivers, que são gatilhos que podemos criar para eventos que acontevem no modelo, por exemplo, podemos pegar o evento de salvar de um modelo, e quando esteve evento ocorrer podemos fazer alguma ação.

## 1. Criando arquivo

Primeiramente precisamos criar um arquivo dentro de `basic` um arquivo chamado `receivers.py`.

```py
from django.db.models.signals import post_save
from django.dispatch import receiver
from basic import models

@receiver(post_save, sender=models.Course, dispatch_uid='create_file_course')
def create_file_state():
    with open('course.txt', 'a') as files:
        file.write('f{instance.id}|{instance.name}')
```
No código acima criamos nosso receiver, associamos ao modelo de curso, precisamos fazer alguns imports como podemos ver no topo do arquivo, o primeiro deles foi `post_save` que é um método pré-definido, representa o evento que ocorre quando salvamos em um modelo, também importamos o `receiver` que é uma anotação onde fazemos as configurações, o primeiro parámetro é referente ao método que será usado, neste caso o `post_save`, em seguida qual o sender, que representa o modelo nesse caso `models.Course`, e por último precisamos informar um `uid` para identificar esse receiver, aqui podemos apenas usr o mesmo nome do nosso método.
Em seguida temos o método, que será executado quando o evento configurado acontecer, nesse caso sempre que o método salvar do modelo curso for chamado vamos executar esse método que irá criar ou editar um arquivo chamado `course.txt` e irá escrever o `id` e  `nome` que estão presentes na nossa instância.

## 2. Configurando o receiver no apps

Para finalizar nossa configuração e deixar o nosso receiver pronto para ser usado, precisamos editar o arquivo chamado `apss.py` que está na raiz da nossa pasta, dentro deles iremos sobreescrever o método chamado `ready`, vejamos abaixo o que precisa ser adicionado no final do arquivo.

```py
...
def ready(self):
    import receivers
    super(BasicConfig, self).ready()
```
Pronto, com isso configuramos para que nossas receivers sejam chamadas, para testarmos e ver o resultado basta fazer uma chamada para o endpoint de curso com um método POST para criar um novo curso ou até mesmo um PATCH para editar um curso existente.