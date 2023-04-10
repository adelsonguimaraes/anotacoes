# Iniciando um projeto com Django

Aqui veremos como iniciar do zero um projeto com Django, assim também como tudo que é necessário para realizar está atividade.

## 1. Pré-requisito

Para iniciarmos esse estudo precisamos atender alguns pré-requistos que são necessários para seguir para os próximos passos.

1. Python
2. Pip
3. Django
4. Pycharm

### 1.1 Python

Django é um framework que utiliza python como a linguagem de desenvolvimento, então precisamos instalar o python para prosseguir.
Aqui vou utilizar linux como o sistema base para o desenvolvimento, por padrão o sistema já traz o linux intalado, mas se no seu caso não estiver instalado ou estiver em outro SO pode seguir o passo a passo do site oficial da linguagem.

> https://www.python.org/downloads/

### 1.2 Pip

Também vamos precisar do instalador de pacotes do python, o pip, para instalar o mesmo você pode seguir o site oficial

> https://pip.pypa.io/en/stable/installation/

ou no caso ainda do python pode rodar o comando de instalador de pacotes do próprio linux.

```sh
apt-get install pip
```

### 1.3 Django

Pra finalizar nossos pré-requisitos precisamos instalar o nosso django, para isso também podemos seguir a documentação oficial.

> https://docs.djangoproject.com/en/4.2/topics/install/

mas pra facilitar você pode rodar o seguinte comando no terminal com o instalador do python (pip).

```sh
pip install django
```

### 1.4 Pycharm

Um pré-requisto extra que vamos usar aqui vai ser a IDE Pycharm, não é um requisito obrigatório, porém ele ajuda muito na produtividade pois foi criado exclusivamente para trabalhar com Django. Para instalar a IDE siga a documentação oficial.

> https://www.jetbrains.com/pt-br/pycharm/

Um dica para o linux é instalar utilizando o gerenciador de pacotes flatpak.

> https://flathub.org/apps/details/com.jetbrains.PyCharm-Community

## 2. Criando o projeto

Agora que já configuramos todos os nossos pré-requisitos necessários, podemos seguir com nosso estudo, vamos agora criar nosso projeto. Para isso escolha um local pra colocar o seu projeto em seu computador, vamos chamar nosso projeto de `sale`, em seguida abra o terminal e rode o seguinte comando.

```sh
django-admin startproject sale 
```

