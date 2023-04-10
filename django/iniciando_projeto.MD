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

## 3. Abrindo o projeto com o Pycharm e criando Venv

Agora vamos abrir o nosso projeto com o Pycharm e criar nosso ambiente virtual (venv).

### 3.1 Abrindo o projeto

Depois de abrir o pycharm na tela inicial selecione a opção `Open` para abrir um projeto já existente, na nova janela que irá abrir, navegue até a pasta do seu projeto, selecione e clique em `OK`, com isso o seu projeto deve ser aberto dentro da IDE.

### 3.2 Criando o ambiente virtual (Venv)

Ainda na interface da IDE vamos clicar no canto inferior direito onde você deve ver algo como `Interpreter` ou `Python 2.3` ou algo nesse sentido, escolhemos a opção `Add interpreter`, uma nova janela irá ser mostrada, clique na opção `Base Interpreter` e selecione a versão do python, caso ele não esteja sendo reconhecido na lista, será necessário ir até o local do arquivo clicando nos 3 pontos, depois de selecionado basta clicar em `OK` e finalizar a configuração.

Para garantir que sua venv foi configurada corretamente no projeto, no memso local no canto inferior direito deve aparecer a versão do python e o nome da sua venv.

## 4. Instalando o Django na Venv e Congelando as configurações

Agora que já estamos com o nosso ambiente virtual do projeto configurado, vamos instalar o django dentro dele pra utilizar em nosso projeto.

### 4.1 Instalando o Django

Dentro do terminal do pycharm rode o seguinte comando.

```sh
pip install django
```

### 4.2 Congelando as configurações

Vamos congelar as configurações, ou seja vamos criar um arquivo de configuração dos pacotes utilizados e necessários para rodar nosso projeto, para que quando for necessário rodar o projeto em outro ambiente seja fácil instalar os pacotes necessários.
Para criar esse arquivos dentro do terminal do pycharm rode o seguinte comando.

```sh
pip freeze > requirements.txt
```

Após a finalização um arquivo será criado na raiz do projeto chamado `requirements.txt` nesse arquivo irá conter todos os pacotes utilizados no projeto até o momento. No sentido inverso quando necessário instalar estes pacotes em outro ambiente basta rodar o seguinte comando.

```sh
pip install -r requirements.txt
```
