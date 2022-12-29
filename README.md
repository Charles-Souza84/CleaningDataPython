# Problemas comuns em dados
 
### Restrições de tipos de dados

É importante realizarmos um tratamento dos dados a fim de excluirmos eventuais erros que possam influenciar na análise de dados de forma indesejada (_garbage in garbage out_). 

Inicialmente devemos checar se os data types das variáveis estão corretos.

**string para inteiros**
```python
# Import CSV file and output header
sales = pd.read_csv('sales.csv')
sales.head(2)

    SalesOrderID Revenue Quantity
0      43659      23153$    12
1      43660      1457$      2

# Get data types of columns
sales.dtypes

SalesOrderID    int64
Revenue         object
Quantity        int64
dtype: object
# os valores contidos na coluna Revenue são do tipo object, que é o tipo utilizado pelo Pandas para armazenar strings

# podemos checar o data type o e número de campos vazios por coluna em um dataframe

# Get DataFrame information
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
# Print sum of all Revenue column
sales['Revenue'].sum()

'23153$1457$36865$32474$472$27510$16158$5694$6876$40487$807$6893$9153$6895$4216..
```
Devemos então tratar os dados desta coluna :
```python
# Remove $ from Revenue column
sales['Revenue'] = sales['Revenue'].str.strip('$')
sales['Revenue'] = sales['Revenue'].astype('int')

# Verify that Revenue is now an integer 
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
# Convert to categorical
df["marriage_status"] = df["marriage_status"].astype('category')
df.describe()

marriage_status
count   241
unique  4
top     1
freq    120
```

### Restrições de intervalo de dados

### Restrições de singularidade
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

