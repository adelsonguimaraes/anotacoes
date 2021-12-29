# Criando e Restaurando um Backup com MySQL e MySQL Dump.
O ``MySQL Dump`` é uma ferramenta do própio ``MySQL`` que ja é instalada juntamente com o MySQL.

## MySQL Dump

Com essa ferramenta podemos criar forma fácil e rápida ``dumps`` (backups) de bancos de dados dentro de um servidor MySQL, e podemos fazer isso para mais de um banco de uma única vez gerando um único arquivo, o que serve para cenários onde uma aplicação utiliza vários bancos de dados.

## Abrangência

A ferramenta abrange não só cenários ``locais`` como também cenários na ``nuvem``, então com isso, podemos de forma rápida e fácil criar dumps no nosso computador local de servidores que estão na nuvem, o que facilita bastante quando precisamos gerenciar cópias do banco sem acessar a máquina servidor diretamente.

## Restauração

Assim como o MySQL Dump consegue criar dumps do banco de dados, o próprio Mysql serve para fazer o papel de ferramenta para restaurar o banco de dados, seja no ambiente local ou na nuvem.

## Utilização

Vamos ver agora como funciona a utilização dessa ferramenta para criação de dumps e como utilizar o MySQL para fazer a restauração.

- Único banco de dados

    Nesse exemplo vamos fazer um dump de um único banco de dados local ou nuvem.

    ```sh
    mysqldump -h ENDERECO_SERVIDOR -u NOME_USUARIO -p SENHA_USUARIO NOME_BANCO > NOME_DO_ARQUIVO_DE_SAÍDA.sql
    ```
- Múltiplos bancos de dados

    Nesse exemplo vamos fazer um dump de múltiplos bancos de dados local ou nuvem, salvando em um único árquivo.

    ```sh
    mysqldump -h ENDERECO_SERVIDOR -u NOME_USUARIO -p SENHA_USUARIO
    --databases PRIMEIRO_BANCO SEGUNDO_BANCO > NOME_DO_ARQUIVO_DE_SAIDA.sql
    ```

- Restauração de Dump

Agora vamos utilizar o MySQL para fazer a restauração de um dump do banco, nesse processo o recomendado é apagar o banco de dados existente antes de rodar a recuperação.

Para restaurar utilizamos o comando
```sh
mysql -h ENDERECO_SERVIDOR -u NOME_USUARIO -p SENHA_USUARIO < CAMINHO_COMPLETO_DO_DUMP.sql
```