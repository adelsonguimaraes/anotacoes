# Conectando ao postgres com Django

Nosso próximo passo é conectar nosso projeto com o banco de dados `Postgres`, é necessário ter instalado o postegres em sua máquina para proseguir, não abordaremos como fazer esses passos aqui, mas o contéudo é muito fácil de se encontrar na internet.

## 1. Instalando lib de conexão

Para que seja possível fazer a conexão com o Postgres precisamos instalar uma lib que irá fazer a ponte entre Django e nosso banco de dados, para instalar utilize o comando abaixo dentro do terminal do pycharm.

```sh
pip install psycopg2-binary
```

## 2. Stando o database no settings

Agora vamos até o nosso arquivo `sale/settings` e na sessão de conexão com o banco de dados vamos fazer a seguinte configuração.

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'sale3',
        'HOST': 'localhost',
        'PORT': '5432',
        'USER': 'postgres',
        'PASSWORD': '123456'
    }
}
```
Importante que o banco de dados precisa ser criado no postgres, esse processo também pode ser feito pela aba `Data Base` do pycharm como fizemos a alguns passos atrás pra conectar no `SQLite` porém agora deve-se selecionar o servidor `PostgreSQL`.

## 3. Rodando as migrations

Se tudo aqui foi configurado corretamente, podemos executar nossas migrations para o novo banco de dados, para isso basta roda o comando.

```sh
python manage.py migrate
```
Pronto agora nosso projeto está configurado com o banco de dados `Postgres`. Aqui termina nosso estudo de como conectar o Django com o banco Postgres.