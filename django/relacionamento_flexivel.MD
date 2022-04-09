# Relacionamento flexível em tableas associativas com FlexFields
Aqui vamos ver como simplificar o gerenciamento da serialização de tableas associativas dentro do Django, para isso vamos utilizar um pacote chamado FlexFIelds, ele vai ser o responsável por fazer toda a mágica pra gente.

## Instalando o pacote
Primeiramente precisamos instalar o pacote FlexFields dentro do nosso projeto, para isso vamos utilizar o código abaixo no terminal
```sh
pip install drf-flex-fields
```

## Importando no arquivo serializer.py
Agora para utilizar o pacote precisamos importa-lo detro do nosso seralizer, para isso no topo do nosso arquivos vamos adiconar a seguinte linha
```python
from rest_flex_fields import FlexFieldsModelSerializer
```

## Injetando a herança na classe serializer
Agora para utilizar na classe serializer precisamos injetar o pacote, abaixo segue um modelo de uma classe chamada Materia.
```python
class MateriaSerializer(FlexFieldsModelSerializer):
    class Meta:
        model = models.Materia
        fields = '__all__'
```

## Inserindo os campos expansíveis
Agora precisamos dentro dessa classe adicionar um dicionário chamado ``expandable_fields`` que vai receber as configurações para trazer os dados da tabela associada, vejamos a baixo o código exemplo.
```python
class MateriaModelSerializer(FlexFieldsModelSerializer):
    class Meta:
        model = models.Materia
        fields = '__all__'

    # dict
    expandable_fields = {
        'alunos': ( # o nome da lista
            'app.AlunoModelSerializer', # o serelizador da tabela associada
            {
                'source': 'aluno_set', # o campo set 
                'many' : True # relacionamento muitos pra muitos
            }
        )
    }
```

## Acessando a listagem com FlexFields
Legal, agora nosso serializer está todo configurado e pronto para listar nossas ``Matérias`` e os ``Alunos`` que estão associados a elas.
O interessante do FlexFields é que ele permite você controlar se que listar os dados associados ou não, para isso precisamos adicionar na requisição da url um parametro chamado ``expand`` com o valor ``alunos`` que é nome da lista que configuramos ali em cima no serialize, vamos ver abaixo um exemplo de como fica a url.
```sh
http://localhost:8000/app/materia/?expand=alunos
```
O resultado dessa consulta deve trazer algo parecido com isso

```json
[
    {
        "id": 1,
        "nome": "Matemática",
        "alunos": [
            {
                "id": 40,
                "nome": "Pedro"
            },
            {
                "id": 89,
                "nome": "João"
            }
        ]
    }
]
```
Quando o parametro ``expand=alunos`` é removido a sessão de alunos não é retorna no resultado, apenas a lista de matérias.

# Fazendo o caminho inverso
Já conseguimos expandir todos os alunos de uma matéria, agora vamos fazer o inverso, vamos expandir a matéria de um aluno, muito simples, precisamos injetar nossa herança e adiconar nosso dict assim como fizemos em materia.

```python
class AlunoModelSerializer(FlexFieldsModelSerializer):
    class Meta:
        model = models.Aluno
        fields = '__all__'

    # dict
    expandable_fields = {
        'materia_group': (  # nome do grupo associativa
            'app.MateriaModelSerializer',
        )
    }
```

Assim como em materia precisamos adicionar na nossa url de consulta o parametro ``expand`` com o nome que demos para nossa expanção.

```sh
http://localhost:8000/app/aluno/?expand=materia_group
```
E nossa resposta deve ser algo parecido com isso.
```json
[
    {
        "id": 40,
        "nome": "Pedro",
        "materia_group": {
            "id": 1,
            "nome": "Matemática"
        }
    }
]
```
Esse é um modelo simples onde cada aluno pode estar em apenas uma matéria.

# Melhorando a performance
Quando trabalhamos no Django com consultas em tabelas associativas, na maioria das vezes ele não resolve a consulta da melhor forma, isso quer dizer que quando temos uma consulta de um pra muitos ou muitos pra muitos, ele acaba fazendo uma nova consulta para cada registro da lista, o que deixa a perfomance mais lenta.

Para resolver isso e fazer com que ele consulta a menor quantidade de vezes possível precisamos adicionar na consulta da viewset uma informção para que ele faça o ``join`` do relacionamento e assim não precise consultar separadamente cada item.

Vamos ver abaixo como isso ficaria.

```python
class MatriaViewSet(viewsets.ModelViewSet):
    queryset = models.Materia.objects.all()
    serializer_class = serializers.MateriaSerializer

    def list(self, request, *args, **kwargs):
        self.queryset = self.queryset.prefetch_related('aluno_set')
        return super(MateriaViewSet, self).list(request, *args, **kwargs)
```

Aqui nessa caso sobre escrevemos o metodo list, mas também poderia ser feito direto na queryset principal.
Adicionamos ali um campo após o queryset chamado ``prefetch_related`` e passamos o nome do set associado, essa campo é utilizado quando trabalhamos na consulta inversa, ou seja, quando queremos começar a consulta pela Matéria e listar todos os alunos relacionados.
Se a consulta fosse iniciar do Aluno para saber todas as matérias que ele está associado, adicionariamos um campo na viewset de aluno, vamos ver.

```python
class AlunoViewSet(viewsets.ModelViewSet):
    queryset = models.Aluno.objects.select_related('materia').all()
    serliazer_class = serializers.AlunoSerializer
```

Aqui nesse caso usamos o campos ``select_related`` logo após a queryset, e passamos o nome do campo relacional de materia dentro da model do aluno que é materia, com isso o relacionamento com materia é feito com ``join`` e o Django tenta realizar a menor consulta possível.