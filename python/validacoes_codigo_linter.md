# Validações de código Linter
Olá, nessa sessão veremos como podemos validar, melhorar e padronizar noso código Python com ferramentas de validação de linter.

## O que é linter ?
Nesse momento você deve estar se perguntando "mas o que é linter?", bom o linter ou lint como também é conhecido, é uma ferramenta ou várias ferramentas que seguem padrões de projeto que são orientados nas documentas oficiais das linguagens.

A função dessas ferramentas é nos ajudar a manter uma boa qualidade de código no nosso desenvolvimento, padronizando os códigos par que possamos ter escritas mais claras e limpas facilitando o trabalho em times e manutenções no futuro já que o código passa a ficar sempre muito parecido devido estar padronizado.

## Conhecendo o Isort
![logo_isort](https://raw.githubusercontent.com/pycqa/isort/main/art/logo_large.png)

Vamos falar da nossa primeira ferramenta aqui, ela é a nossa ferramenta mais simples, porém, muito eficaz, a função do Isort é validar e organizar nossa sessão de imports no topo dos arquivos.

Antes de seguir com a explicação vamos instalar nossa ferramenta dentro do nosso projeto python
```sh
pip install isort
```

Podemos testar se está instaldo rodando
```
isort
```

A saída deve ser algo como:
```sh
                 _                 _
                (_) ___  ___  _ __| |_
                | |/ _/ / _ \/ '__  _/
                | |\__ \/\_\/| |  | |_
                |_|\___/\___/\_/   \_/

      isort your imports, so you don't have to.

                    VERSION 5.10.1
```

Assim como as demais ferramentas o Isort tem um padrão que deve ser obdecido, esse padrão determina uma hierarquia nas chamadas de import, então ele considera a seguinte ordem:

1. Imports nativos e de sistemas
2. Imports de pacotes ou ferramentas de terceiros
3. Imports da nossa aplicação

Além disso ele também cuida para que os imports sejam mais inteligentes, isso quer dizer que, imports de várias ferramentas de um mesmo pacote devem ser feitas em uma única chamada do pacote, e não chamar um import pra cada ferramenta, segue um exemplo abaixo

Sem o padrão Isort
```py
import frutas from manga
import frutas from abacaxi
import frutas from caju
```

Com o padrão de import inteligente do Isort
```py
import frutas from manga, abacaxi, caju
```

Esses sãos benefícios da nossa primeira ferramenta, e ela aceita vários parâmetros que podem ajudar na sua execução.

Desses parâmetros que vamos utilizar aqui são o `--check`, o `--diff` e o `--color`, todos os demais podem ser consultados rodanddo "isort -h" no terminal.

O --check é utilizado quando queremos apenas validar os códigos, com isso ele não fará nenhuma mudança no código de forma automática, apenas sinalizará que existem problemas ou não.

O --diff é utilizado para mostrar cada diferença no código de como está feito e de como deve ficar segundo os padrões do Isort.

O --color deve ser utilizado junto com o --diff, com essa combinação ele mostrará em vermelho o erro e em verde a correção, o que ajuda bastante na hora de identificar o errado e o certo.

Agora que entendemos teoricamente como tudo isso irá funcionar, vamos rodar o Isort e ver as coisas acontecendo na prática.
```sh
isort --check --diff --color .
```
Se ele econtrar erros na ordem dos imports ele irá mostrar na saída do terminal, e para fazer com que ele corrija tudo que ele encontrou fora de padrão, basta rodar o comando sem os parâmetros, com isso ele deve corrigir tudo e se rodar novamente o check ele não irá mais encontrat problemas.
```sh
isort .
```

## Conhecendo o Black
![logo_black](https://warehouse-camo.ingress.cmh1.psfhosted.org/d3a1a77162e3cd8c3d2089f27899b6eee71af013/68747470733a2f2f7261772e67697468756275736572636f6e74656e742e636f6d2f7073662f626c61636b2f6d61696e2f646f63732f5f7374617469632f6c6f676f322d726561646d652e706e67)

Agora vamos conhecer nossa segunda ferramenta que irá fazer parte do nosso processo de validação de lint.

O Black é uma ferramenta bem conhecida que envolve várias validações focadas na indentação e formatação do código, vejamos abaixo algumas de suas aplicações:
1. Vírgula mágica, ao se colocar uma virgula no final do último item de um array ou parâmetro por exemplo, ele mágicamente colocará cada item em uma linha, sem a virgula o comportamente é comum.
2. Comentários, dois espaços entre comentário e código e espaço no começo do comentário.
3. Aspas, o padrão implicito para aspas em strings é o uso de "aspas dupla".

Existem muitos outros padrões no estilo de código do Black é importante entender todos eles para saber o que o Black fará com seus códigos, para ver a lista completa pode acessar usado o link abaixo
>https://black.readthedocs.io/en/stable/the_black_code_style/current_style.html

Agora antes de seguirmos falando da nossa ferramenta vamos fazer a sua instalação:
```sh
pip install black
```
Para verificar se o black está corretamente instalado rode:
```sh
black --version
```
A saída deve ser algo como:
```sh
black, 22.3.0 (compiled: yes)
```

Assim como vimos na primeira ferramenta o Black também aceita vários parâmetros, que podem ser visualizados rodando o comando:
```sh
black -h
```

Isso irá mostrar todos os parâmetros que podem ser utilziados, aqui pro nosso caso vamos utlizar `--chek`, `--diff` e `--color` e a explicação pra cada uma dessas ferramentas é exatamente a mesma do Isort, caso tenha dúvidas pode olha acima onde explicamos para que serve cada um desse.

Agora vamos rodar nosso comando no terminal e ver toda a saída que a ferramenta vai trazer.
```sh
black --check --diff --color .
```

Show, possívelmente devem ter retornado vários prblemas econtrados por ele e para corrigir isso é muito simples, precisamos apenas rodar o comando:
```sh
black .
```

Com isso o Black irá modificar tudo automáticamente e deixar da maneira que o padrão dele deixa implicito, após isso podemos rodar novamente o comando anterior de `--check` e veremos que não encontrá mais nenhum problema.

## Conhecendo MyPy
![logo_mypy](https://miro.medium.com/max/2696/1*2icBzVd8jj0ay6MNzuHPug.png)

Agora vamos conhecer nossa terceira ferramenta o MyPy, essa ferramenta tem uma especificidade que é fazer a validação de `Type Hint`, ou seja, nossa ferramenta é utilizada para testar variáveis e funções tipadas, verificando se as variáveis estão recebendo realmente o valor esperado e se as funções estão tendo as saídas corretas.

Em grande parte por ser uma linguagem dinâmica, a maioria das pessoas não costuma criar a amarração de tipar as variáveis, o que é natural, porém, é entendido atualmente que mesmo em linguagens dinâmicas o type hint traz benefícios como por exemplo a segurança de tipos, obrigando que o tipo esperado seja atendido, assim também como ajuda a IDE ser mais assertiva em suas sugestões já que ela passa a entender quais funções servem para aquele tipo de variável que está sendo inteirada.

Porém, mesmo que o type hint seja aplicado corretamente no seu código, apenas isso não será o suficiente, pois o python ou a IDE nativamente não irá obrigar você a utilizar a tipagem, é ai que o MyPy entra em ação, ele fará o papel de validar e refutar seus códigos.

Antes de prosseguirmos vamos instalar o MyPy:
```sh
pip install mypy
```

Para verificar se foi instalado corretamente rode:
```sh
mypy --version
```

A saída deve ser algo como:
```sh
mypy 0.960 (compiled: yes)
```

Um ponto de atenção aqui é que talvez com algumas bibliotecas ele reclame da falta de stubs, isso aconteceu com o pacote de requests por exemplo, e para resovler esse problema geralmente ele informa no terminal que deve ser rodado um comando, mas pra evitarmos esse problema desde agora vamos instalar esses stubs agora:
```sh
python3 -m pip install types-requests
```

Seguindo a mesma linha das ferramentas anteriores o MyPy recebe parâmetros e todos eles podem ser visualizados executando o comando "mypy -h", porém nesse vamos criar um arquivo de configuração onde poderemos definir tudo que a ferramenta vai poder fazer.

Na Raiz do projeto crie um arquivo chamado ``mypy.ini``, e dentro dele vamos colocar apenas duas configurações, caso tenha interesse em saber mais, no site oficial pode ser visto isso nas documentações.

```ini
[mypy]
python_version = 3.10
ignore_missing_imports = true
```
Basicamente na 1a linha estamos declarando a sessão global, na 2a linha estamos setando a versão do python que está sendo utilziado e por fim na 3a linha estamos ignorando alguns imports de ferramentas como frameworks que não devem ser verificados.

Agora vamos rodar pela primera vez nossa ferramenta:
```sh
mypy .
```

Segue aqui exemplo de saída da execução do MyPy quando ele encontrar erros de tipagem no código:
```sh
testes.py:7: error: Incompatible return value type (got "str", expected "int")
actions.py:8: error: Module has no attribute "parse"

Found 2 errors in 2 files (checked 19 source files)
```