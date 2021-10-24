# Copiando diretórios e arquivos via SSH
Aqui vamos ver como copiar aquivos via SSH.
- Copiar arquivos via ssh para local.
- Copiar diretórios via ssh para local.
- Copiar arquivos entre 2 servidores via ssh.

## Copiando arquivos do servidor para local
Para copiar aquivos de um servidor via SSH para nosso local podemos utilizar o seguinte comando
>scp usuario@ipservidor:arquivo.extensao /caminho_local

## Copiando diretórios do servidor para local
Para copiar diretórios de um servidor via SSH para o local podemos utilizar o seguinte comando
>scp usuario@ipservidor:caminho/* /caminho_local

## Copiando arquivos entre servidores
Para copiar arquivos ou diretórios entre 2 servidores podemos utilizar a mesma forma vista anteriormente, o que muda é o destino, veja o código abaixo
>scp usuario@ipservidor:arquivo.exetensao usuario@ipservidor2:caminho_servidor2/

e para diretórios

>scp usuario@ipservidor:caminho/* usuario@ipservidor2:caminho_servidor2/*