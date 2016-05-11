---
layout: post
title: Client Side Validations
published: false
author:
  display_name: Alan Batista
---

Com alguma frequência nos perguntam como fazer validação no browser, que neste
post chamaremos de _Client Side Validations_ ou validações no lado do cliente.

Não há resposta certa aqui, dependendo da habilidade do programador que está
perguntando poderia dar algumas respostas, entre elas: _Deixe pra lá e valide
somente no servidor com as validações do ActiveRecord_, mas essa nem sempre
agrada.

Outra resposta é: implemente você em Javascript. O ponto negativo aqui é que
você terá que refletir exatamente as validações que você fez no server, afim de
deixar seu código minimamente coerente e lembre-se, você terá que manter essas
validações semelhantes durante o ciclo de vida do seu projeto.

Agora, se o programador tiver alguma experiência prévia e realmente precisar
implementar tais validações, uma alternativa é sugerir a gem
[ClientSideValidations][ClientSideValidations]. Além de razoavelmente fácil de
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

E por último, adicione a seguinte linha no seu
`app/assets/javascripts/application.js`:

```
//= require rails.validations
```

## Uso

No projeto que estou usando para este post, já existe uma classe _Customer_ com
algumas validações do Rails.

```ruby
class Customer < ActiveRecord::Base
  validates :name, :cpf, :address_number, :address_street, presence: true
end

```

No form devemos informar que queremos a validação no lado do cliente:

```erb
<%= form_for(@model, validate: true) %>
  ...
<% end %>
```

Abra seu projeto para testá-lo:

```
rails server
```

No meu teste, fiz a validação em um model Customer, então vamos vê-lo no browser:

```
http://localhost:3000/customers/new
```

## Resultado

Antes, sem o client_side_validations:

![Sem client_side_validations](/assets/images/sem_client_side.gif)

E agora com client_side_validations:

![Com client_side_validations](/assets/images/com_client_side.gif)


## SimpleForm

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
receberá um erro de Javascript na sua página por conta das dependências.

E para adicionar no seu form é simples como deve ser:

```erb
<%= simple_form_for @customer, validate: true do |f| %>
  <%= f.input :name %>
<% end %>
```

## Custom Validators

Na documentação do [Rails][rails-validators] temos exemplos de custom validators
(validadores customizados) e aqui vamos mostrar um exemplo de um validator para
CEP que precisará de um validação no remota servidor, faremos isso com AJAX.

Vamos começar criando nosso model __Cep__:

```
rails g model cep codigo:string
```

Onde _codigo_ é o número do CEP.

No nosso caso vamos adicionar no nosso model __Customer__ o atributo cep:

```
rails g migration add_cep_to_customer cep
```

E não se esqueça de rodar as migrations:

```
rake db:migrate
```

Vamos criar uma validação no server com o arquivo
`app/validators/brazilian_zip_code_validator.rb`, lembrando que a pasta validators também
precisa se criada:

```ruby
class BrazilianZipCodeValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless Cep.find_by(codigo: value)
      record.errors.add(:cep, :cep) # atributo :cep, chave do locale :cep
    end
  end
end
```

Essa validação nos permitirá adicionar a validação de CEP no model, como no
exemplo abaixo:

```ruby
class Customer < ActiveRecord::Base
...
  validates :name, :cpf, :address_number, :address_street, presence: true
  validates :cep, brazilian_zip_code: true
...
end
```

Pronto, a validação está pronta para ser executado no server, mas espere, o foco
desse post era a validação no client, entãa vamos lá.

Crie o arquivos `app/assets/javascripts/rails.validations.customValidators.js`
com o seguinte conteúdo:

```javascript
window.ClientSideValidations.validators.remote['brazilian_zip_code'] = function(element, options) {
  if ($.ajax({
    url: '/validators/cep',
    data: { id: element.val() },
    // async DEVE estar definido como false
    async: false
  }).status == 404) { return options.message; }
}
```

Veja que `cep` nesse caso é o nome da validação e não do campo. (Sim, eu achei
ambíguo).

Agora nós precisamos criar um middleware para lidar com a validação client side,
para isso vamos criar o arquivo `lib/client_side_validations/middleware/cep.rb`:

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
`autoload`, edite o arquivo `config/application.rb` com a seguinte linha:

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
      brazilian_zip_code: "Not a valid zip code"
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
cd config/locale
curl -O https://raw.githubusercontent.com/svenfuchs/rails-i18n/master/rails/locale/pt-BR.yml
```

E edite o arquivo `config/locales/pt-BR.yml` respeitando a identação:

```yml
pt-BR:
  activerecord:
    errors:
      messages:
        brazilian_zip_code: CEP inexistente # <----- adicione sua linha aqui!
...
```

Você deve inserir a linha `brazilian_zip_code: CEP inexistente` abaixo de `messages:` e
identado com dois espaços.

## Conclusão

Com essa gem fica fácil adicionar validação Client Side, mesmo para as
validações remotas, se feita com atenção, é bastante simples.

O código usado neste post está [aqui][codigo_exemplo]

E nos seus projetos, como você costuma criar as validações?

[codigo_exemplo]:https://github.com/campuscode/client_side_validations_example
[ClientSideValidations]:https://github.com/DavyJonesLocker/client_side_validations
[bestpraticesvalidations]:http://alistapart.com/article/inline-validation-in-web-forms
[simple-form]:https://github.com/plataformatec/simple_form
[pt-br.yml]:https://raw.githubusercontent.com/svenfuchs/rails-i18n/master/rails/locale/pt-BR.yml
