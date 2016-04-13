---
layout: post
title: Keyword Arguments
author:
  display_name: Alan Batista
---

No [Code Saga](http://codesaga.com.br){:target="_blank"} temos um
[desafio](http://www.codesaga.com.br/challenges/buscando-o-ruby-parte-1){:target="_blank"},
em que surgem muitas dúvidas sobre como solucioná-lo.

Uma característica introduzida no Ruby 2.1 foi o **Keyword Arguments** e ela pode
no ajudar nesse desafio. Vamos ver como isso funciona.

Suponha que você quer um parâmetro opcional, para isso você faria:

```ruby
def pagar(valor, forma_pagto='boleto')
 puts "Pagando R$#{valor} com #{forma_pagto}"
end

pagar(1000)
#=> Pagando R$1000 com boleto
```

O código acima não tem nenhum problema, mas requer do programador que vai usá-lo
conhecer no mínimo quais parametros e em qual ordem eles são esperados:

```ruby
pagar('cartão', 1000)
#=> Pagando R$cartão com 1000
```


Nesse caso o **keyword arguments** ajuda muito, veja o exemplo:

```ruby
def pagar(valor:, forma_pagto: 'boleto')
  puts "Pagando R$#{valor} com #{forma_pagto}"
end

pagar(1000)
#=>BOOOOM! ArgumentError: wrong number of arguments (given 1, expected 0)

pagar(valor: 1000)
#=> Pagando R$1000 com boleto

pagar(forma_pagto: 'Cartão', valor: 1300)
#=> Pagando R$1300 com Cartão
```

Veja que na segunda execução do método `#pagar` nós passamos os parametros em
ordem diferente da qual eles foram declarados e ainda assim o método funcionou
como esperado.

Podemos esperar argumentos adicionais, porém também "nomeados", veja:

```ruby
def pagar(valor:, forma_pagto:, **opcoes)
  puts "Pagando R$#{valor} com #{forma_pagto}"
  puts "Opcoes #{opcoes}"
end

pagar(forma_pagto: 'Cartão', valor: 1300, limite:3000, autorizado: true)
#=> Pagando R$1300 com Cartão
Opcoes {:limite=>3000, autorizado: true}

pagar(forma_pagto: 'Cartão', valor: 1300, 3000, true)
#=> BOOOOM!!! SyntaxError: (irb):26: syntax error, unexpected ',', expecting =>
```

A segunda execução do exemplo acima nos mostra que o uso de _keyword arguments_
exige a nomeação de qualquer argumento, até os adicionais!

## Keyword Arguments X Hash arguments

Você já deve ter visto algo como:

```ruby
def algum_metodo(params)
  arg1 = params[:arg1]
  arg2 = params[:arg2]
  arg3 = params[:arg3]
  puts "valor arg1: #{arg1} arg2: #{arg2} arg3: #{arg3}"
end

algum_metodo(arg1: 1431, arg2: 'valor', arg3: :symb)
#=> valor arg1: 1431 arg2: valor arg3: :symb

algum_metodo(arg1: 1431, arg2: 'valor', arg1: :symb)
#=> valor arg1: symb arg2: valor arg3:
```

Consegue achar o erro na segunda execução no exemplo acima? Difícil né? Vamos
implementar o mesmo código com o **keywords arguments**:

```ruby
def algum_metodo(arg1:, arg2:, arg3:)
  puts "valor arg1: #{arg1} arg2: #{arg2} arg3: #{arg3}"
end

algum_metodo(arg1: 1431, arg2: 'valor', arg3: :symb)
#=> valor arg1: 1431 arg2: valor arg3: :symb

algum_metodo(arg1: 1431, arg2: 'valor', arg1: :symb)
#=> BOOOOM! ArgumentError: missing keyword: arg3
```

## Keyword Arguments X Argumentos Posicionais

Aqui a coisa complica um pouco... Você já escreveu muito código com argumentos
posicionais e não viu nenhum problema nisso.

Pois é, o problema mora quando outro programador (você de amanhã) precisa usar
esse código que você escreveu:

```ruby
def meu_link_to(url_or_path, text, css, *options)
 # implentacao por conta da imaginação
end

meu_link_to('Texto', posts_path, "post_link")
#=> <a class="post_link" href="Texto">/posts</a>
```

Um pouco estranho, né? Você, durante a implementação, achou que colocar o _path_
ou _url_ como primeiro argumento faria todo o sentido, mas esqueceu disso quando
foi utilizar o método. É a vida, não é mesmo?!

## Considerações

Não há reais motivos para você sair refatorando todos seus códigos agora, mas é
bom considerar **keyword arguments** quando implementar aquele métodos que a
ordem dos argumentos não são lá tão intuitivos quanto parecem a primeira
impressão.

Uma boa regra é, poucos argumentos (1 ou 2), que façam sentido na ordem que você
implementou, não são necessariamente candidatos para keyword arguments. Métodos
com 3 ou mais argumentos podem ser candidatos a um belo **Refactoring**, mas isso é
assunto para outro post ;).

E você, o que acha dos keyword arguments, devemos usá-los quando? Deixe seu
comentário.

Até mais!


### Referências

- [Documentação
  Oficial](https://robots.thoughtbot.com/ruby-2-keyword-arguments){:target="_blank"}
- [Ruby 2 Keyword
  Arguments](https://robots.thoughtbot.com/ruby-2-keyword-arguments){:target="_blank"}
