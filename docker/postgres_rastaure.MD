# Restaurando banco de dados Postegres

Olá, aqui vamos estudar como restaurar um banco de dados a partir de arquivos no postgres rodando em um container docker.

## 1. Descobrindo o ID do Container

Para podermos prosseguir precisamos incialmente do ID do nosso caintainer que está rodando o serviço postgres, podemos conseguir isso rodando o comando.

```sh
docker ps
```
Uma lista de containers ativos será exibida, agora basta copiar a informação ID do container que possui o postgres.

## 2. Copiando o arquivo para o container

Nosso segundo passo aqui é transferir o arquivo de restauração para dentro do nosso container, para que em seguida possamos executá-lo.

```sh
docker cp backup.dump ID_CONTAINER:tmp/
```
No exemplo acima, estamos copiando da pasta atual que estamos acessando via terminal para a pasta `tmp` do nosso container, o arquivo de restauração que chamamos aqui de `backup.dump`.

## 3. Restaurando com pg_restore

Agora que já temos nosso arquivo dentro do container, precisamos rodar um comando que irá usar uma ferramenta do Postgres chamada `pg_restore`, essa ferramente só executa arquivos do tipo `.dump`, então vejamos o comando abaixo.

```sh
docker exec ID_CONTAINER pg_restore -U postgres -d NOME_BANCO /tmp/backup.dump
```

Pronto se tudo estiver correto a ferramenta irá fazer a restauração do banco sem problemas, assim que terminar de executar no terminal.

## 4. Restaurando com PSQL

Uma outra forma de restaura o banco de dados é utilizando outra ferramenta do Postgres, chamada `psql`, no caso dessa ferramenta ela espera um arquivo do tipo `.sql`, então o processo de envio é o mesmo, e o comando de execução é o seguinte.

```sh
docker exec ID_CONTAINER psql -U postgres -d NOME_BANCO -f /tmp/backup.sql
```

Se tudo estiver correto, no final da execução o banco deve ser corretamente restaurado.