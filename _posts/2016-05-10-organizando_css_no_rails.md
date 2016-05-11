---
layout: post
title: Organizando seu CSS no Rails com Sass
author:
  display_name: João Almeida
---

No começo de cada time do **Campus Code** fazemos uma pequena revisão de
tecnologias ligadas ao desenvolvimento Web, incluindo HTML 5 e CSS. Alguns dias
depois, já programando suas aplicações Rails, uma dúvida sempre aparece: como
organizar o CSS no Rails?

## Asset Pipeline

Para começar sempre precisamos entender o que o Rails nos oferece por padrão, e
para tratar o CSS e outros assets como Javascripts, temos o [Asset Pipeline][asset_pipeline].
Ele permite que todo Javascript e CSS sejam unificados e comprimidos além de
adicionar **fingerprints** aos arquivos.

Se você criar uma nova aplicação Rails terá por padrão em `app/assets/stylesheets`
um arquivo `application.css` com várias linhas, mas o mais importante está no final:

```css
/* ...
*= require_tree .
*= require_self
*/
```

Este arquivo não é um CSS comum, trata-se de um **manifesto**. Nele adicionamos
**diretivas** que indicam como montar o arquivo CSS definitivo. A diretiva
`require_tree .` indica que todos arquivos CSS em `app/assets/stylesheets` devem
ser incluídos no arquivo final. Já a diretiva `require_self` indica que o CSS do
próprio `application.css` deve ser incluído.

Para exemplificar, criamos o arquivo `app/assets/stylesheets/nav.css` com o
seguinte conteúdo:

```css
nav {
  background-color: #333;
  color: #fff;
}

nav h1 {
  padding: 10px;
  font-family: "Trebuchet MS", Helvetica, sans-serif;
}

nav a {
  color: #000;
}
```

E vamos adicionar o seguinte CSS no arquivo `application.css`:

```css
/* ...
*= require_tree .
*= require_self
*/

body {
  padding: 0;
  margin: 0;
}

```


Agora podemos executar a rake task `rake assets:precompile`. Ao final teremos os
arquivos CSS e JS que nossa aplicação irá usar em produção no diretório
`public/assets`. Repare que o código dos dois arquivos foi unificado e seu nome
é algo como `application-6ff728c3941a2b32cfe87975064520eb3bd901e50aba7f1315513697dae08b95.css`.

Este arquivo final, além de unificar o CSS possui um **fingerprint** em seu nome.
O fingerprint é gerado a partir do conteúdo do arquivo, ou seja, mesmo que você
faça vários deploys da sua aplicação, só mudanças no CSS implicam em um novo
fingerprint. A grande vantagem deste modelo é permitir que os browsers dos
clientes utilizem o cache de forma mais eficiente.

## Sass Rails

Agora que já vimos de forma bem rápida como funciona o Asset Pipeline, vamos
falar de outra componente que os projetos Rails possuem por padrão: a gem `sass-rails`.

Essa gem permite que façamos uso de [Sass][sass_website] para escrever nosso CSS.
Vou citar aqui algumas vantagens de utilizarmos o Sass mas recomendo a leitura
da documentação completa sempre :)

### Configuração

Como a gem já está no nosso `Gemfile` por padrão, a única coisa que precisamos
fazer é criar os arquivos com a extensão `scss` nos nossos assets.

### Variáveis

Com Sass podemos declarar variáveis e facilitar a manutenção do nosso CSS.

```scss

$background-color: #e9e9e9;
$primary-color: #3299bb;
$alternative-color: #ff9900;

body {
  color: $primary-color;
  background-color: $background-color;
}

a {
  color: $alternative-color;
}

```

Para os exemplos abaixo, vou considerar que o código acima está salvo no arquivo `colors.scss`.

### Nesting

Outra característica de Sass que permite criar um CSS mais enxuto é o aninhamento. Com ele podemos trocar códigos repetitivos como:

```scss

@import 'colors';

nav {
  background-color: $background-color;
}

nav ul {
  display: inline;
  padding: 10px;
  list-style: none;
}

nav ul li {
  text-align: center;
  margin: 0 20px;
}

nav ul li a {
  color: $alternative-color;
  text-decoration: none;
}

nav ul li a:hover {
  text-decoration: underline;
}

```

Por isso:

```scss
@import 'colors';

nav {
  background-color: $background-color;

  ul {
    display: inline;
    padding: 10px;
    list-style: none;

    li {
      text-align: center;
      margin: 0 20px;

      a {
        color: $alternative-color;
        text-decoration: none;
        &:hover { text-decoration: underline; }
      }
    }
  }
}
```


### Extend

Para reaproveitar estilo em classes com objetivos semelhantes, uma boa opção é utilizar o `extend`.

```scss
@import 'colors';

.title {
  font-size: 1.2 em;
  color: $primary-color;
  padding: 20px 0;
  border-bottom: solid 1px $alternative-color;
}

.internal-title {
  @extend .title;
  color: #449900;
}

.alternate-title {
  @extend .title;
  color: $alternative-color;
}
```

## Colocando em prática

Espero que você tenha visto as vantagens de usar o Sass em seu projeto Rails,
agora vamos colocar tudo isso em prática. Existem várias outras funcionalidades
que não citamos aqui mas que são bem detalhadas na [documentação oficial][sass_doc].

Para continuar nosso exemplo, peguei os códigos acima e gerei três arquivos:
`colors.scss`, `nav.scss` e `titles.scss`

A primeira coisa que fazemos aqui no Campus Code é **renomear** o arquivo 
`applications.css` para `application.scss` e **remover todo conteúdo prévio**.

Ao invés das diretivas `require` vamos usar o `@import` do Sass. Além do arquivo
ficar mais limpo isso traz mais controle para nosso código, afinal a
**ordenação** é muito relevante. No nosso exemplo o arquivo `application.scss`
ficou assim:


```scss
@import 'colors';
@import 'nav';
@import 'titles'
```

Outro ponto é que podemos remover os `@import 'colors';` do início dos arquivos
`nav.scss` e `titles.scss`.

## Conclusão

Neste post vimos o que o Rails nos oferece por padrão para organização e escrita
do CSS. Mesmo num overview rápido podemos notar como o uso de Sass permite criar
CSS mais **DRY**.

E você? Como organiza o CSS na sua aplicação? Diga ai nos comentários.

## Referências

 * [Documentação de Asset Pipelines][asset_pipeline]
 * [Documentação do Sass][sass_doc]

[asset_pipeline]:http://guides.rubyonrails.org/asset_pipeline.html
[sass_website]:http://sass-lang.com/
[sass_doc]:http://sass-lang.com/documentation/file.SASS_REFERENCE.html
