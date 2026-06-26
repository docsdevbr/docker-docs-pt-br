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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/09_image_best.md
source_revision: ca0e85cf12f7a517cb633f75e414c2a4169dd5dd
translation_status: ready

title: Boas práticas para a construção de imagens
weight: 90
linkTitle: "Parte 8: boas práticas para a construção de imagens"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop
description: Dicas para construir imagens para sua aplicação.
aliases:
  - /get-started/09_image_best/
  - /guides/workshop/09_image_best/
---

## Camadas de imagem

Usando o comando `docker image history`, você pode visualizar o comando usado
para criar cada camada de uma imagem.

1. Use o comando `docker image history` para visualizar as camadas da imagem
   `getting-started` que você criou.

   ```console
   $ docker image history getting-started
   ```

   Você deve obter uma saída semelhante à seguinte.

   ```plaintext
   IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
   a78a40cbf866        18 seconds ago      /bin/sh -c #(nop)  CMD ["node" "src/index.j…    0B
   f1d1808565d6        19 seconds ago      /bin/sh -c npm install --omit=dev               85.4MB
   a2c054d14948        36 seconds ago      /bin/sh -c #(nop) COPY dir:5dc710ad87c789593…   198kB
   9577ae713121        37 seconds ago      /bin/sh -c #(nop) WORKDIR /app                  0B
   b95baba1cfdb        13 days ago         /bin/sh -c #(nop)  CMD ["node"]                 0B
   <missing>           13 days ago         /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B
   <missing>           13 days ago         /bin/sh -c #(nop) COPY file:238737301d473041…   116B
   <missing>           13 days ago         /bin/sh -c apk add --no-cache --virtual .bui…   5.35MB
   <missing>           13 days ago         /bin/sh -c addgroup -g 1000 node     && addu…   74.3MB
   <missing>           13 days ago         /bin/sh -c #(nop)  ENV NODE_VERSION=12.14.1     0B
   <missing>           13 days ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B
   <missing>           13 days ago         /bin/sh -c #(nop) ADD file:e69d441d729412d24…   5.59MB
   ```

   Cada uma das linhas representa uma camada da imagem.
   A exibição aqui mostra a base na parte inferior, com a camada mais recente no
   topo.
   Com isso, você também pode visualizar rapidamente o tamanho de cada camada, o
   que ajuda a diagnosticar imagens grandes.

2. Você notará que várias das linhas estão truncadas.
   Se você adicionar a flag `--no-trunc`, obterá a saída completa.

   ```console
   $ docker image history --no-trunc getting-started
   ```

## Cache de camadas

Agora que você viu o sistema de camadas em ação, há uma lição importante para
reduzir os tempos de construção das suas imagens de contêiner.
Quando uma camada é alterada, todas as camadas subsequentes também precisam ser
recriadas.

Observe o Dockerfile a seguir, que você criou para a aplicação
`getting-started`.

```dockerfile
# syntax=docker/dockerfile:1
FROM node:24-alpine
WORKDIR /app
COPY . .
RUN npm install --omit=dev
CMD ["node", "src/index.js"]
EXPOSE 3000
```

Voltando à saída do histórico da imagem, você pode ver que cada comando no
Dockerfile se torna uma nova camada na imagem.
Talvez você se lembre de que, ao fazer uma alteração na imagem, as dependências
precisavam ser reinstaladas.
Não faz muito sentido transportar as mesmas dependências toda vez que você
realiza uma construção.

Para resolver isso, você precisa reestruturar seu Dockerfile de modo a favorecer
o cache das dependências.
No caso de aplicações baseadas em Node, essas dependências são definidas no
arquivo `package.json`.
Você pode copiar apenas esse arquivo primeiro, instalar as dependências e, em
seguida, copiar o restante.
Assim, as dependências só serão recriadas se houver alguma alteração no
`package.json`.

1. Atualize o Dockerfile para copiar o `package.json` primeiro, instalar as
   dependências e, depois, copiar o restante.

   ```dockerfile
   # syntax=docker/dockerfile:1
   FROM node:24-alpine
   WORKDIR /app
   COPY package.json package-lock.json ./
   RUN npm install --omit=dev
   COPY . .
   CMD ["node", "src/index.js"]
   ```

2. Construa uma nova imagem usando `docker build`.

   ```console
   $ docker build -t getting-started .
   ```

   Você deve ver uma saída como a seguinte.

   ```plaintext
   [+] Building 16.1s (10/10) FINISHED
   => [internal] load build definition from Dockerfile
   => => transferring dockerfile: 175B
   => [internal] load .dockerignore
   => => transferring context: 2B
   => [internal] load metadata for docker.io/library/node:24-alpine
   => [internal] load build context
   => => transferring context: 53.37MB
   => [1/5] FROM docker.io/library/node:24-alpine
   => CACHED [2/5] WORKDIR /app
   => [3/5] COPY package.json package-lock.json ./
   => [4/5] RUN npm install --omit=dev
   => [5/5] COPY . .
   => exporting to image
   => => exporting layers
   => => writing image     sha256:d6f819013566c54c50124ed94d5e66c452325327217f4f04399b45f94e37d25
   => => naming to docker.io/library/getting-started
   ```

3. Agora, faça uma alteração no arquivo `src/static/index.html`.
   Por exemplo, altere o `<title>` para "A Incrível Aplicação de Tarefas".

4. Crie a imagem Docker agora usando `docker build -t getting-started .`
   novamente.
   Desta vez, a saída deve parecer um pouco diferente.

   ```plaintext
   [+] Building 1.2s (10/10) FINISHED
   => [internal] load build definition from Dockerfile
   => => transferring dockerfile: 37B
   => [internal] load .dockerignore
   => => transferring context: 2B
   => [internal] load metadata for docker.io/library/node:24-alpine
   => [internal] load build context
   => => transferring context: 450.43kB
   => [1/5] FROM docker.io/library/node:24-alpine
   => CACHED [2/5] WORKDIR /app
   => CACHED [3/5] COPY package.json package-lock.json ./
   => CACHED [4/5] RUN npm install
   => [5/5] COPY . .
   => exporting to image
   => => exporting layers
   => => writing image     sha256:91790c87bcb096a83c2bd4eb512bc8b134c757cda0bdee4038187f98148e2eda
   => => naming to docker.io/library/getting-started
   ```

   Primeiramente, você deve notar que o processo de construção foi muito mais
   rápido.
   Além disso, você verá que várias etapas usam camadas armazenadas em cache
   anteriormente.
   O envio e o download desta imagem e de suas atualizações também serão muito
   mais rápidos.

## Construções em vários estágios

As construções em vários estágios são uma ferramenta incrivelmente poderosa
que permite usar várias etapas para criar uma imagem.
Eles oferecem diversas vantagens:

- Separar dependências de tempo de construção de dependências de tempo de
  execução.
- Reduzir o tamanho total da imagem, incluindo apenas o que sua aplicação
  precisa para rodar.

### Exemplo com Maven/Tomcat

Ao criar aplicações baseadas em Java, você precisa de um JDK para compilar o
código-fonte em bytecode Java.
No entanto, esse JDK não é necessário em produção.
Além disso, você pode estar usando ferramentas como Maven ou Gradle para ajudar
na construção da aplicação.
Elas também não são necessárias na sua imagem final.
As construções em vários estágios ajudam nesse cenário.

```dockerfile
# syntax=docker/dockerfile:1
FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package

FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps
```

Neste exemplo, você usa um estágio (chamado `build`) para realizar a compilação
propriamente dita do Java usando o Maven.
No segundo estágio (que começa em `FROM tomcat`), você copia arquivos do estágio
`build`.
A imagem final consiste apenas no último estágio criado, embora isso possa ser
alterado usando a flag `--target`.

### Exemplo com React

Ao criar aplicações React, você precisa de um ambiente Node para compilar o
código JS (tipicamente JSX), folhas de estilo SASS, entre outros, em arquivos
estáticos de HTML, JS e CSS.
Se você não estiver usando renderização no lado do servidor, não precisará de um
ambiente Node para a construção de produção.
Você pode disponibilizar os recursos estáticos em um contêiner Nginx configurado
para conteúdo estático.

```dockerfile
# syntax=docker/dockerfile:1
FROM node:24-alpine AS build
WORKDIR /app
COPY package* ./
RUN npm install
COPY public ./public
COPY src ./src
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

O exemplo de Dockerfile anterior usa a imagem `node:24-alpine` para realizar a
construção (maximizando o cache de camadas) e, em seguida, copia o resultado
para um contêiner nginx.

> [!TIP]
>
> Este exemplo em React serve apenas para fins ilustrativos.
> A aplicação de lista de tarefas do guia de introdução é uma aplicação de
> back-end em `Node.js`, e não um front-end em React.

## Resumo

Nesta seção, você conheceu algumas das melhores práticas para a construção de
imagens, incluindo o cache de camadas e construções em vários estágios.

Informações relacionadas:
- [Referência do Dockerfile](/reference/dockerfile/)
- [Boas práticas para o Dockerfile](/manuals/build/building/best-practices.md)

## Próximos passos

Na próxima seção, você conhecerá recursos adicionais que pode usar para
continuar aprendendo sobre contêineres.

{{< button text="Próximos passos" url="10_what_next.md" >}}
