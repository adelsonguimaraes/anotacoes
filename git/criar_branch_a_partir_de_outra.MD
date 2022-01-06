# Criando um novo Branch a partir de outro.
Nosso objetivo aqui é criar uma ``nova branch`` a partir de uma ``branch já existente``, utilizando a linha de comando via terminal.

Recurso importante para criar novas branchs para desenvolver soluções separadamente baseado em uma outra brach mais atualizada.

No nosso cenário aqui estamos trabalhando em um projeto de calculadora, temos uma branch principal chamada ``calculadora`` e vamos fazer uma melhoria de subtração pra isso vamos criar uma branch nova chamada ``adicionar_subtração``.

## Checando uma branch que já existe
Vamos checar nossa branch principal que já existe, chamada ``calculadora``, a qual desejamos copiar os recursos para nossa nova branch.
>git checkout calculadora

Vamos atualizar a branch para pegar todos os recursos mais atulizados
>git pull


## Criando a nova branch
Agora vamos criar a nossa nova branch chamada ``melhoria/adicionando_subtracao`` a partir da nossa branch checada e atualizada.
>git checkout -b melhoria/adicionando_subtracao

O comando a cima vai criar uma nova branch localmente e checa-la assim que for criada, essa branch traz todos recursos que estão na branch ``calculadora`` assim podemos dar continuidade no trabalho já feito.

## Publicando nossa branch remotamente
Agora vamos publicar nossa branch nova na nuvem, para que outras pessoas possam encherga-la e para que possamos enviar commits para a nuvem enquanto trabalhamos.
>git origin push melhoria/adicionando_subtracao

Pronto nossa branch foi publicada na nuvem e agora outras pessoas que participam do projeto podem ver em que estamos trabalhando.

Vamos rodar um comando para verificar as branchs locais e remotas e ter a certeza que nossa branch já está na origem.
>git brancha -a