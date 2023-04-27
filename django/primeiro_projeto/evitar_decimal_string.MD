# Evitar Decimal como String

Por padrão no django quando fazemos uma consulta na api, o resultado retornado pelo `rest_framework` são valores em string, isso inclui os decimais, porém, para melhorar nossa forma de trabalhar podemos evitar que os decimais sejam convertido para string.

# 1. Configurando para evitar conversão

Para evitarmos a conversão de decimal para string, devemos apenas setar uma configuração no `settings.py`, abaixo como deve ficar.

```py
...

REST_FRAMEWORK = {
    'COERCE_DECIMAL_TO_STRING': False,
}
```

Aqui finalizamos o nosso estudo, nosso próximo estudo é sobre `Managers`, para ir para o próximo estudo clique no link a seguir.

[Próximo passo](./managers.MD) - Trabalhando com Managers.