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

##### Qualquer colaboração é bem vinda, lembrando é apenas uma simples documentação para aula, existe muito mais funcionalidades que podem ser encontradas em:

[Documentação Eloquent ORM](https://laravel.com/docs/7.x/eloquent-relationships)
