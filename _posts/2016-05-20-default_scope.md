---
layout: post
title: Default Scope, sempre uma boa idéia?
published: true
author:
  display_name: Alan Batista
---

Você está desenvolvendo um site institucional e este tem um model `Article` que
sempre que você precisa obter a lista de Articles, que acontece com muita
frequência, faz a seguinte consulta:

```ruby
Articles.where(published: true)
```

mas o que você queria era mesmo algo como:

```ruby
Articles.all
```

Só que nesse caso os Artigos (Articles) não publicados também são retornados,
chato não?

## Default Scope

Alguém te dá a ~~excelente idéia~~ de usar _default scope_ e você não hesita em
sair implementando no seu código:

```ruby
class Article < ActiveRecord::Base
  default_scope { where(published: true) }
end
```

Sucesso! Agora `Article.all` retorna somente os artigos publicados! Mas espera
aí! E os não publicados? 

Uma consulta rápida no google, te dá a resposta esperada:

```ruby
Articles.unscoped.all
```

Duplo sucesso! Todos seus problemas estão resolvidos!

## O perigo mora ao lado

Alguém, um estraga prazer, te avisa que todos os artigos estão sendo
automaticamente criados como publicados (published true) e você não consegue
entender porque.

Uma ida rápida ao nosso amigo __IRB__ revela o seguinte:

```ruby
2.3.0 :001 > Article.create(title: "Teste", body: "Qualquer")
    (0.1ms)  begin transaction
SQL (1.1ms)  INSERT INTO "articles" ("published", "title", "body", "created_at",
"updated_at") VALUES (?, ?, ?, ?, ?)  [["published", "t"], ["title", "Teste"], 
["body", "Qualquer"], ["created_at", "2016-05-20 20:00:14.377031"],
["updated_at", "2016-05-20 20:00:14.377031"]]
   (0.9ms)  commit transaction
 => <Article id: 1, title: "Teste", body: "Qualquer", PUBLISHED: TRUE,
created_at: "2016-05-20 20:00:14", updated_at: "2016-05-20 20:00:14">
```

Como assim? Quando você criou um artigo, sem definir se publicado ou não, ele
foi automaticamente definido como publicado? Não é possível, deve ser bug!

É meu caro! O __Default Scope__ está cobrando seu preço, e a falta de testes no
seu código também.

## Como melhorar

Primeira coisa que eu diria para você é, seja específico! Se quer os artigos não
publicados crie algo como:

```ruby
class Article < ActiveRecord::Base
  scope :published, -> { where(published: true) }
end
```

Isso lhe permitirá consultar os artigos não publicados assim:

```ruby
Articles.published
```

Muito melhor não? E ainda, por não ser o padrão, não afetará a criação de novos
artigos.

## Conclusão

Consultar a documentação oficial além de o lugar correto para encontrar
informação precisa, é ótimo para evitar problemas.

No caso do __default scope__ o guides não diz muito sobre como ele se comporta
na criação de novos registros, sendo necessário consultar a [documentação][api]
da API do rails.


## Referências

- [ActiveRecordScoping](http://api.rubyonrails.org/classes/ActiveRecord/Scoping/Default/ClassMethods.html)
- [Rails Guides - Active Record Query Interface](http://guides.rubyonrails.org/active_record_querying.html)

[api]:http://api.rubyonrails.org/
