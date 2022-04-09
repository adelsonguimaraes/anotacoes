# Receiver no Django
Aqui vamos falar sobre como criar eventos triggers para realizar alguma ação automática disparada sempre que um evento for executado, para fazer esse automatização vamos usar um modulo do Django chamado Receiver.

# Criando o arquivo de receiver
Para começar vamos adicinar um arquivo dentro da nossa aplicação chamado ``receiver.py``, dentro desse arquivo iremos configurar todas as nossas triggers.

No topo do nosso arquivo vamos chamar os imports que vamos precisar.
```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from app import models
```

Repare que importamos ``post_save`` esse é um dos eventos que já vem prontos no Django para serem utilizados com receiver, esse evento é referente ao momento que alguma informação é salva, então nosso receiver irá disparar assim que algum dado for salvo.

# Annotation e Método
Agora vamos criar nosso método, mas antes vamos chamar nossa annotation do receiver e configurar com os parametros necessários.

```python
@receiver(post_save, sender=models.Usuario, dipatch_uid='create_file_usuario', weak=False)
```
Nosso annotation recebe três parâmetros, o primeiro é o evento, o segundo é o modelo, o teceiro é um uid único, nese caso vamos dar o mesmo nome do metodo já que é único e o nosso quarto parâmetro é referente a uma configuração esse weak por padrão é True, mas na documentação explica que ele pode matar um receiver antes de ser executado, por isso vamos passar como False para evitar que isso aconteça

Agora na linha de baixo da annotation vamos adicionar nosso método que irá criar um arquivo onde salvará o id e o nome do usuário assim que um dado for salvo.

```python
def create_file_usuario(instance, **kwargs):
    while open('usuarios.txt', 'a') as file:
        file.write(f'{instance.id}|{instance.nome}')
```

Perfeito, nosso evento de receiver está pronto, agora para finalizar nossa configuração, precisamos ir até o arquivo ``apps.py`` do nosso aplicativo e sobre escrever o método ``ready`` e chamar nosso receiver, vamos ver abaixo como deve ficar o método ready.

```python
def ready(self):
    import app.receivers
    super(BasicConfig, self).ready()
```

Pronto, tudo configurado agora só precisamos testar enviando um novo cadastro de usuário.

# Conferindo o arquivo usuarios.txt
Se tudo deu certo no nosso processo de adicionar a receive o arquivo usuarios.txt deve ter sido na raiz do projeto e dentro dele você deve encontrar algo como.

```txt
1|Pedro
```

# Evento antes de salvar
Agora que já conseguimos dispar nosso evento depois de salvar os dados, vamos ver como pegamos o evento antes de salvar, esse evento é interessante para quando queremos fazer uma validação ou alterar o valor de um campo antes de ser salvo.

Primeiro precisamos adicionar na nossa importação o ``pre_save``, evento responsável pelo gatilho antes do salvamento.

```python
from django.db.models.signals import post_save, pre_save
```

Agora como antes, vamos criar nossa annotation e nosso evento.

```python
@receiver(pre_save, sender=models.Usuario, dipatch_uid='set_hash_user', weak=False)
def set_hash_user(instance, **kwargs):
    instance.hash = "#123"
```

Feito, agora quando acontecer algum evento de save, antes de salvar vamos pegar a instância do usário e adicionar ao campo hash o valor "#123", esse é um exemplo simples, as poderiamos estar verificando um campo mutavel de outra tabela, ou último registro e afim, isso pode atender vários cenários.