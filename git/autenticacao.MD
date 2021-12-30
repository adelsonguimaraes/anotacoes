# Configurando e salvando credenciais git
Aqui vamos ver como configurar acesso e salvar o acesso do git.

## Setando para o git config manter credenciais salvas
Inicialmente vamos setar pro arquivo de configuração do git dados da nossa credêncial, essas informações são relativas ao sevidor que vocÊ utilzia, são os dados que você utiliza para cessar.
>git config --global credential.helper store

## Acessando com credenciais
Agora configuramos nosso git para salvar as informações de acesso do nosso usuário, agora vamos rodar um comando para o servidor, assim ele vai solicitar as credênciais e uma vez informado ele vai salvar para próximas utilizações.

>git fetch

Informe o nome de usuário e senha como solicitado e pronto, a partir de agora o acesso está salvo.