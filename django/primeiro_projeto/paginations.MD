# Paginação Django

No nosso estudo agora vamos ver como fazer paginação no Django, a paginação é comumente usada nas aplicações, geralmente como limitador de dados para deixar as páginas com listagem de dados mais performática e leve, com isso o Django também possui uma forma de facilitar esse trabalho.

## 1. Configurando a paginação

Para configurarmos a paginação no Django é bem simples, precisamos apenas adicionar um trecho de configuração no nosso arquivo `settings`, vejamos abaixo.

```py
...

REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
}
```
O código acima foi adicionado ao final do nosso arquivo `settings.py`, com apenas essa configuração todos os nossos modelos passam a ter paginação, para testarmos basta acessar uma url adicionando os parâmetros `limit` que serve para dizermos a quantidade de itens que queremos por página, e `offset` que serve para dizer a partir de qual item a listagem deve começar, lembrando que a listagem começa contando de 0, vejamos o exemlo abaixo.

> http://localhost:8000/api/basic/student/?limit=10&offset=0

No exemplo a cima estamos fazendo uma listagem de estudantes iniciando do registro 0 e limitando em 10 itens na lista.