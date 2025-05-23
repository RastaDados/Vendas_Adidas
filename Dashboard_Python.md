# Dashboard Utilizando a Biblioteca Dash do Python

```python
#Criando o aplicativo em Dash
app = Dash(__name__)

#Obtendo as categorias e gêneros únicos, removendo valores nulos
unique_categories = [cat for cat in df_clean['category'].unique() if pd.notnull(cat)]
unique_genders = [gen for gen in df_clean['gender'].unique() if pd.notnull(gen)]

app.layout = html.Div([
    html.H1("Análise de Vendas da Adidas", style={'textAlign': 'center', 'color':'yellow'}),
    
    html.Div([
        dcc.Dropdown(
            id='category-selector',
            options=[{'label': cat, 'value': cat} for cat in unique_categories],
            value=unique_categories,  # Seleciona todos por padrão
            multi=True,
            placeholder="Selecione categorias"
        )
    ], style={'width': '48%', 'display': 'inline-block'}),
    
    html.Div([
        dcc.Dropdown(
            id='gender-selector',
            options=[{'label': gen, 'value': gen} for gen in unique_genders],
            value=unique_genders,  # Seleciona todos por padrão
            multi=True,
            placeholder="Selecione gêneros"
        )
    ], style={'width': '48%', 'float': 'right', 'display': 'inline-block'}),
    
    dcc.Graph(id='price-distribution'),
    
    dcc.Graph(id='availability-plot'),
    
    dcc.Graph(id='size-distribution'),
    
    html.Div([
        dcc.Graph(id='price-vs-size'),
        dcc.Graph(id='stock-rate')
    ], style={'display': 'flex'})
])

@app.callback(
    [Output('price-distribution', 'figure'),
     Output('availability-plot', 'figure'),
     Output('size-distribution', 'figure'),
     Output('price-vs-size', 'figure'),
     Output('stock-rate', 'figure')],
    [Input('category-selector', 'value'),
     Input('gender-selector', 'value')]
)
def update_graphs(selected_categories, selected_genders):
    #Filtrando o DataFrame baseado nas seleções
    if not selected_categories:  #Se nenhuma categoria for selecionada, mostra todas
        selected_categories = unique_categories
    if not selected_genders:  #Se nenhum gênero for selecionado, mostra todos
        selected_genders = unique_genders
    
    filtered_df = df_clean[
        (df_clean['category'].isin(selected_categories)) & 
        (df_clean['gender'].isin(selected_genders))
    ]
```

<hr>

#### Gráfico de distribuição de preços

```python
 price_fig = px.histogram(
        filtered_df, x='price', nbins=30, 
        title='Distribuição de Preços',
        labels={'price': 'Preço (€)'}
    )
```

![01](https://github.com/user-attachments/assets/188897c0-2da7-4ea2-9543-4a9390cb0baf)

<hr>

#### Gráfico de Disponibilidade

```python
avail_fig = px.box(
        filtered_df, x='category', y='availability',
        title='Disponibilidade por Categoria',
        labels={'availability': 'Unidades Disponíveis', 'category': 'Categoria'}
    )
```

<hr>

#### Gráfico de Distribuição de Tamanhos

```python
size_counts = filtered_df['size'].value_counts().reset_index()
    size_counts.columns = ['size', 'count']
    size_fig = px.bar(
        size_counts, x='size', y='count',
        title='Distribuição de Tamanhos',
        labels={'size': 'Tamanho', 'count': 'Contagem'}
    )
```

<hr>

#### Gráfico de Preço vs Tamanho

```python
price_size_fig = px.scatter(
        filtered_df, x='size_numeric', y='price', color='category',
        title='Preço vs Tamanho',
        labels={'size_numeric': 'Tamanho Numérico', 'price': 'Preço (€)'}
    )
```

<hr>

#### Taxa de Estoque por Categoria

```python
stock_rate = filtered_df.groupby('category')['has_stock'].mean().reset_index()
    stock_fig = px.bar(
        stock_rate, x='category', y='has_stock',
        title='Taxa de Produtos em Estoque por Categoria',
        labels={'has_stock': 'Taxa de Estoque', 'category': 'Categoria'}
    )
```

```python
    return price_fig, avail_fig, size_fig, price_size_fig, stock_fig

if __name__ == '__main__':
    app.run_server(debug=True)
```



