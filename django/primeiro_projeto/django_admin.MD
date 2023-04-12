# Django Admin

Nesse estudo vamos falar sobre o `Django Admin` que é uma ferramenta muito interessante que já vem dentro do Django, basicamente essa ferramenta nós permite acessar uma interface de gerenciamento da nossa api, assim também como de forma fácil nos permite criar interfaces para nossas classes, onde podemos consultar, editar e remover dados, dependendo da complexidade do projeto o Django admin pode até ser utilizado como uma interface de uso final.

# 1. Acessando o Django Admin

Para acessarmos a interface do `admin` primeiramente precisamos colocar o nosso servidor para rodar, para isso bastar ir no `pycharm` e clicar no botão verde que está no topo com ícone de `play`, se tudo estiver sido feito corretamente até aqui o seu servidor irá iniciar e uma mensagem parecida com essa irá ser apresentada.

```sh
Watching for file changes with StatReloader
System check identified no issues (0 silenced).
April 11, 2023 - 23:45:33
Django version 4.2, using settings 'sale.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
Após isso basta acessar via navegador a seguinte `url`.

> http://localhost:8000/admin/

# 2. Criando um super usuário

Agora que estamos acessando nosso admin, vamos nos deparar com uma tela onde será solicitado login e senha, então precisamos criar um usuário para que possamos fazer o acesso. Vamos criar um `super usuário` rodando o seguinte comando.

```sh
python manage.py createsuperuser
```
Um passo a passo irá aparecer pedindo, `nome`, `email` e `senha`, se nenhum nome for informado ele irá usar o do sistema, o email não é obrigatório, a senha sim.
Após realizar o processo basta inserir os dados de usuário e senha na interface web e acessar o painel admin.

# 3. Criando uma interface

Agora que já acessamos nosso painel, vamos criar uma interface para nossa classe `Course`, o processo para isso é muito simples, primeiramente acesse o arquivo `basic/admin.py` e dentro dele adicione o seguinte código.

```py
from django.contrib import admin
from basic import models


@admin.register(models.Course)
class CourseAdmin(admin.ModelAdmin):
    list_display = ['id', 'name', 'updated_at', 'active']
    list_display_links = ['id', 'name', 'updated_at', 'active']
    search_fields = ['name']
    list_filter = ['active']

```

Como podemos ver é um código bem simples usando os mesmo conceitos que usamos anteriormente, nesse caso precisamos registrar o modelo no `admin` para que possamos utilizar, isso é feito no seguinte trecho de código `@admin.register(models.Course)`.
Os dois primeiros atributos da classe `list_display` e `list_display_links` são referentes aos campos do modelo que desejamos exibir na tela e qual delas são `links clicáveis` para navegar para o formulário de detalhes e edição.
O Terceiro campo é referente aos campos que desejamos poder filtrar, e o quarto é referente a uma lista de filtros que aparece na lateral.
Existem outros campos que podem ser configurados e todos podem ser verificados na documentação.
Agora podemos acessar a interface no navegador pelo caminho.

> http://localhost:8000/admin/basic/course/

A interface é mostrada onde você pode fazer todas as opções de gerenciamente dos dados, listagem, cadastro, edição e remoção de dados.