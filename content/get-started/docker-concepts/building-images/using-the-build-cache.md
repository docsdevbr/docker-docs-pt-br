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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/building-images/using-the-build-cache.md
source_revision: 19961dd50fa5a23b4d9fabe272eb885b6a5311a9
translation_status: ready

title: Using the build cache
keywords: concepts, build, images, container, docker desktop
description: This concept page will teach you about the build cache, what changes invalidate the cache and how to effectively use the build cache.
summary: |
  Using the build cache effectively allows you to achieve faster builds by
  reusing results from previous builds and skipping unnecessary steps. To
  maximize cache usage and avoid resource-intensive and time-consuming
  rebuilds, it's crucial to understand how cache invalidation works. In this
  guide, you’ll learn how to use the Docker build cache efficiently for
  streamlined Docker image development and continuous integration workflows.
weight: 4
aliases:
 - /guides/docker-concepts/building-images/using-the-build-cache/
---

{{< youtube-embed Ri6jMknjprY >}}

## Explicação

Considere o seguinte Dockerfile que você criou para a aplicação
[getting-started](./writing-a-dockerfile/).

```dockerfile
FROM node:22-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "./src/index.js"]
```

Ao executar o comando `docker build` para criar uma nova imagem, o Docker
executa cada instrução do seu Dockerfile, criando uma camada para cada comando
na ordem especificada.
Para cada instrução, o Docker verifica se pode reutilizá-la de uma construção
anterior.
Se encontrar uma instrução semelhante já executada, o Docker não precisa
refazê-la.
Em vez disso, usa o resultado em cache.
Dessa forma, o processo de construção torna-se mais rápido e eficiente,
economizando tempo e recursos valiosos.

Usar o cache de construção de forma eficaz permite obter construções mais
rápidas, reutilizando resultados de construções anteriores e evitando trabalho
desnecessário.
Para maximizar o uso do cache e evitar reconstruções demoradas que consomem
muitos recursos, é importante entender como funciona a invalidação do cache.
Aqui estão alguns exemplos de situações que podem invalidar o cache:

- Qualquer alteração no comando de uma instrução `RUN` invalida essa camada.
  O Docker detecta a alteração e invalida o cache de construção se houver
  qualquer modificação em um comando `RUN` no seu Dockerfile.

- Qualquer alteração em arquivos copiados para a imagem com as instruções
  `COPY` ou `ADD` também invalida o cache.
  O Docker monitora quaisquer alterações em arquivos dentro do diretório do seu
  projeto.
  Seja uma mudança no conteúdo ou em propriedades como permissões, o Docker
  considera essas modificações como gatilhos para invalidar o cache.

- Uma vez que uma camada é invalidada, todas as camadas subsequentes também são
  invalidadas.
  Se alguma camada anterior, incluindo a imagem base ou camadas intermediárias,
  tiver sido invalidada devido a alterações, o Docker garante que as camadas
  subsequentes que dependem dela também sejam invalidadas.
  Isso mantém o processo de construção sincronizado e evita inconsistências.

Ao escrever ou editar um Dockerfile, fique atento a cache misses desnecessários
para garantir que as construções sejam executadas da forma mais rápida e
eficiente possível.

## Experimente

Neste guia prático, você aprenderá como usar o cache de construção do Docker de
forma eficaz para uma aplicação Node.js.

### Construa a aplicação

1. [Baixe e instale](https://www.docker.com/products/docker-desktop/) o Docker
   Desktop.

2. Abra um terminal e
   [clone esta aplicação de exemplo](https://github.com/dockersamples/todo-list-app).

   ```console
   $ git clone https://github.com/dockersamples/todo-list-app
   ```

3. Navegue até o diretório `todo-list-app`:

   ```console
   $ cd todo-list-app
   ```

   Dentro deste diretório, você encontrará um arquivo chamado `Dockerfile` com o
   seguinte conteúdo:

   ```dockerfile
   FROM node:22-alpine
   WORKDIR /app
   COPY . .
   RUN yarn install --production
   EXPOSE 3000
   CMD ["node", "./src/index.js"]
   ```

4. Execute o seguinte comando para criar a imagem Docker:

   ```console
   $ docker build .
   ```

   Aqui está o resultado do processo de construção:

   ```console
   [+] Building 20.0s (10/10) FINISHED
   ```

   A primeira linha indica que todo o processo de construção levou
   *20,0 segundos*.
   A primeira construção pode demorar um pouco, pois instala as dependências.

5. Reconstrua sem fazer alterações.

   Agora, execute novamente o comando `docker build` sem fazer nenhuma alteração
   no código-fonte ou no Dockerfile, conforme mostrado:

   ```console
   $ docker build .
   ```

   As construções subsequentes à inicial são mais rápidas devido ao mecanismo de
   cache, desde que os comandos e o contexto permaneçam inalterados.
   O Docker armazena em cache as camadas intermediárias geradas durante o
   processo de construção.
   Ao reconstruir a imagem sem fazer alterações no Dockerfile ou no
   código-fonte, o Docker pode reutilizar as camadas em cache, acelerando
   significativamente o processo de construção.

   ```console
   [+] Building 1.0s (9/9) FINISHED                                                                            docker:desktop-linux
    => [internal] load build definition from Dockerfile                                                                        0.0s
    => => transferring dockerfile: 187B                                                                                        0.0s
    ...
    => [internal] load build context                                                                                           0.0s
    => => transferring context: 8.16kB                                                                                         0.0s
    => CACHED [2/4] WORKDIR /app                                                                                               0.0s
    => CACHED [3/4] COPY . .                                                                                                   0.0s
    => CACHED [4/4] RUN yarn install --production                                                                              0.0s
    => exporting to image                                                                                                      0.0s
    => => exporting layers                                                                                                     0.0s
    => => exporting manifest
   ```

   A construção subsequente foi concluída em apenas 1,0 segundo, aproveitando as
   camadas em cache.
   Não há necessidade de repetir etapas demoradas, como a instalação de
   dependências.

   <table>
     <thead>
       <tr>
         <th>Passos
         </th>
         <th>Descrição
         </th>
         <th>Tempo gasto (1ª execução)
         </th>
         <th>Tempo gasto (2ª execução)
         </th>
       </tr>
     </thead>
     <tbody>
     <tr>
      <td>1
      </td>
      <td><code>Load build definition from Dockerfile</code>
      </td>
      <td>0,0 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>2
      </td>
      <td><code>Load metadata for docker.io/library/node:22-alpine</code>
      </td>
      <td>2,7 segundos
      </td>
      <td>0,9 segundos
      </td>
     </tr>
     <tr>
      <td>3
      </td>
      <td><code>Load .dockerignore</code>
      </td>
      <td>0,0 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>4
      </td>
      <td><code>Load build context</code>
   <p>
   (Context size: 4.60MB)
      </td>
      <td>0,1 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>5
      </td>
      <td><code>Set the working directory (WORKDIR)</code>
      </td>
      <td>0,1 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>6
      </td>
      <td><code>Copy the local code into the container</code>
      </td>
      <td>0,0 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>7
      </td>
      <td><code>Run yarn install --production</code>
      </td>
      <td>10,0 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>8
      </td>
      <td><code>Exporting layers</code>
      </td>
      <td>2,2 segundos
      </td>
      <td>0,0 segundos
      </td>
     </tr>
     <tr>
      <td>9
      </td>
      <td><code>Exporting the final image</code>
      </td>
      <td>3,0 segundos
      </td>
      <td>0,0 segundos
      </td>
    </tr>
    </tbody>
   </table>

   Voltando à saída do `docker image history`, você verá que cada comando no
   Dockerfile se torna uma nova camada na imagem.
   Você deve se lembrar que, ao fazer uma alteração na imagem, as dependências
   do Yarn precisavam ser reinstaladas.
   Existe alguma maneira de corrigir isso?
   Não faz muito sentido reinstalar as mesmas dependências a cada build, certo?

   Para corrigir isso, reestruture seu Dockerfile para que o cache de
   dependências permaneça válido, a menos que realmente precise ser invalidado.
   Para aplicações baseadas em Node.js, as dependências são definidas no arquivo
   `package.json`.
   Você precisará reinstalar as dependências se esse arquivo for alterado, mas
   usar as dependências em cache se o arquivo permanecer inalterado.
   Portanto, comece copiando apenas esse arquivo, depois instale as dependências
   e, por fim, copie o resto.
   Assim, você só precisará recriar as dependências do Yarn se houver alguma
   alteração no arquivo `package.json`.

6. Atualize o Dockerfile para copiar primeiro o arquivo `package.json`, instalar
   as dependências e, em seguida, copiar o resto.

   ```dockerfile
   FROM node:22-alpine
   WORKDIR /app
   COPY package.json yarn.lock ./
   RUN yarn install --production
   COPY . .
   EXPOSE 3000
   CMD ["node", "src/index.js"]
   ```

7. Crie um arquivo chamado `.dockerignore` na mesma pasta do Dockerfile com o
   seguinte conteúdo.

   ```plaintext
   node_modules
   ```

8. Construa a nova imagem:

   ```console
   $ docker build .
   ```

   Em seguida, você verá uma saída semelhante à seguinte:

   ```console
   [+] Building 16.1s (10/10) FINISHED
   => [internal] load build definition from Dockerfile                                               0.0s
   => => transferring dockerfile: 175B                                                               0.0s
   => [internal] load .dockerignore                                                                  0.0s
   => => transferring context: 2B                                                                    0.0s
   => [internal] load metadata for docker.io/library/node:22-alpine                                  0.0s
   => [internal] load build context                                                                  0.8s
   => => transferring context: 53.37MB                                                               0.8s
   => [1/5] FROM docker.io/library/node:22-alpine                                                    0.0s
   => CACHED [2/5] WORKDIR /app                                                                      0.0s
   => [3/5] COPY package.json yarn.lock ./                                                           0.2s
   => [4/5] RUN yarn install --production                                                           14.0s
   => [5/5] COPY . .                                                                                 0.5s
   => exporting to image                                                                             0.6s
   => => exporting layers                                                                            0.6s
   => => writing image
   sha256:d6f819013566c54c50124ed94d5e66c452325327217f4f04399b45f94e37d25        0.0s
   => => naming to docker.io/library/node-app:2.0                                                 0.0s
   ```

   Você verá que todas as camadas foram reconstruídas.
   Perfeitamente normal, já que você alterou bastante o Dockerfile.

9. Agora, faça uma alteração no arquivo `src/static/index.html` (como mudar o
   título para "A incrível aplicação de tarefas").

10. Construa a imagem Docker.
    Desta vez, a saída deverá ser um pouco diferente.

    ```console
    $ docker build -t node-app:3.0 .
    ```

    Em seguida, você verá uma saída semelhante à seguinte:

    ```console
    [+] Building 1.2s (10/10) FINISHED
    => [internal] load build definition from Dockerfile                                               0.0s
    => => transferring dockerfile: 37B                                                                0.0s
    => [internal] load .dockerignore                                                                  0.0s
    => => transferring context: 2B                                                                    0.0s
    => [internal] load metadata for docker.io/library/node:22-alpine                                  0.0s
    => [internal] load build context                                                                  0.2s
    => => transferring context: 450.43kB                                                              0.2s
    => [1/5] FROM docker.io/library/node:22-alpine                                                    0.0s
    => CACHED [2/5] WORKDIR /app                                                                      0.0s
    => CACHED [3/5] COPY package.json yarn.lock ./                                                    0.0s
    => CACHED [4/5] RUN yarn install --production                                                     0.0s
    => [5/5] COPY . .                                                                                 0.5s
    => exporting to image                                                                             0.3s
    => => exporting layers                                                                            0.3s
    => => writing image
    sha256:91790c87bcb096a83c2bd4eb512bc8b134c757cda0bdee4038187f98148e2eda       0.0s
    => => naming to docker.io/library/node-app:3.0                                                 0.0s
    ```

    Primeiramente, você notará que a construção foi muito mais rápida.
    Você verá que várias etapas estão usando camadas previamente armazenadas em
    cache.
    Isso é uma ótima notícia; você está usando o cache de construção.
    O envio e o download desta imagem e suas atualizações também serão muito
    mais rápidos.

Ao seguir essas técnicas de otimização, você pode tornar suas construções do
Docker mais rápidas e eficientes, resultando em ciclos de iteração mais curtos e
maior produtividade no desenvolvimento.

## Recursos adicionais

* [Otimizando construções com gerenciamento de cache](/build/cache/)
* [Backend de armazenamento de cache](/build/cache/backends/)
* [Invalidação do cache de construção](/build/cache/invalidation/)

## Próximos passos

Agora que você entende como usar o cache de construção do Docker de forma
eficaz, pode aprender sobre construções em várias etapas.

{{< button text="Construções em várias etapas" url="multi-stage-builds" >}}
