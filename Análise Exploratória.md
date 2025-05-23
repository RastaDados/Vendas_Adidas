## Exploração Inicial dos Dados

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import LabelEncoder
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score
import plotly.express as px
import plotly.graph_objects as go
from dash import Dash, dcc, html, Input, Output
import warnings
warnings.filterwarnings('ignore')

#Carregando os dados
df = pd.read_csv('dados/Adidas_Sales.csv')

#Visualização inicial
print("Primeiras linhas do dataset:")
print(df.head())
print("\nInformações do dataset:")
print(df.info())
print("\nEstatísticas descritivas:")
print(df.describe())
print("\nValores nulos por coluna:")
print(df.isnull().sum())
print("\nValores duplicados:", df.duplicated().sum())

#Verificando se há colunas repetidas
print("\nColunas com nomes repetidos:")
print([col for col in df.columns if df.columns.tolist().count(col) > 1])
```

![01](https://github.com/user-attachments/assets/aef95e90-b440-4597-9454-7792236e0c12)

<hr>
<br>

## Análise Exploratória

#### Distribuição de Preço

```python
plt.figure(figsize=(12, 6))
sns.histplot(df_clean['price'], bins=30, kde=True)
plt.title('Distribuição de Preços dos Produtos')
plt.xlabel('Preço (€)')
plt.ylabel('Contagem')
plt.show()

print("\nEstatísticas de preço:")
print(df_clean['price'].describe())
```

![01](https://github.com/user-attachments/assets/68376eee-f4ae-472a-b80a-c1614cbf9aeb)

<hr>

#### Disponibilidade por Categoria

```python
plt.figure(figsize=(12, 6))
sns.boxplot(x='category', y='availability', data=df_clean)
plt.title('Disponibilidade por Categoria')
plt.xticks(rotation=45)
plt.show()

# Taxa de produtos em estoque por categoria
stock_rate = df_clean.groupby('category')['has_stock'].mean().sort_values(ascending=False)
print("\nTaxa de produtos em estoque por categoria:")
print(stock_rate)
```

![01](https://github.com/user-attachments/assets/6da896d8-78d2-406c-b713-1a19415a831f)

<hr>

#### Popularidade por Tamanho

```python
plt.figure(figsize=(15, 6))
size_counts = df_clean['size'].value_counts().sort_index()
sns.barplot(x=size_counts.index, y=size_counts.values)
plt.title('Distribuição de Tamanhos Disponíveis')
plt.xticks(rotation=90)
plt.show()

# Tamanhos mais comuns em estoque
stock_by_size = df_clean[df_clean['has_stock']]['size'].value_counts().head(10)
print("\nTamanhos mais comuns em estoque:")
print(stock_by_size)
```

![01](https://github.com/user-attachments/assets/ae4aac03-2b42-4d1e-9e59-cc87ff39b74d)

<hr>

#### Correlações

```python
numeric_cols = ['price', 'availability', 'size_numeric']
corr_matrix = df_clean[numeric_cols].corr()

plt.figure(figsize=(8, 6))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm')
plt.title('Matriz de Correlação')
plt.show()
```

![01](https://github.com/user-attachments/assets/0ce8baa2-56eb-47c4-928d-13c939df4251)






