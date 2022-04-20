# GetX: Como gerar modelo a partir de JSON
Aqui vamos ver como podemos gerar uma class model a partir de um modelo com o pacote GetX, e o GET_CLI.

# Instalando o pacote GetX
Para iniciarmos nosso desenvolvimento precisamos instalar nosso pacote GetX dentro do nosso projeto, para isso basta usar no terminal dentro da raiz do nosso projeto o seguinte comando (estamos levando em consideração que você já possui uma aplicação inicial e está apenas aprendendo como usar o GetX.);

```sh
flutter pub install get
```

# Instalando o CLI do Get
Para automatizar nosso processo precisamos instalar o CLI do GetX, para isso estou deixando o link aqui de um video que ensina como configurar o CLI e também o link do git onde possui essa documentação.

video: https://www.youtube.com/watch?v=kvhnet3ekGA
Doc: https://github.com/jonataslaw/get_cli

# Gerando o modelo
Para podermos gerar nosso modelo agora que temos todo o necessário configurado, precisamos do arquivo que contem o JSON que queremos usar para criar nosso modelo, aqui abaixo temos um modelo bem báiso de json de usuário, apenas para material de estudo.

```json
[
    {
        "id": 1,
        "name": "Alex",
        "email": "alex@email.com",
        "password": "12345",
        "active": true,
        "date": "2022-03-01 06:00:00"
    }
]
```

Certo vamos chamar esse nosso json de ``user.json`` e vamos coloca-lo dentro de ``assets/json``.

Em seguida o que pracisamos fazer e rodar nosso comando que irá usar nosso arquivo json como refência para criar nosso modelo,

```sh
get create model on user with assets/json/user.json
```

também podemos fazer isso a partir de uma url que contenha como resposta um json, ficaria assim.

```sh
get create model on user from "https://https://meusite.com/user.json
```

Perfeito, com isso o get cli irá gerar dentro da pasta category um arquivo de modelo para nós todo pronto pro uso, baseado no json que fornecemos como fonte.