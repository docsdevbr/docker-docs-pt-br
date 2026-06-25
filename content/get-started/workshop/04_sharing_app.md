---
# SPDX-FileCopyrightText: 2013-2026 Docker Inc.
# Docker and the Docker logo are trademarks or registered trademarks of Docker,
# Inc. in the United States and/or other countries.
# Docker, Inc. and other parties may also have trademark rights in other terms
# used herein.
#
# SPDX-License-Identifier: Apache-2.0
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/docker-doc-pt-br/blob/-/LICENSES/Apache-2.0.txt

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/04_sharing_app.md
source_revision: 934faffc71240d2141d2d5c8d3b2b10420615729
translation_status: ready

title: Compartilhe a aplicação
weight: 40
linkTitle: "Parte 3: compartilhe a aplicação"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop, docker hub, compartilhamento
description: >-
  Compartilhe a imagem que você criou para a sua aplicação de exemplo para que
  você possa executá-la em outro lugar e outras pessoas desenvolvedoras possam
  usá-la.
aliases:
  - /get-started/part3/
  - /get-started/04_sharing_app/
  - /guides/workshop/04_sharing_app/
---

Agora que você criou uma imagem, pode compartilhá-la.
Para compartilhar imagens Docker, você precisa usar um registro Docker.
O registro padrão é o Docker Hub, de onde vieram todas as imagens que você usou.

> **ID Docker**
>
> Um ID Docker permite que você acesse o Docker Hub, que é a maior biblioteca e
> comunidade do mundo para imagens de contêineres.
> Crie um [ID Docker](https://hub.docker.com/signup) gratuitamente, caso ainda
> não tenha um.

## Crie um repositório

Para enviar uma imagem, primeiro você precisa criar um repositório no Docker
Hub.

1. [Cadastre-se](https://www.docker.com/pricing?ref=Docs&refAction=DocsSharingApp)
   ou faça login no [Docker Hub](https://hub.docker.com).

2. Selecione o botão **Create Repository**.

3. Para o nome do repositório, use `getting-started`.
   Certifique-se de que a **Visibility** esteja definida como **Public**.

4. Selecione **Create**.

Na imagem a seguir, você pode ver um exemplo de comando Docker do Docker Hub.
Este comando enviará a imagem para este repositório.

![Exemplo do comando Docker com push](images/push-command.webp)

## Envie a imagem

Vamos tentar enviar a imagem para o Docker Hub.

1. Na linha de comando, execute o seguinte comando:

   ```console
   docker push docker/getting-started
   ```

   Você verá um erro como este:

   ```console
   $ docker push docker/getting-started
   The push refers to repository [docker.io/docker/getting-started]
   An image does not exist locally with the tag: docker/getting-started
   ```

   Essa falha é esperada porque ainda não foi criada uma tag para a imagem
   corretamente.
   O Docker está procurando por uma imagem com o nome `docker/getting-started`,
   mas sua imagem local continua nomeada como `getting-started`.

   Você pode confirmar isso executando:

   ```console
   docker image ls
   ```

2. Para corrigir isso, primeiro faça o login no Docker Hub usando seu ID Docker:
   `docker login -u SEU-NOME-DE-USUÁRIO`.
3. Use o comando `docker tag` para dar um novo nome à imagem `getting-started`.
   Substitua `SEU-NOME-DE-USUÁRIO` pelo seu ID Docker.

   ```console
   $ docker tag getting-started SEU-NOME-DE-USUÁRIO/getting-started
   ```

4. Agora, execute o comando `docker push` novamente.
   Se você estiver copiando o valor do Docker Hub, pode omitir a parte
   `tagname`, pois você não adicionou uma tag ao nome da imagem.
   Se você não especificar uma tag, o Docker usa uma tag chamada `latest`.

   ```console
   $ docker push SEU-NOME-DE-USUÁRIO/getting-started
   ```

## Execute a imagem em uma nova instância

Agora que sua imagem foi criada e enviada para um registro, você pode executar
sua aplicação em qualquer máquina que tenha o Docker instalado.
Experimente baixar e executar sua imagem em outro computador ou em uma instância
na nuvem.

## Resumo

Nesta seção, você aprendeu como compartilhar suas imagens enviando-as para um
registro.
Em seguida, você acessou uma nova instância e conseguiu executar a imagem
recém-enviada.
Isso é bastante comum em pipelines de CI, onde a pipeline cria a imagem e a
envia para um registro, e então o ambiente de produção pode usar a versão mais
recente da imagem.

Informações relacionadas:
  - [Referência da CLI do Docker](/reference/cli/docker/)
  - [Imagens multiplataforma](/manuals/build/building/multi-platform.md)
  - [Visão geral do Docker Hub](/manuals/docker-hub/_index.md)

## Next steps

In the next section, you'll learn how to persist data in your containerized application.

{{< button text="Persist the DB" url="05_persisting_data.md" >}}
