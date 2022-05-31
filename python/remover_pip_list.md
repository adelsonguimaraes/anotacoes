# Removendo todos os pacotes pip instalados
Código útil para remover todos os pacotes instalados no pip install, útil para quando precisamos remover tudo que foi instalado em uma venv e instalar novamente de um arquivo requirements.txt.

```sh
pip freeze | xargs pip uninstall -y
```