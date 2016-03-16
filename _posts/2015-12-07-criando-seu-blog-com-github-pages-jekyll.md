---
layout: post
status: publish
published: true
title: Criando seu blog com Github Pages + Jekyll
author:
  display_name: João Almeida
  login: joaorsalmeida
  email: joaorsalmeida@gmail.com
  url: ''
author_login: joaorsalmeida
author_email: joaorsalmeida@gmail.com
wordpress_id: 139
wordpress_url: http://campuscode.com.br/blog/?p=139
date: '2015-12-07 15:23:14 -0200'
date_gmt: '2015-12-07 18:23:14 -0200'
categories:
- Tutoriais
tags:
- github
- jekyll
- blog
comments: []
---
Ia come&ccedil;ar esse post falando da import&acirc;ncia de devs manterem blogs
contando experi&ecirc;ncias e compartilhando conhecimento com toda comunidade.
Mas voc&ecirc; j&aacute; deve ter notado isso lendo posts ou vendo tutoriais em
p&aacute;ginas como a do
<a href="http://albertoleal.me" target="_blank">Alberto Leal</a>,
<a href="https://nandovieira.com.br" target="_blank">Nando Vieira</a>,
<a href="http://pothix.com" target="_blank">Pothix</a>, entre tantos outros...

Comunicar-se &eacute; um requisito chave para membros de times de desenvolvimento e uma forma de praticar isso &eacute; escrevendo. Por mais simples que um assunto seja para voc&ecirc;, com certeza algu&eacute;m vai se beneficiar da sua explica&ccedil;&atilde;o ou opini&atilde;o.
Esse post mostra um jeito simples e r&aacute;pido de subir uma p&aacute;gina pessoal/blog usando sua pr&oacute;pria conta do Github: o
<a href="https://pages.github.com" target="_blank">Github Pages</a>.
Por que&nbsp;este caminho? Pois assim vamos utilizar v&aacute;rias ferramentas
triviais de um desenvolvedor como Markdown, Ruby Gems e sua conta no Github, &eacute; claro.

## Vamos come&ccedil;ar?

Pr&eacute; Requisitos
Antes de tudo, estou assumindo que voc&ecirc; j&aacute; tenha uma conta no Github e uma vers&atilde;o de Ruby e da gem Bundler instalados e prontos na sua m&aacute;quina. Se voc&ecirc; tem d&uacute;vidas sobre como fazer tudo isso, o <a href="http://www.codesaga.com.br" target="_blank">Code Saga</a> pode te ajudar :)
Instalando o Jekyll
Apesar do Github Pages suportar p&aacute;ginas HTML est&aacute;ticas, vamos usar o Jekyll para produzir nosso conte&uacute;do. O Jekyll vai nos entregar de forma instant&acirc;nea features como categorias, permalinks e posts organizados. Sua instala&ccedil;&atilde;o &eacute; simples:
gem install jekyll
Navegue para seu diret&oacute;rio de trabalho (no meu caso ~/workspace) e execute o seguinte:
{% highlight bash %}
  jekyll new blog
  cd blog
  jekyll serve
{% endhighlight %}

Pronto! Agora voc&ecirc; pode entrar no diret&oacute;rio e subir o server embarcado do Jekyll para ver seu blog rodando localmente em http://localhost:4000.

<a href="/assets/2015/12/Screen-Shot-2015-12-05-at-18.07.00.png"><img class="size-full wp-image-149" src="/assets/2015/12/Screen-Shot-2015-12-05-at-18.07.00.png" alt="Blog rec&eacute;m criado via Jekyll" width="810" height="581" /> Blog rec&eacute;m criado via Jekyll</a>

## Configurando seu Github Pages

Ok. Temos nosso blog rodando localmente, mas o objetivo aqui &eacute;
public&aacute;-lo usando o servi&ccedil;o Pages do Github. Ent&atilde;o acesse
sua conta e crie um novo reposit&oacute;rio com o nome seu_nome_de_usuario.github.io.

<a href="/assets/2015/12/Screen-Shot-2015-12-05-at-18.09.07.png"><img class="size-full wp-image-151" src="/assets/2015/12/Screen-Shot-2015-12-05-at-18.09.07.png" alt="Criando um reposit&oacute;rio com o padr&atilde;o: nome_de_usuario.github.io" width="748" height="569" /> Criando um reposit&oacute;rio com o padr&atilde;o: nome_de_usuario.github.io</a>

Voc&ecirc; ter&aacute; um novo reposit&oacute;rio remoto criado no Github.&nbsp;
Vamos voltar para o diret&oacute;rio onde criamos o blog. Primeiro mate o
processo jekyll serve com um ctrl c.

Agora vamos iniciar um reposit&oacute;rio Git local e adicionar o remote.

{% highlight bash %}
git init
git remote add origin
{% endhighlight %}

Ent&atilde;o basta fazer o primeiro commit/push :)

{% highlight bash %}
git add .
git commit -am 'Commit inicial do meu blog'
git push origin master
{% endhighlight %}

Pronto! Acesse a URL do seu Github Pages (nome_de_usuario.github.io) que seu blog j&aacute; est&aacute; no ar.

## Escrevendo posts

Como disse no come&ccedil;o desse post, vamos usar
<a href="https://daringfireball.net/projects/markdown/" target="_blank">Markdown</a>
para criar nossos posts. Acho Markdown uma op&ccedil;&atilde;o muito
interessante por ser simples, leve e utilizada em v&aacute;rias
situa&ccedil;&otilde;es como o Readme de projetos no Github, por exemplo. Um
tutorial que indico para quem est&aacute; come&ccedil;ando &eacute; o
<a href="https://help.github.com/articles/markdown-basics/" target="_blank">Markdown Basics, do Github.</a>

Um post em Jekyll &eacute; simplesmente um arquivo .markdown dentro da pasta
`_posts`. Voc&ecirc; pode abrir o arquivo `_posts/-welcome-to-jekyll.markdown`
e us&aacute;-lo como exemplo. No topo do arquivo temos o
<a href="http://jekyllrb.com/docs/frontmatter/" target="_blank">YAML Front Matter.</a>

Nele definimos alguns par&acirc;metros como layout, t&iacute;tulo e categorias do post. Em seguida temos o texto do post, simples assim.
Para criar um novo post basta criar um novo arquivo e seguir a conven&ccedil;&atilde;o do YAML Front Matter no in&iacute;cio e o conte&uacute;do em seguida. Por exemplo:
`touch _posts/2015-12-05-um-novo-post.markdown`

Conte&uacute;do do arquivo:

{% highlight ruby %}
---
layout: post
title: "Novo post em Jekyll"
categories: jekyll noticias
---
{% endhighlight %}

Este &eacute; um novo post em Jekyll.

Mais uma vez, basta fazer o commit/push e o post estar&aacute; dispon&iacute;vel em seu blog :)

## Configura&ccedil;&otilde;es gerais

Informa&ccedil;&otilde;es gerais como t&iacute;tulo do blog,
descri&ccedil;&atilde;o e links para seu Github ou Twitter s&atilde;o definidas
atrav&eacute;s do arquivo `_config.yml` presente na ra&iacute;z do diret&oacute;rio.

## Utilizando dom&iacute;nios customizados

Caso voc&ecirc; tenha um dom&iacute;nio pr&oacute;prio e queira utiliz&aacute;-lo
em seu blog, a configura&ccedil;&atilde;o &eacute; bem simples. Na raiz do
diret&oacute;rio, crie um arquivo chamado CNAME e dentro dele inclua uma &uacute;nica\
linha com a URL que deseja utilizar. Fa&ccedil;a um novo commit/push para enviar
a configura&ccedil;&atilde;o para o Github.

Agora basta atualizar suas entradas DNS no servi&ccedil;o que gerencia seu dom&iacute;nio. Voc&ecirc; deve inserir duas entradas do tipo A apontando para 192.30.252.153 e 192.30.252.154 e uma entrada do tipo CNAME apontando seu dom&iacute;nio com www para a URL gerada pelo Github. No caso do exemplo abaixo, as configura&ccedil;&otilde;es foram feitas diretamente no Registro.br.
<a href="/assets/2015/12/Screen-Shot-2015-12-05-at-18.29.09.png"><img class="size-full wp-image-153" src="/assets/2015/12/Screen-Shot-2015-12-05-at-18.29.09.png" alt="Apontamento feito no Registro.br para minha Github Page" width="757" height="360" /> Apontamento feito no Registro.br para minha Github Page</a>

## Conclus&atilde;o

De forma bem simples e r&aacute;pida temos um blog no ar utilizando o &oacute;timo servi&ccedil;o gratuito oferecido pelo Github Pages. &Eacute; claro que voc&ecirc; vai querer customizar seu blog e a <a href="http://jekyllrb.com/docs/home/" target="_blank">documenta&ccedil;&atilde;o do Jekyll</a> vai te ajudar bastante.
&Eacute; isso!

Bons posts para voc&ecirc;s!

E compartilhem por aqui os blogs que criarem :)
