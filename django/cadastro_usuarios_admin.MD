# Utilizando o cadastro de usuário do Django Admin
Como sabemos o Django Admin já trás algumas apps prontas para o uso, e uma delas é a de Usuários, hoje vamos ver aqui como podemos utilizá-la para cadastrar nossos usuários por ela, sem precisar criar do zero um cadastro de usuários, essa é uma das facilidades do Django.

Então resumindo, vamos configurar nosso projeto para utilizar o modulo de autenticação de usuários também aplicaremos o JWT, assim controlar os acessos a aplicação verificando se o usuário está logado e se tem permissão.

Só uma observação que aqui vamos fazer a configuração para o uso via API, para usar na web em parte o caminho é parecido, a diferença é que não vamos configurar nenhuma View aqui.

# Instalando o necessário
Para ganhar tempo vamos partir do princípio que você já possui um projeto básico iniciado para que possamos trabalhar nele apenas o que é importante aqui nesse estudo.
A partir desse ponto vamos começar a instalar tudo que é necessário nosso objetivo.

Django
>pip install django

Django FrameWork
> pip install djangoframework

Django FrameWork JWT
> pip install djangoframework-simplejwt

Pronto, tudo que precisavamos está instalado, agora vamos para as configurações.

# Configurando o settings.py
Vamos editar nosso arquivo settings.py, dentro dele na parte de ``INSTALLED_APPS`` vamos adicionar nossos pacotes que instalamos para que o Django saiba que quermos usar esses modulos.

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'rest_framework.authtoken',
]
```

Agora vamos configurar nossas variáveis do ``REST_FRAMEWORK``, se ainda não possuir dentro do seu arquivo a sessão abaixo adicione.

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```

Perfeito, nossas configurações no arquivo de settings estão ok, já deixamos o Django saber o que precisamos usar no nosso projeto.

# Configurando o serializador de usuário
Agora precisamos adicionar um serializador para nosso modelo de usuário, não temos o modelo porque vamos usar o que vem do próprio admin, mas vamos ter nosso serializador para poder personalizar a saída dos dados se necessário.

Dentro do projeto se não existir o arquivo ``serializers.py`` crie e adicione o código abaixo.

```py
from rest_framework import serializers
from django.contrib.auth import get_user_model


class UserSerializer(serializers.ModelSerializer):

    class Meta:
        model = get_user_model()
        fields = '__all__'
```

Prontinho nosso serializador de usuário está configurado.

# Configurando o viewset de usuário
Nosso próximo passo é configurar nosso viewset de usuário que é nossa saída visual de dados para a API, se ainda não existir dentro do seu aplicativo o arquivo ``viewsets.py`` crie e adicione o código abaixo.

```py
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from django.contrib.auth import get_user_model
from memcardapi import serializers

class UserViewSet(viewsets.ModelViewSet):

    permission_classes = (IsAuthenticated,)
    serializer_class = serializers.UserSerializer
    queryset = get_user_model().objects.all()
```

Ótimo, temos nosso viewset também configurado, agora vamos para nossa última etapa que é a configuração da rota.

# Configurando a rota para usuários
Agora que temos a maioria das coisas configuradas precisamos apenas configurar nossa rota para que nossa saída de dados seja acessável.
Se dentro do aplicativo não existir o arquivo ``urls.py`` crie e adicione o código abaixo.

```py
from rest_framework.routers import DefaultRouter
from django.urls import path
from memcardapi import viewsets

router = DefaultRouter()
router.register('user', viewsets.UserViewSet, basename='user')

urlpatterns = router.urls
```

Certo agora já temos a nossa rota de usuários configurado, mas ainda precisamos ir até o ``urls.py`` do aplicativo principal e adicionar a inclusão da nossa rota nele, para isso vamos abrir ele e adicionar o código abaixo.

```py
from django.contrib import admin
from django.urls import path, include
from django.conf.urls.static import static
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView
)

from config.settings import MEDIA_URL, MEDIA_ROOT

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api-auth/', include('rest_framework.urls')),
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),

    path('', include('memcardapi.urls'), name='user'),
]
```

