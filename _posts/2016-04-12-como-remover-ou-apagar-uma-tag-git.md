---
layout: post
title: Como remover uma tag de um repositório Git
author:
  display_name: Alan Batista
---

Eu não costumo apagar tags dos repositórios que trabalho com frequência, mas
sempre que preciso, acabo pedindo ajuda do grande mestre
[Google](https://www.google.com.br/webhp?#q=how+to+delete+a+tag+from+git){:target='blank'}.

Uma tag (ou marca) serve para identificar uma versão especifica do seu código de
forma permanente.

## Adicionando uma tag

Para criar uma **tag**, execute no seu repositório:

```git
git tag 1.0.9
```

Onde _1.0.9_ é o nome da tag que você deseja marcar.

Esse comando cria uma tag localmente, para enviar para o seu repositório remoto
faça:

```git
git push origin master --tags
```

Esse comando, além do _push_ que a gente já conhece, faz também o envio das tags
para o seu repositório remoto.

## Removendo uma tag

### Localmente

Bem, porém, por N motivos você pode querer apagar esse tag, e para fazer isso
localmente o comando é bem intuitivo:

```git
git tag -d 1.0.9
```

### No repositório remoto

Mas para apagar a tag que está no servidor remoto é um pouco diferente:

```git
git push origin :refs/tags/1.0.9
```

Para apagar um tag você precisa indicar qual o caminho da tag. Explicando um pouco mais dos _internals_ do Git, cada tag é armazenada como um arquivo onde o nome desse arquivo é a tag e seu conteúdo é a _hash_ do commit que a tag referencia. Estes arquivos ficam na pasta `.git` do seu repositório, dentro da pasta `refs/tags/`.

Espero que com a explicação você não precisa recorrer ao Google para encontrar a
resposta para essa dúvida tão "remota".
