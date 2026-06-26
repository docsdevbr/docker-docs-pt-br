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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/building-images/build-tag-and-publish-an-image.md
source_revision: c5b3cb60ad63dca4d6fc7cf19c001ba893b0c6b2
translation_status: ready

title: Construa, adicione tags e publique uma imagem
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará você a construir, adicionar tags e publicar
  uma imagem no Docker Hub ou em qualquer outro registro.
summary: >-
  Construir, adicionar tags e publicar imagens Docker são etapas essenciais no
  fluxo de trabalho de conteinerização.
  Neste guia, você aprenderá como construir imagens Docker, como adicionar tags
  com um identificador exclusivo e como publicar sua imagem em um registro
  público.
weight: 3
aliases:
  - /guides/docker-concepts/building-images/build-tag-and-publish-an-image/
---

{{< youtube-embed chiiGLlYRlY >}}

## Explicação

Neste guia, você aprenderá o seguinte:

- Criação de imagens - o processo de construção de uma imagem baseada em um
  `Dockerfile`.
- Criação de tags de imagens - o processo de atribuir um nome a uma imagem, o
  que também determina onde a imagem pode ser distribuída.
- Publicação de imagens - o processo de distribuir ou compartilhar a imagem
  recém-criada usando um registro de contêineres.

### Criando imagens

Na maioria das vezes, as imagens são construídas usando um Dockerfile.
O comando `docker build` mais básico pode ser semelhante ao seguinte:

```bash
docker build .
```

O ponto final (`.`) no comando fornece o caminho ou URL para o
[contexto de construção](https://docs.docker.com/build/concepts/context/#what-is-a-build-context).
Nesse local, o construtor encontrará o arquivo `Dockerfile` e outros arquivos
referenciados.

Ao executar uma construção, o construtor baixa a imagem base, se necessário, e
então executa as instruções especificadas no Dockerfile.

Com o comando anterior, a imagem não terá nome, mas a saída fornecerá o ID da
imagem.
Por exemplo, o comando anterior poderia produzir a seguinte saída:

```console
$ docker build .
[+] Building 3.5s (11/11) FINISHED                                              docker:desktop-linux
 => [internal] load build definition from Dockerfile                                            0.0s
 => => transferring dockerfile: 308B                                                            0.0s
 => [internal] load metadata for docker.io/library/python:3.12                                  0.0s
 => [internal] load .dockerignore                                                               0.0s
 => => transferring context: 2B                                                                 0.0s
 => [1/6] FROM docker.io/library/python:3.12                                                    0.0s
 => [internal] load build context                                                               0.0s
 => => transferring context: 123B                                                               0.0s
 => [2/6] WORKDIR /usr/local/app                                                                0.0s
 => [3/6] RUN useradd app                                                                       0.1s
 => [4/6] COPY ./requirements.txt ./requirements.txt                                            0.0s
 => [5/6] RUN pip install --no-cache-dir --upgrade -r requirements.txt                          3.2s
 => [6/6] COPY ./app ./app                                                                      0.0s
 => exporting to image                                                                          0.1s
 => => exporting layers                                                                         0.1s
 => => writing image sha256:9924dfd9350407b3df01d1a0e1033b1e543523ce7d5d5e2c83a724480ebe8f00    0.0s
```

Com a saída anterior, você poderia iniciar um contêiner usando a imagem
referenciada:

```console
docker run sha256:9924dfd9350407b3df01d1a0e1033b1e543523ce7d5d5e2c83a724480ebe8f00
```

Esse nome certamente não é memorável, e é aí que a criação de tags se torna
útil.

### Criação de tags de imagens

Criar tags de imagens é o método de atribuir um nome memorável a uma imagem.
No entanto, existe uma estrutura para o nome de uma imagem.
O nome completo de uma imagem tem a seguinte estrutura:

```text
[HOST[:PORT_NUMBER]/]PATH[:TAG]
```

- `HOST`: O nome opcional do host do registro onde a imagem está localizada.
  Se nenhum host for especificado, o registro público do Docker em `docker.io`
  será usado por padrão.
- `PORT_NUMBER`: O número da porta do registro, caso um nome de host seja
  fornecido.
- `PATH`: O caminho da imagem, composto por componentes separados por barra.
  Para o Docker Hub, o formato segue `[NAMESPACE/]REPOSITORY`, onde namespace é
  um nome de usuário ou organização.
  Se nenhum namespace for especificado, `library` será usado, que é o namespace
  para Imagens Oficiais do Docker.
- `TAG`: Um identificador personalizado e legível por pessoas, normalmente usado
  para identificar diferentes versões ou variantes de uma imagem.
  Se nenhuma tag for especificada, `latest` será usada por padrão.

Alguns exemplos de nomes de imagens incluem:

- `nginx`, equivalente a `docker.io/library/nginx:latest`: isso obtém uma imagem
  do registro `docker.io`, do namespace `library`, do repositório de imagens
  `nginx` e da tag `latest`.
- `docker/welcome-to-docker`, equivalente a
  `docker.io/docker/welcome-to-docker:latest`: obtém uma imagem do registro
  `docker.io`, do namespace `docker`, do repositório de imagens
  `welcome-to-docker` e da tag `latest`.
- `ghcr.io/dockersamples/example-voting-app-vote:pr-311`: obtém uma imagem do
  GitHub Container Registry, do namespace `dockersamples`, do repositório de
  imagens `example-voting-app-vote` e da tag `pr-311`.

Para adicionar uma tag a uma imagem durante a compilação, use a flag `-t` ou
`--tag`.

```console
docker build -t my-username/my-image .
```

Se você já construiu uma imagem, pode adicionar outra tag à imagem usando o
comando
[`docker image tag`](https://docs.docker.com/engine/reference/commandline/image_tag/):

```console
docker image tag my-username/my-image another-username/another-image:v1
```

### Publicando imagens

Após construir uma imagem e adicionar uma tag, você está pronto para enviá-la
para um registro.
Para isso, use o comando
[`docker push`](https://docs.docker.com/engine/reference/commandline/image_push/):

```console
docker push my-username/my-image
```

Em poucos segundos, todas as camadas da sua imagem serão enviadas para o
registro.

> **Autenticação necessária**
>
> Antes de poder enviar uma imagem para um repositório, você precisará realizar
> a autenticação.
> Para isso, basta usar o comando
> [docker login](https://docs.docker.com/engine/reference/commandline/login/).
{ .information }

## Experimente

Neste guia prático, você criará uma imagem simples usando um Dockerfile
fornecido e a enviará para o Docker Hub.

### Configuração

1. Obtenha a aplicação de exemplo.

   Se você tiver o Git instalado, poderá clonar o repositório da aplicação de
   exemplo.
   Caso contrário, você pode baixar a aplicação de exemplo.
   Escolha uma das seguintes opções.

   {{< tabs >}}
   {{< tab name="Clonar com o git" >}}

   Use o seguinte comando em um terminal para clonar o repositório da aplicação
   de exemplo.

   ```console
   $ git clone https://github.com/docker/getting-started-todo-app
   ```
   {{< /tab >}}
   {{< tab name="Baixar" >}}

   Baixe o código-fonte e extraia-o.

   {{< button url="https://github.com/docker/getting-started-todo-app/raw/cd61f824da7a614a8298db503eed6630eeee33a3/app.zip" text="Baixe o arquivo fonte" >}}

   {{< /tab >}}
   {{< /tabs >}}

2. [Baixe e instale](https://www.docker.com/products/docker-desktop/) o Docker
   Desktop.

3. Se você ainda não tem uma conta Docker,
   [crie uma agora](https://hub.docker.com/).
   Depois disso, faça login no Docker Desktop usando essa conta.

### Construa uma imagem

Agora que você tem um repositório no Docker Hub, é hora de construir uma imagem
e enviá-la para o repositório.

1. Usando um terminal na raiz do repositório da aplicação de exemplo, execute o
   seguinte comando.
   Substitua `SEU_NOME_DE_USUARIO_DOCKER` pelo seu nome de usuário do Docker Hub:

   ```console
   $ docker build -t <SEU_NOME_DE_USUARIO_DOCKER>/concepts-build-image-demo .
   ```

   Por exemplo, se seu nome de usuário for `mobywhale`, você executaria o
   comando:

    ```console
    $ docker build -t mobywhale/concepts-build-image-demo .
    ```

2. Após a conclusão da construção, você pode visualizar a imagem usando o
   seguinte comando:

   ```console
   $ docker image ls
   ```

   O comando produzirá uma saída semelhante à seguinte:

   ```plaintext
   REPOSITORY                             TAG       IMAGE ID       CREATED          SIZE
   mobywhale/concepts-build-image-demo    latest    746c7e06537f   24 seconds ago   354MB
   ```

3. Você pode visualizar o histórico (ou como a imagem foi criada) usando o
   comando [docker image history](/reference/cli/docker/image/history/):

   ```console
   $ docker image history mobywhale/concepts-build-image-demo
   ```

   Em seguida, você verá uma saída semelhante à seguinte:

   ```plaintext
   IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
   f279389d5f01   8 seconds ago   CMD ["node" "./src/index.js"]                   0B        buildkit.dockerfile.v0
   <missing>      8 seconds ago   EXPOSE map[3000/tcp:{}]                         0B        buildkit.dockerfile.v0
   <missing>      8 seconds ago   WORKDIR /app                                    8.19kB    buildkit.dockerfile.v0
   <missing>      4 days ago      /bin/sh -c #(nop)  CMD ["node"]                 0B
   <missing>      4 days ago      /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B
   <missing>      4 days ago      /bin/sh -c #(nop) COPY file:4d192565a7220e13…   20.5kB
   <missing>      4 days ago      /bin/sh -c apk add --no-cache --virtual .bui…   7.92MB
   <missing>      4 days ago      /bin/sh -c #(nop)  ENV YARN_VERSION=1.22.19     0B
   <missing>      4 days ago      /bin/sh -c addgroup -g 1000 node     && addu…   126MB
   <missing>      4 days ago      /bin/sh -c #(nop)  ENV NODE_VERSION=20.12.0     0B
   <missing>      2 months ago    /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
   <missing>      2 months ago    /bin/sh -c #(nop) ADD file:d0764a717d1e9d0af…   8.42MB
   ```

   Esta saída mostra as camadas da imagem, destacando as camadas que você
   adicionou e aquelas que foram herdadas da sua imagem base.

### Envie a imagem

Agora que você criou uma imagem, é hora de enviá-la para um registro.

1. Envie a imagem usando o comando
   [docker push](/reference/cli/docker/image/push/):

   ```console
   $ docker push <SEU_NOME_DE_USUARIO_DOCKER>/concepts-build-image-demo
   ```

   Se você receber a mensagem `requested access to the resource is denied`,
   verifique se você fez o login e se o seu nome de usuário do Docker está
   correto na tag da imagem.

   Após alguns instantes, sua imagem deverá ser enviada para o Docker Hub.

## Recursos adicionais

Para saber mais sobre como construir, criar tags e publicar imagens, visite os
seguintes recursos:

- [O que é um contexto de construção?](/build/concepts/context/#what-is-a-build-context)
- [Referência do docker build](/reference/cli/docker/buildx/build/)
- [Referência do docker image tag](/reference/cli/docker/image/tag/)
- [Referência do docker push](/reference/cli/docker/image/push/)
- [O que é um registro?](/get-started/docker-concepts/the-basics/what-is-a-registry/)

## Próximos passos

Agora que você aprendeu sobre como cosntruir e publicar imagens, é hora de
aprender como acelerar o processo de construção usando o cache de construção do
Docker.

{{< button text="Usando o cache de construção" url="using-the-build-cache" >}}
