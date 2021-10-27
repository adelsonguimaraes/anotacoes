# Criando o Projeto de um Aplicação Flutter
Aqui vamos ver pode onde começamos para criar uma aplicação Flutter, vamos entender que neste ponto você já possua o ambiente de desenvolvimento configurado para programar em Flutter.

Caso ainda não pussua o ambiente Flutter, pode aprender a configurar com o seguinte link;
>https://flutter.dev/docs/get-started/install

## Criando o projeto
Para criar nosso projeto precisamos utilizar o terminal, existem outras formas de criar um projeto Flutter, utilizando recursos de uma IDE como o ``Android Studio``, mas nesse tutorial vamos fazer de uma forma simples via terminal, pra isso navegue via terminal até uma pasta onde deseja criar a pasta do seu projeto e utilize o seguinte comando.

```sh
flutter create [NOME_DO_APLICATIVO]
```

Quando o processo temrinar uma pasta com o nome que você escolheu para o seu projeto será criada no local onde você executou o comando com o terminal.

## Consultando os Emuladores Disponíveis
Vamos verificar os emuladores disponíveis no nosso computador para testar nossa aplicação, podemos utilizar os emuladores ``AVD`` do próprio ``Android Studio``, ou um celular ligado na ubs com o modo de ``Depuração USB`` ativo, como o comando a seguir podemos ver se alguma das opções estão disponíveis para rodar nosso projeto

```sh
flutter emulators
```

## Rodando Nosso Aplicativo
Agora que já verificamos e temos certeza que possuímos um emulador disponível para testar nossa aplicação, só precisamos entrar com o comando para iniciar a aplicação, então fazemos

```sh
flutter run
```

Se tudo está corretamente configurado, sua aplicação será processada e iniciará nos seu emulador ou celular.