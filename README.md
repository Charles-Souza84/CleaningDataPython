# Problemas comuns em dados
 
### Restrições de tipos de dados

É importante realizarmos um tratamento dos dados a fim de excluirmos eventuais erros que possam influenciar na análise de dados de forma indesejada (_garbage in garbage out_). 

Inicialmente devemos checar se os data types das variáveis estão corretos.

**string para inteiros**
```python
# importando arquivo CSV e imprimindo header
sales = pd.read_csv('sales.csv')
sales.head(2)

    SalesOrderID Revenue Quantity
0      43659      23153$    12
1      43660      1457$      2

# retornando data types das colunas
sales.dtypes

SalesOrderID    int64
Revenue         object
Quantity        int64
dtype: object
# os valores contidos na coluna Revenue são do tipo object, que é o tipo utilizado pelo Pandas para armazenar strings

# podemos checar o data type o e número de campos vazios por coluna em um dataframe

# retornando informação do DataFrame
sales.info()

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 31465 entries, 0 to 31464
Data columns (total 3 columns):
SalesOrderID        31465 non-null int64
Revenue             31465 non-null object
Quantity            31465 non-null int64
dtypes: int64(2), object(1)
memory usage: 737.5+ KB
```
Para efetuar operações com os valores da coluna _Revenue_ , como soma, da forma que está  o resultado seria :
```python
# imprimindo a soma da coluna Revenue
sales['Revenue'].sum()

'23153$1457$36865$32474$472$27510$16158$5694$6876$40487$807$6893$9153$6895$4216..
```
Devemos então tratar os dados desta coluna :
```python
# removendo $ da coluna Revenue
sales['Revenue'] = sales['Revenue'].str.strip('$')
sales['Revenue'] = sales['Revenue'].astype('int')

# verificando se a coluna Revenue é agora formada por inteiros 
# assert - retorna um erro se a condição não for True
assert sales['Revenue'].dtype == 'int'
```

**numérico ou categórico?**

* categórico - representa categorias com uma quantidade finita de possíveis categorias 

Uma coluna de dados contendo valores numéricos representando categorias é importada com o tipo _integer_ , o que pode induzir a erros em resumos estatísticos:  
```python
marriage_status ...
... 3 ...
... 1 ...
... 2 ...
df['marriage_status'].describe()

marriage_status
...
mean    1.4
std     0.20
min     0.00
50%     1.8 ...

```
Isso pode ser resolvido da seguinte maneira :
```python
# convertendo to categórico
df["marriage_status"] = df["marriage_status"].astype('category')
df.describe()

marriage_status
count   241
unique  4
top     1
freq    120
```

### Dados fora de intervalo

Podemos nos deparar com alguns erros de dados com valores fora de seu intervalo esperado como, por exemplo, data de assinatura de clientes feitas no futuro ou avaliações com nota 11 quando o valor máximo seria 10.

**como lidar com dados fora do intervalo?**

* deletar os dados - é a maneira mais simples, porém dependendo do tamanho do dataset, podemos perder informações importantes. É necessário um bom entendimento do dataset para realizar esta operação e ela só deve ser feita quando afeta uma pequena parte do dataset.

* substituir por valores mínimos e máximos customizados.

* tratar como dados faltantes.

* substituir por um valor customizado em função das características do negócio.

```python
import pandas as pd

# retorna os filmes com avaliação maior que 5
movies[movies['avg_rating'] > 5]

     movie_name         avg_rating
23 A Beautiful Mind         6
65 La Vita e Bella          6
77     Amelie               6

# Drop values usando filtro
movies = movies[movies['avg_rating'] <= 5]

# Drop values usando .drop()
movies.drop(movies[movies['avg_rating'] > 5].index, inplace = True)

# checando resultado com assert
assert movies['avg_rating'].max() <= 5

# convertendo avg_rating > 5 para 5
movies.loc[movies['avg_rating'] > 5, 'avg_rating'] = 5

# checando resultado com assert
assert movies['avg_rating'].max() <= 5
```

Ao trabalharmos com datas, precisamos checar o tipo se o data type da coluna é do tipo data. Caso seja um objeto, devemos converter para data para fazer as comparações necessárias e isso pode ser feito da seguinte maneira :

```python
# convertendo para data
# coluna do dataframe com data a ser convertida = função pd.to_datetime(coluna do dataframe) -> converte para um objeto pandas
# datetime
# para converter o objeto datetime para data, utilizamos .dt.date
# poderíamos converter o objeto diretamente para data, mas precisaríamos passar uma string com o formato da data e desta forma
# é mais fácil fazer a conversão
user_signups['subscription_date'] = pd.to_datetime(user_signups['subscription_date']).dt.date

today_date = dt.date.today()

# Drop values usando filtro
user_signups = user_signups[user_signups['subscription_date'] < today_date]
# Drop values usando .drop()
user_signups.drop(user_signups[user_signups['subscription_date'] > today_date].index, inplace = True)

# substituindo datas futuras por data atual
user_signups.loc[user_signups['subscription_date'] > today_date, 'subscription_date'] = today_date
# checando o resultado - importante utilizar o método .date() para que uma data seja retornada e não um timestamp
assert user_signups.subscription_date.max().date() <= today_date
```

### Valores duplicados

Identificamos dados duplicados quando temos informações repatidas ao longo de múltiplas linhas ou entre diferentes colunas no dataframe. 

**como encontrar valores duplicados**

Encontramos valores duplicados com o método <code>.duplicated()</code> passando os seguintes argumentos :
* subset - lista com os nomes das colunas a serem checadas;
* keep - se devemos manter o primeiro ('first') , o último ('last') ou todos ('False') valores duplicados;
```python
# Column names to check for duplication
column_names = ['first_name','last_name','address']
duplicates = height_weight.duplicated(subset = column_names, keep = False)
```

**como tratar dados duplicados**

O método <code>.drop_duplicates()</code> retorna uma série booleana referente às linhas duplicadas e tem os seguintes argumentos:
* subset - lista com os nomes das colunas a serem checadas;
* keep - se devemos manter o primeiro ('first') , o último ('last') ou todos ('False') valores duplicados;
* inplace - deletar as linhas duplicadas diretamente do dataframe sem criar um novo objeto (True);

```python
# elimina linhas que sejam exatamente iguais ( todos os valores em suas colunas )
height_weight.drop_duplicates(inplace = True)
```
Em alguns casos o dataframe contem linhas com valores de colunas repetidos, mas alguns com diferenças em campos numéricos. Nestas situações podemos combinas estas linhas por meio de medidas estatísticas como média, por exemplo.

```python
# agrupa pelos nomes das colunas e produz resumos estatísticos
column_names = ['first_name','last_name','address']
summaries = {'height': 'max', 'weight': 'mean'}
# .reste_index() serve para que tenhamos índice numerado na saída final
height_weight = height_weight.groupby(by = column_names).agg(summaries).reset_index()

# checa se a combinação foi feita
duplicates = height_weight.duplicated(subset = column_names, keep = False)
height_weight[duplicates].sort_values(by = 'first_name')
```

___

# Problemas em dados categóricos e tipo texto

### 

###

###

___

# Problemas avançados em dados

###

###

###
___

# Ligação de registro

###

###

###

