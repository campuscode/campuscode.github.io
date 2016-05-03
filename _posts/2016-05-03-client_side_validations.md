---
layout: post
title: Client Side Validations
published: false
author:
  display_name: Alan Batista
---

## Introdução

Com alguma frequência nos perguntam como fazer validação no browser, que neste
post chamaremos de _Client Side Validations_ ou validações no lado do cliente.

Não há resposta certa aqui, dependendo da habilidade do programador que está
perguntando poderia dar algumas respostas, entre elas é: _Deixe pra lá e valide
somente no servidor com as validações do ActiveRecord_, mas essa por sua vez
nem sempre agrada.

Outra resposta é implemente você em javacript, porém há um problema, você terá
que refletir exatamente as validações que você fez no server, afim de deixar seu
código minimamente coerente e lembre-se, você terá que manter essas validações
semelhantes durante o ciclo de vida do seu projeto.

Agora, se o programador tiver alguma experiência prévia e realmente precisar
implementar tais validações, uma alternativa é sugerir a gem
[ClientSideValidations][ClientSideValidations], além de razoavelmente fácil de
ser inserida no seu projeto, ela se preocupa com algumas boas práticas, tais
como aplicar as validações que estão no server no client, entre
[outras][bestpraticesvalidations].

## Configurando

No seu _Gemfile_ adicione a linha:

```ruby
gem 'client_side_validations'
```

E instale as novas dependências no seu projeto:

```
bundle install
```

E então rodo o instalador da gem:

```
rails g client_side_validations:install
```

Esse instalador adicionará esse arquivo
`config/initializers/client_side_validations.rb` no seu projeto. Vamos editar
esse arquivo conforme explicado da documentação oficial.

Descomente o bloco abaixo, ele deve estar no fim do arquivo:

```ruby
ActionView::Base.field_error_proc = Proc.new do |html_tag, instance|
  unless html_tag =~ /^<label/
    %{<div class="field_with_errors">#{html_tag}<label for="#{instance.send(:tag_id)}" class="message">#{instance.error_message.first}</label></div>}.html_safe
  else
    %{<div class="field_with_errors">#{html_tag}</div>}.html_safe
  end
end
```

E por ultimo, adicione a seguinte linha no seu
`app/assets/javacripts/application.js`:

```
//= require rails.validations
```

## Uso

Se você utilizou scaffold no seu projeto (o que eu espero que não!!!) seu
projeto estará quase pronto, mas com aquela _carinha_ bacana que só o _scaffold_
faz por você, mas caso não tenha usado, vamos editar um arquivo css, mas não
agora.

Agora no seu form você deve dizer que usará validações, como a seguir:

```erb
<%= form_for(@model), validations: true %>
...
<% end %>
```

No seu model dever ter as validações necessárias, exemplo:

```ruby
class Customer < ActiveRecord::Base
  has_many :contracts
  validates :name, :cpf, presence: true
  validates :cpf, numericality: true
end

```

Abra seu projeto para testá-lo:

```
rails server
```

No meu teste, fiz a validação em um model Customer, então vamos vê-lo no browser:

```
http://localhost:3000/customers/new
```

E tentar salvar nosso form:

IMAGEM

E como podemos ver no log, nada foi enviado, como esperado:


Calma que não acabamos ainda, esse gem tem um plugin para o
[SimpleForm][simple-form] que para nós aqui no __Campus Code__ faz toda a
diferença.

Para adicioná-lo basta adicionar as linhas no seu Gemfile:

```ruby
gem 'simple_form'
gem 'client_side_validations'
gem 'client_side_validations-simple_form'
```

E adicionar a seguinte linha no seu `app/assets/javascript/application.js`, após
a linha que você já tinha adicionado:

```
//= require rails.validations.simple_form
```

Caso esse linha seja adicionada antes do `//=require rails.validations` você
receberá um erro de javascript na sua página por conta das dependências.

E para adicionar no seu form é simples como deve ser:

```erb
<%= simple_form_for @book, validate: true do |book| %>
  <%= book.input :name %>
<% end %>
```

## Custom Validators

Na documentação do [Rails][rails-validators] temos exemplos de custom validators
(Validadores customizados) e aqui vamos mostrar um exemplo de um validator para
Cep, o qual necessitará de um validação no servidor.

Vamos começar criando nosso model __Cep__:

```
rails g model cep codigo:string
```

Onde _codigo_ é o número do cep.

Vamos assumir que você tem um modelo _Company_ (ou qualquer outro nome) com um
atributo cep do tipo _String_.

Vamos criar uma validação no server, vamos criar o arquivo
`app/validators/cep_validator.rb`, lembrando que a pasta validators também
precisa se criada:

```ruby
class CepValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless Cep.find_by(codigo: value)
      record.errors.add(:cep, :cep) # atributo :cep, chave do locale :cep
    end
  end
end
```

Essa validação nos permitirá adicionar a validação de Cep no model, como no
exemplo abaixo:

```ruby
class Company < ActiveRecord::Base
...
  validates :name, :location, :mail, :phone, :cep, presence: true
  validates :cep, cep: true
...
end
```

Pronto, a validação está pronta para ser executado no server, mas espere, o foco
desse post era a validação no client, entãa vamos lá.

Crie o arquivos `app/assets/javacripts/rails.validations.customValidators.js`
com o seguinte conteúdo:

```javascript
window.ClientSideValidations.validators.remote['cep'] = function(element, options) {
  if ($.ajax({
    url: '/validators/cep',
    data: { id: element.val() },
    // async DEVE estar definido como false
    async: false
  }).status == 404) { return options.message; }
}
```

Agora nós precisamos criar um middleware para lidar com a validação client side,
para isso vamos criar o arquivo 'lib/client_side_validations/middleware/cep.rb':

```ruby
module ClientSideValidations::Middleware
  class Cep < ClientSideValidations::Middleware::Base
    def response
      if ::Cep.find_by(codigo: request.params[:id])
        self.status = 200
      else
        self.status = 404
      end
      super
    end
  end
end
```

Como adicionamos um arquivo na pasta `lib`, vamos adicionar a pasta lib no
`autoload`, edit o arquivo `config/application.rb` com a seguinte linha:

```ruby
config.autoload_paths << "#{Rails.root}/lib"
```

Último e não menos importante, o client_side_validations precisa de uma mensagem
de erro para ser exibida no client, para isso vamos editar o arquivo
`config/locales/en.yml` com o conteúdo abaixo:

```yml
en:
  errors:
    messages:
      cep: "Not a valid zip code"
```

É isso, vamos testar agora:

IMAGEM

## Bônus

Já que o exemplo estava todo em pt-BR, nada mais justo que deixar nosso projeto
em pt-BR também.

Vamos começar mudanda a localização do nosso projeto para `pt-BR` editando
novamente o arquivo `config/application.rb`, adicione a linha a seguir:

```ruby
config.i18n.default_locale = :'pt-BR'
```

Depois, na pasta do seu projeto, adicione esse [arquivo][pt-br.yml] na pasta
`config/locale` ou ainda mais fácil, rode o seguinte comando no terminal na
pasta locale do projeto:

```
curl -O https://raw.githubusercontent.com/svenfuchs/rails-i18n/c0927b490aa2ff9c536c15bcb2eebed77aa22e88/rails/locale/pt-BR.yml
```

E edite o arquivo `config/locales/pt-BR.yml` respeitando a identação:

```yml
pt-BR:
  activerecord:
    errors:
      messages:
        cep: Cep inexistente
        record_invalid: 'A validação falhou: %{errors}'
...
```

Você deve inserir a linha `cep: Cep inexistente` abaixo de `messages:` e
identado com dois espaços.

[ClientSideValidations]:https://github.com/DavyJonesLocker/client_side_validations
[bestpraticesvalidations]:http://alistapart.com/article/inline-validation-in-web-forms
[simple-form]:https://github.com/plataformatec/simple_form
[pt-br.yml]:https://raw.githubusercontent.com/svenfuchs/rails-i18n/c0927b490aa2ff9c536c15bcb2eebed77aa22e88/rails/locale/pt-BR.yml
