---
layout: post
published: false
title: Frozen Strings
author:
  display_name: Alan Batista
---

Todas as String literais serão imutáveis (frozen) no Ruby 3.0, mas o que isso
significa?

Hoje nós podemos fazer algo do tipo:

```ruby
mensagem = 'uma string qualquer'.upcase!
mensagem.upcase!
```

Esse código alteraria a string `'uma string qualquer'` para `'UMA STRING
QUALQUER'`, apesar de muito legal, isso tem um custo na performance do nosso
código.

Por esse motivo, a partir do Ruby 3.0 isso não será mais possível e você pode
ver a discussão que rolou no [Twitter][disc_twitter].

Para suportar melhor a transição para a nova versão, no Ruby 2.3 nós podemos
colocar um comentário mágico (magic comment) que além de deixar nosso código
mais compatível, congelará as string, fazendo com que seu código, caso tenha
strings literais, tenha um desempenho melhor.

Adicione o comentário no inicio do código como no exemplo:

```ruby
# frozen_string_literal: true

require 'rails_helper'

feature 'User adds a comment' do
  scenario 'sucessfully' do
  end
end
```

Se já esta usando o Ruby 2.3 experimente adicionar esse comentário nos seus
código, nós já estamos fazendo isso!

[disc_twitter]:https://twitter.com/yukihiro_matz/status/634386185507311616

