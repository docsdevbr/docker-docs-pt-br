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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/building-images/understanding-image-layers.md
source_revision: f482b78ec94de3c39bc6371aa8210f559432d12a
translation_status: ready

title: Entendendo as camadas de imagem
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará sobre as camadas de imagem de contêiner.
summary: >-
  Você já se perguntou como as imagens funcionam?
  Este guia ajudará você a entender as camadas de imagem - os blocos de
  construção fundamentais das imagens de contêiner.
  Você obterá uma compreensão abrangente de como as camadas são criadas,
  empilhadas e usadas para garantir contêineres eficientes e otimizados.
weight: 1
aliases:
  - /guides/docker-concepts/building-images/understanding-image-layers/
---

{{< youtube-embed wJwqtAkmtQA >}}

## Explicação

Como você aprendeu em
[O que é uma imagem?](../the-basics/what-is-an-image/),
as imagens de contêiner são compostas de camadas.
E cada uma dessas camadas, uma vez criada, é imutável.
Mas o que isso realmente significa?
E como essas camadas são usadas para criar o sistema de arquivos que um
contêiner pode usar?

### Camadas de imagem

Cada camada em uma imagem contém um conjunto de alterações no sistema de
arquivos - adições, exclusões ou modificações.
Vejamos uma imagem teórica:

1. A primeira camada adiciona comandos básicos e um gerenciador de pacotes, como
   o apt.
2. A segunda camada instala um tempo de execução Python e o pip para
   gerenciamento de dependências.
3. A terceira camada copia o arquivo `requirements.txt` específico de uma
   aplicação.
4. A quarta camada instala as dependências específicas dessa aplicação.
5. A quinta camada copia o código-fonte da aplicação.

Este exemplo pode ser semelhante a este:

![Captura de tela do fluxograma mostrando o conceito das camadas da imagem](images/container_image_layers.webp?border=true)

Isso é vantajoso porque permite que camadas sejam reutilizadas entre imagens.
Por exemplo, imagine que você queira criar outra aplicação Python.
Graças ao empilhamento em camadas, você pode aproveitar a mesma base Python.
Isso tornará as construções mais rápidas e reduzirá a quantidade de
armazenamento e largura de banda necessária para distribuir as imagens.
O empilhamento da imagem em camadas pode ser semelhante ao seguinte:

![Captura de tela do fluxograma mostrando os benefícios do empilhamento das imagens em camadas](images/container_image_layer_reuse.webp?border=true)

As camadas permitem estender imagens de outras pessoas reutilizando suas camadas
base, permitindo que você adicione apenas os dados que sua aplicação precisa.

### Empilhando as camadas

O empilhamento em camadas é possível graças ao armazenamento endereçável por
conteúdo e aos sistemas de arquivos de união.
Embora isso envolva alguns detalhes técnicos, veja como funciona:

1. Após o download de cada camada, ela é extraída para seu próprio diretório no
   sistema de arquivos do host.
2. Ao executar um contêiner a partir de uma imagem, um sistema de arquivos de
   união é criado, onde as camadas são empilhadas umas sobre as outras, criando
   uma nova visualização unificada.
3. Quando o contêiner é iniciado, seu diretório raiz é definido como o local
   desse diretório unificado, usando `chroot`.

Quando o sistema de arquivos de união é criado, além das camadas de imagem, um
diretório é criado especificamente para o contêiner em execução.
Isso permite que o contêiner faça alterações no sistema de arquivos, mantendo as
camadas de imagem originais intactas.
Isso permite que você execute vários contêineres a partir da mesma imagem
subjacente.

## Experimente

Neste guia prático, você criará manualmente novas camadas de imagem usando o
comando
[`docker container commit`](https://docs.docker.com/reference/cli/docker/container/commit/).
Observe que você raramente criará imagens dessa forma, pois normalmente
[usará um Dockerfile](./writing-a-dockerfile.md).
No entanto, isso facilita a compreensão de como tudo funciona.

### Crie uma imagem base

Neste primeiro passo, você criará sua própria imagem base, que será usada nos
passos seguintes.

1. [Baixe e instale](https://www.docker.com/products/docker-desktop/) o Docker
   Desktop.

2. Em um terminal, execute o seguinte comando para iniciar um novo contêiner:

   ```console
   $ docker run --name=base-container -ti ubuntu
   ```

   Após o download da imagem e a inicialização do contêiner, você deverá ver um
   novo prompt de shell.
   Ele será executado dentro do seu contêiner.
   Será semelhante ao seguinte (o ID do contêiner pode variar):

   ```console
   root@d8c5ca119fcd:/#
   ```

3. Dentro do contêiner, execute o seguinte comando para instalar o Node.js:

   ```console
   $ apt update && apt install -y nodejs
   ```

   Ao executar este comando, o Node é baixado e instalado dentro do contêiner.
   No contexto do sistema de arquivos de união, essas alterações no sistema de
   arquivos ocorrem dentro do diretório exclusivo desse contêiner.

4. Verifique se o Node está instalado, executando o seguinte comando:

   ```console
   $ node -e 'console.log("Olá, mundo!")'
   ```

   Você deverá ver a mensagem "Olá, mundo!" no console.

5. Agora que o Node está instalado, você pode salvar as alterações feitas como
   uma nova camada de imagem, a partir da qual poderá iniciar novos contêineres
   ou criar novas imagens.
   Para fazer isso, você usará o comando
   [`docker container commit`](https://docs.docker.com/reference/cli/docker/container/commit/).
   Execute o seguinte comando em um novo terminal:

   ```console
   $ docker container commit -m "Adiciona o Node" base-container node-base
   ```

6. Visualize as camadas da sua imagem usando o comando `docker image history`:

   ```console
   $ docker image history node-base
   ```

   Você verá uma saída semelhante à seguinte:

   ```console
   IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
   9e274734bb25   10 seconds ago   /bin/bash                                       157MB     Adiciona o Node
   cd1dba651b30   7 days ago       /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
   <missing>      7 days ago       /bin/sh -c #(nop) ADD file:6089c6bede9eca8ec…   110MB
   <missing>      7 days ago       /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
   <missing>      7 days ago       /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
   <missing>      7 days ago       /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
   <missing>      7 days ago       /bin/sh -c #(nop)  ARG RELEASE                  0B
   ```

   Observe o comentário "Adiciona o Node" na primeira linha.
   Esta camada contém a instalação do Node.js que você acabou de fazer.

7. Para comprovar que sua imagem tem o Node instalado, você pode iniciar um novo
   contêiner usando esta nova imagem:

   ```console
   $ docker run node-base node -e "console.log('Olá novamente')"
   ```

   Com isso, você deve obter a mensagem "Olá novamente" no terminal, mostrando
   que o Node foi instalado e está funcionando.

8. Agora que você terminou de criar sua imagem base, pode remover esse
   contêiner:

   ```console
   $ docker rm -f base-container
   ```

> **Definição da imagem base**
>
> Uma imagem base é a base para a construção de outras imagens.
> É possível usar qualquer imagem como base.
> No entanto, algumas imagens são criadas intencionalmente como blocos de
> construção, fornecendo uma base ou ponto de partida para uma aplicação.
>
> Neste exemplo, você provavelmente não implantará esta imagem `node-base`, pois
> ela ainda não faz nada.
> Mas é uma base que você pode usar para outras construções.

### Construa uma imagem de aplicação

Agora que você tem uma imagem base, pode estendê-la para criar imagens
adicionais.

1. Inicie um novo contêiner usando a imagem `node-base` recém-criada:

   ```console
   $ docker run --name=app-container -ti node-base
   ```

2. Dentro deste contêiner, execute o seguinte comando para criar uma aplicação
   Node:

   ```console
   $ echo 'console.log("Olá de uma aplicação")' > app.js
   ```

   Para executar esta aplicação Node, você pode usar o seguinte comando e ver a
   mensagem impressa na tela:

   ```console
   $ node app.js
   ```

3. Em outro terminal, execute o seguinte comando para salvar as alterações deste
   contêiner como uma nova imagem:

   ```shell
   $ docker container commit -c "CMD node app.js" -m "Adiciona aplicação" app-container sample-app
   ```

   Este comando não apenas cria uma nova imagem chamada `sample-app`, mas também
   adiciona configurações adicionais à imagem para definir o comando padrão ao
   iniciar um contêiner.
   Neste caso, você está configurando o contêiner para executar automaticamente
   `node app.js`.

4. Em um terminal fora do contêiner, execute o seguinte comando para visualizar
   as camadas atualizadas:

   ```console
   $ docker image history sample-app
   ```

   Você verá uma saída semelhante à seguinte.
   Observe que o comentário da camada superior contém "Adiciona aplicação" e o
   da camada seguinte contém "Adiciona o Node":

   ```console
   IMAGE          CREATED              CREATED BY                                      SIZE      COMMENT
   c1502e2ec875   About a minute ago   /bin/bash                                       33B       Adiciona aplicação
   5310da79c50a   4 minutes ago        /bin/bash                                       126MB     Adiciona o Node
   cd1dba651b30   7 days ago           /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
   <missing>      7 days ago           /bin/sh -c #(nop) ADD file:6089c6bede9eca8ec…   110MB
   <missing>      7 days ago           /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
   <missing>      7 days ago           /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
   <missing>      7 days ago           /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
   <missing>      7 days ago           /bin/sh -c #(nop)  ARG RELEASE                  0B
   ```

5. Por fim, inicie um novo contêiner usando a nova imagem.
   Como você especificou o comando padrão, pode usar o seguinte comando:

   ```console
   $ docker run sample-app
   ```

   Você deverá ver sua mensagem de boas-vindas aparecer no terminal, vinda da
   sua aplicação Node.

6. Agora que você terminou de usar seus contêineres, pode removê-los usando o
   seguinte comando:

   ```console
   $ docker rm -f app-container
   ```

## Recursos adicionais

Se você quiser se aprofundar mais no que aprendeu, confira os seguintes
recursos:

* [`docker image history`](/reference/cli/docker/image/history/)
* [`docker container commit`](/reference/cli/docker/container/commit/)

## Próximos passos

Como mencionado anteriormente, a maioria das construções de imagem não usa
`docker container commit`.
Em vez disso, você usará um Dockerfile que automatiza essas etapas para você.

{{< button text="Escrevendo um Dockerfile" url="writing-a-dockerfile" >}}
