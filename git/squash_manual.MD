# Squash Manual
Neste estudo vamos ver rapidamento como fazer o squash manual usando as funções de reset, amend e force push

## 1. Quando utilizar
Devemos utilizar o squash quando queremos unir vários commits que temos de mesmo contexto em um único commit, ou seja, os commits devem ser dependentes ou complementares.

## 2. Realizando o reset
Para iniciarmos o nosso processo o primeiro passo é localizar o primeiro commit da nossa lista de commits a sofrerem o squash, isso porque assim iremos retornar todos os arquivos do primeiro ao último commit para o stash.

## 2.1 Identificando o commit
Para que possamos verificar os commits podemos utilizar o seguinte commando git no terminal
```sh
git log
```
Uma lista de commits será exibida parecida com o exemplo abaixo

```sh
commit 497a91c5c69a90377fdd6faec4d5c90bc9d2e64c (HEAD -> main, origin/main, origin/HEAD)
Author: Adelson Guimarães Monteiro <email@email.com>
Date:   Sat Apr 22 16:59:17 2023 -0400

    fix: adicionando formulário e cadastro de filmes

commit d3808b6665cc2308036ca7956057c56d24833c8b
Author: Adelson Guimarães Monteiro <email@email.com>
Date:   Tue Dec 6 23:54:21 2022 -0400

    feat: adicionando o readme
```
Nesse exemplo acima temos dois commits feitos nessa branch, eles são mostrados em ordem decrescente, ou seja do último (mais recente) para o primeiro, então com isso sabemos que o nosso primeiro commit é o último da lista, o que precisamos dele é o código hash que identifica o commit.

## 2.2 Resetando
Agora que já fizemos o trabalho de identificação e temos o hash do commit para o qual queremos resetar, vamos usar o seguinte commando para resetar.

```sh
git reset --soft 497a9
```
## 3. Realizando commit com Amend
Agora que já aplicamos nosso reset temos novamente todos os arquivos que foram modificados ou trabalhados em todos os commits disponíveis como stashed e nas suas últimas versões, ou seja prontos para serem commitados, então vamos usar o commando abaixo para fazer nosso commit com amend para sobre escrever o commit `497a9` que está na nuvem.

```
git commit --amend -m "meu commit com squash manual foi um sucesso!"
```
Como podemos ver acima estamos fazendo um commit porém adicionando a cláusula `--amend` que irá sobre escrever um commit que já existe em nuvem.

## 4. Realizando o force push
Agora que temos o nosso commit montado só precisamos enviar ele para a nuvem, porém como estamos fazendo um `amend`, precisamos usar a cláusula `force` isso porque apenas o push irá gerar um erro de conflito, porque na nossa versão existem mudanças em arquivos que já se encontram na nuvem, assim iremos forçar a sobreposição.

```sh
git push --force
```
Pronto, agora sua árvore esta atualizada com seu squash, deverá existir apenas um único commit que foi o commit feito com amend.