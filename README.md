# Padrão de Relacionamentos
1. Toda tabela deve conter um campo "**id**" auto_increment(auto incremental)
2. Toda tabela deve ser chamada no plural e tudo minúsculo ex: clientes, carros, produtos, vendas etc...
3. Toda coluna estrangeira(FK ou foreign key) deve conter o nome da tabela undeline id ex: **produto_id**
4. Toda tabela pivô deve se nomeada como: **tabela1_tabela2**

## HasOne
---
1 para 1 
exemplo: Cliente tem um Endereço 

```php
return $this->hasOne('App\Endereco');
```

ou completo:

```php
return $this->hasOne('App\Endereco', 'clienteid', 'enderecoid');
```
Captura:

```php
$endereco = Cliente::find(1)->endereco
```

### Inverso
```php
// Deve ser colocado no método cliente do Model Endereço
return $this->belongsTo('App\Cliente');
return $this->belongsTo('App\Cliente', 'enderecoid', 'clienteid');
```

## OneToMany
---
1 para muitos
exemplo: Um Post tem muitos Likes

```php
return $this->hasMany('App\Like');
```

ou completo

```php
return $this->hasMany('App\Like', 'likeid', 'postid');

$likes = Post::find(12)->likes
```

Com filtro:

```php
$likes_hoje = Post::find(10)->likes()->where('createdAt', '>=', '2020-07-11 00:00:00')->get();
```

### Inverso

```php
// Deve ser colocado no método post() do model Like

return $this->belongsTo('App\Post');

// Completo
return $this->belongsTo('App\Post', 'likeid', 'postid');

// Captura
$post = Like::find(123)->post
```

## ManyToMany
Precisa ser composta por uma pivot table que liga as duas relações, no caso de post e categoria, o post por ter varias categorias 
assim como a categoria pode esta presente em vários posts isso ocasiona a relação M:M segue um exemplo:

*posts*
- id - integer
- titulo - string
- conteudo - string

*categorias*
- id - integer
- nome - string

*post_categoria*
- post_id - integer
- categoria_id - integer

```php
// no model Post
return $this->belongsToMany('App\Categoria');

// versão completa (caso não siga padrão):
return $this->belongsToMany('App\Categoria', 'post_categoria', 'post_id', 'categoria_id');

// no model Categoria
return $this->belongsToMany('App\Post');
```

Para relacionar:

```php
// Post a categoria, primeiro crie os registros:
$post = new App\Post($postValores);
$post->save();

$cat = new App\Categoria($categoriaValores);
$cat->save();

// vinculando post a categoria
// fará o insert na tabela pivot(post_categoria)
$post->categorias()->attach(cat->id);

// Multiplas associações pelos IDs
$post->categorias()->attach([3, 8, 14, 22]); // IDs precisam existir na tabela Categorias
```

## Buscas e filtros com o ORM

```php
// Todos os Posts sempre retorna um collection
$posts = App\Post::all();
```

```php
// Recuperando um unico post pelo id: 2
// obs: returna o objeto do model ex:  Post
$posts = App\Post::find(2);
```

```php
// Recuperando post com filtro where
$posts = App\Post::where('titulo', 'Livros de TI')->get();
```

```php
// Recuperando produtos com filtro where qtd > 0
$produto = App\Produto::where('qtd', '>', 0)->get();
```

```php
// Filtros com and (where qtd > 0 and preco > 0)
$produto = App\Produto::where('qtd', '>', 0)->where('preco', '>', 0)->get();
```

```php
// Filtros com or (where nome like 'camiseta' or nome like 'camisa')
$produto = App\Produto::where('nome', 'like', 'camiseta')
                        ->orWhere('nome', 'like', 'camisa')->get();
```

```php
// Filtros campo que contém uma lista
$produto = App\Produto::whereIn('nome', ['camisa', 'camiseta', 'blusa', 'blusão'])->get();
```

```php
// Filtro com ordeção pelo nome decrescente
$produto = App\Produto::where('estoque', '>', 0)->orderBy('nome', 'desc')->get();
```

```php
// Pega os 2 primeiros resultados
$produto = App\Produto::where('estoque', '>', 0)->take(2)->get();
```

```php
// Pula os 5 primeiros registros(offset) e retorna o próximos 5 registros(limit)
$produto = App\Produto::where('estoque', '>', 0)->offset(5)->limit(5)->get();
```

```php
// Retornando relacionamentos 1:N
$post = App\Post::find(4);

// Retorna um collection de models Like(deve ser configurado no model Post)
$likes = $post->likes;
```

```php
// Filtro em relações
$post = App\Post::find(4);

// Retorna um collection de models Like(deve ser configurado no model Post)
// onde a data de criação aconteceu hoje
// observe os "()" em likes
$likes = $post->likes()->where('created_at', '>', date('Y-m-d 00:00:00'))->get();
```

```php
// busca por Posts que tem likes
$posts = App\Post::has('likes')->get();

// busca por Posts que não tem likes
$posts = App\Post::doesntHave('likes')->get();
```

##### Qualquer colaboração é bem vinda, lembrando é apenas uma simples documentação para aula, existe muito mais funcionalidades que podem ser encontradas em:

[Documentação Eloquent ORM](https://laravel.com/docs/7.x/eloquent-relationships)
