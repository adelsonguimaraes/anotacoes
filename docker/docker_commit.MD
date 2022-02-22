# Convertendo um container em Imagem
Podemos converter um container docker em imagem, esse é um processo que em alguns momentos pode ser últil, por exemplo quando estamos configurando um container manualmente e queremos salvar uma imagem desse container.

Para criar a imagem a partir de um container utilizamos o comando.
```sh
docker container commit ID_CONTAINER NOME_PRA_IMAGEM
```