# Urls Django

Agora vamos estudar sobre `urls` que basicamente são as definições de rota do django, cada aplicativo tem suas próprias rotas em seu arquivo de urls.

## 1. Criando as urls

Inicialmente vamos criar dentro do nosso aplicativo um novo arquivo chamado `urls.py`, dentro do arquivo vamos adicionar o seguinte código.

```py
from rest_framework.routers import DefaultRouter
from basic import viewsets


router = DefaultRouter()
router.register('course', viewsets.CourseViewSet)

urlpatterns = router.urls

```

Começamos importando do rest framework do arquivo routers a classe `DefaultRouter` e também importamos os `viewsets` do nosso app, em seguida criamos uma variável chamada `router` que receber uma instancia da classe `DefaultRouter`, em seguida registramos uma rota apontando para o nosso `viewset`, então damos um nome pra nossa rota e informamos o viewset referente, por fim chamamos a constante urlspatterns e atualizamos com as `urls` das rotas.

## 2. Adicionado as rotas no aplicativo principal

Agora precisamos adicionar nossas rotas no aplicativo principal, para isso vamos abrir o `sale.urls` e adicionar o seguinte código.

```py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('school/', include('basic.urls')),
]
```

Agora para testar se nossa rota foi configurada corretamente vamos acessar o nosso endpoint, no endereço `http://localhost:8000/school/course/`.

Aqui terminamos o nosso estudo sobre as `url`, no nosso próximo estudo veremos como aplicar o conceito de herança no Django, para seguir para o próximo estudo clique no link a seguir.

[Próximo passo](./model_base.MD) - Trabalhando com Model Base (Herança).