# Salvando uma Imagem docker localmente
Como salvar uma imagem de forma local para uma necessidade de uso off-line.

## Salvando a Imagem
Para salvar a imagem só precisamos rodar o arquivo abaixo, assim teremos a imagem salva no diretório setado.

```sh
docker image save NOME_IMAGEM:VERSAO --output /diretorio/arquivo
```

## Recuperando a Imagem
Para recuperar a imagem basta utilizar o comando abaixo.
```sh
docker image load --input NOME_IMAGEM:VERSAO /diretorio/arquivo
```
