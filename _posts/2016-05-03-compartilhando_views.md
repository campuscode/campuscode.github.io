---
layout: post
published: false
title: Compartilhando Views
author:
  display_name: Alan Batista
---

Hoje durante um _Code Session_ aqui no __Campus Code__ apareceu uma dúvida no
mínimo interessante: Onde colocar as [partial views][partial_view] de forma que
elas possam ser usadas em qualquer lugar no projeto?

## Opção Padrão

A resposta padrão seria na pasta `app/views/shared`, como indicado em vários
lugares, inclusive no [Agile Web Development][agile_web] no capítulo
__Action View__. Porém, neste caso devemos usar assim:

```
<%= render partial: "shared/minha_partial" %>
```

Onde, `minha_partial` estará em: `app/views/shared/_minha_partial.html.erb`.

## Usando o framework ao seu favor

O _lookup_ das views no Rails 4.x (que é a versão estável enquanto escrevo esse
post) é feito na pasta com mesmo nome do controller no plural, exemplo: 
`app/views/customers/` ou na pasta application, `app/views/application`.

Sendo assim, colocando `_minha_partial.html.erb` dentro da pasta
`app/views/application/` nos permitiria usar nossa partial assim:

```
<%= render 'minha_partial' %>
```

Muito mais simples, não?

## Considerações

Apesar de ser mais simples, não podemos esquecer que estamos nos fazendo valer
de uma característica do Rails e esta pode ser alterada na próxima versão, sendo
assim considere isso antes de sair mudando suas views de lugar.

E você, como faz nos seus projetos?

## Referências

- [Rails Casts #269 Template Inheritance][rails_casts]
- [Directory for shared partials in Rails][thoughtbot]
- [Rails Guides - Layouts and Rendering][guides]



[agile_web]:https://pragprog.com/book/rails4/agile-web-development-with-rails-4
[partial_view]:http://guides.rubyonrails.org/layouts_and_rendering.html#using-partials
[thoughtbot]:https://robots.thoughtbot.com/directory-for-shared-partials-in-rails#current-solution-appviewsapplication-directory
[rails_casts]:http://railscasts.com/episodes/269-template-inheritance?view=asciicast
[guides]:http://guides.rubyonrails.org/layouts_and_rendering.html#using-partials
