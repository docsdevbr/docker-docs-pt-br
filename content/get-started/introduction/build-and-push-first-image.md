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

source_url: https://github.com/docker/docs/blob/main/content/get-started/introduction/build-and-push-first-image.md
source_revision: aa3af370e7a38eaab6d9d7359cd6beab78fdc314
translation_status: ready

title: Crie e publique sua primeira imagem
keywords: conceitos, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará você a criar e enviar sua primeira imagem.
summary: >-
  Aprenda a criar sua primeira imagem Docker, uma etapa fundamental na
  conteinerização da sua aplicação.
  Vamos guiar você pelo processo de criação de um repositório de imagens e pela
  construção e envio da sua imagem para o Docker Hub.
  Isso permitirá que você compartilhe sua imagem facilmente com seu time.
weight: 3
aliases:
 - /guides/getting-started/build-and-push-first-image/
---

{{< youtube-embed 7ge1s5nAa34 >}}

## Explicação

Agora que você atualizou a
[aplicação de lista de tarefas](develop-with-containers.md),
já pode criar uma imagem de contêiner para a aplicação e compartilhá-la no
Docker Hub.
Para fazer isso, você precisará fazer o seguinte:

1. Fazer o login com sua conta do Docker.
2. Criar um repositório de imagens no Docker Hub.
3. Criar a imagem do contêiner.
4. Enviar a imagem para o Docker Hub.

Antes de mergulhar no guia prático, a seguir estão alguns conceitos básicos que
você deve conhecer.

### Imagens de contêiner

Se você é iniciante em imagens de contêiner, pense nelas como um pacote
padronizado que contém tudo o que é necessário para executar uma aplicação,
incluindo seus arquivos, configuração e dependências.
Esses pacotes podem ser distribuídos e compartilhados com outras pessoas.

### Docker Hub

Para compartilhar suas imagens Docker, você precisa de um local para
armazená-las.
É aqui que entram os registros.
Embora existam muitos registros, o Docker Hub é o registro padrão e mais usado
para imagens.
O Docker Hub oferece um lugar para você armazenar suas próprias imagens e
encontrar imagens de outras pessoas para executar ou usar como base para suas
próprias imagens.

Ao escolher imagens base, o Docker Hub oferece duas categorias de imagens
confiáveis e mantidas pela equipe do Docker:

- [Imagens Docker Oficiais (DOI)](/manuals/docker-hub/image-library/trusted-content.md#docker-official-images)
  \- Imagens selecionadas de softwares populares, seguindo as melhores práticas
  e atualizadas regularmente.
- [Imagens Docker Reforçadas (DHI)](/manuals/dhi/_index.md) - Imagens mínimas,
  seguras e prontas para produção, com quase zero CVEs, projetadas para reduzir
  a superfície de ataque e simplificar a conformidade.
  As imagens DHI são gratuitas e de código aberto sob a licença Apache 2.0.

Em [Desenvolva com contêineres](develop-with-containers.md), você usou as
seguintes imagens provenientes do Docker Hub, as quais são
[Imagens Docker Oficiais](/manuals/docker-hub/image-library/trusted-content.md#docker-official-images):

- [node](https://hub.docker.com/_/node) - Fornece um ambiente Node e é usada
  como base para seu trabalho de desenvolvimento.
  Esta imagem também é usada como base para a imagem final da aplicação.
- [mysql](https://hub.docker.com/_/mysql) - Fornece um banco de dados MySQL para
  armazenar os itens da lista de tarefas.
- [phpmyadmin](https://hub.docker.com/_/phpmyadmin) - Fornece o phpMyAdmin, uma
  interface web para o banco de dados MySQL.
- [traefik](https://hub.docker.com/_/traefik) - Fornece o Traefik, um moderno
  proxy reverso HTTP e balanceador de carga que direciona as requisições para o
  contêiner apropriado com base em regras de roteamento.

Explore o catálogo completo de conteúdo confiável no Docker Hub:

- [Imagens Docker Oficiais](https://hub.docker.com/search?badges=official) -
  Imagens selecionadas de softwares populares.
- [Imagens Docker Reforçadas](https://hub.docker.com/hardened-images/catalog) -
  Imagens de produção mínimas e com segurança reforçada (também disponíveis
  em [dhi.io](https://dhi.io)).
- [Editores Verificados pelo Docker](https://hub.docker.com/search?badges=verified_publisher)
  \- Imagens de fornecedores de software verificados.
- [Software de Código Aberto Patrocinado pelo Docker](https://hub.docker.com/search?badges=open_source)
  \- Imagens de projetos de código aberto patrocinados pelo Docker.

## Experimente

Neste guia prático, você aprenderá como fazer o login no Docker Hub e enviar
imagens para o repositório do Docker Hub.

## Faça o login com sua conta do Docker

Para enviar imagens para o Docker Hub, você precisará fazer o login com uma
conta do Docker.

1. Abra o Painel do Docker.

2. Selecione **Sign in** no canto superior direito.

3. Se necessário, crie uma conta e conclua o processo de login.

Quando terminar, você verá o botão **Sign in** se transformar em uma foto de
perfil.

## Crie um repositório de imagens

Agora que você tem uma conta, pode criar um repositório de imagens.
Assim como um repositório Git armazena código-fonte, um repositório de imagens
armazena imagens de contêiner.

1. Acesse o [Docker Hub](https://hub.docker.com).

2. Selecione **Create repository**.

3. Na página **Create repository**, insira as seguintes informações:

   - **Repository name** - `getting-started-todo-app`.
   - **Short description** - Sinta-se à vontade para inserir uma descrição, se
     desejar.
   - **Visibility** - Selecione **Public** para permitir que outras pessoas
     baixem sua aplicação de tarefas personalizada.

4. Selecione **Create** para criar o repositório.

## Crie e envie a imagem

Agora que você tem um repositório, pode criar e enviar sua imagem.
Uma observação importante é que a imagem que você está criando estende a imagem
do Node, o que significa que você não precisa instalar ou configurar o Node, o
Yarn, etc.
Você pode simplesmente se concentrar no que torna sua aplicação única.

> **O que é uma imagem/Dockerfile?**
>
> Sem entrar muito em detalhes ainda, pense em uma imagem de contêiner como um
> único pacote que contém tudo o que é necessário para executar um processo.
> Nesse caso, ela conterá um ambiente Node, o código do back-end e o código
> React compilado.
>
> Qualquer máquina que execute um contêiner usando a imagem poderá executar a
> aplicação como ela foi criada, sem precisar de mais nada pré-instalado na
> máquina.
>
> Um `Dockerfile` é um script baseado em texto que fornece o conjunto de
> instruções sobre como criar a imagem.
> Para esse início rápido, o repositório já contém o `Dockerfile`.

{{< tabs group="cli-or-vs-code" persist=true >}}
{{< tab name="CLI" >}}

1. Para começar, clone ou
   [baixe o projeto como um arquivo ZIP](https://github.com/docker/getting-started-todo-app/archive/refs/heads/main.zip)
   para sua máquina local.

   ```console
   $ git clone https://github.com/docker/getting-started-todo-app
   ```

   Após o projeto ser clonado, navegue até o novo diretório criado pelo comando:

   ```console
   $ cd getting-started-todo-app
   ```

2. Construa o projeto executando o seguinte comando, trocando `usuário-do-docker`
   pelo seu nome de usuário:

   ```console
   $ docker build -t <usuário-do-docker>/getting-started-todo-app .
   ```

   Por exemplo, se seu nome de usuário do Docker fosse `mobydock`, você
   executaria o seguinte:

   ```console
   $ docker build -t mobydock/getting-started-todo-app .
   ```

3. Para verificar se a imagem existe localmente, você pode usar o comando
   `docker image ls`:

   ```console
   $ docker image ls
   ```

   Você verá uma saída semelhante à seguinte:

    ```console
    REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
    mobydock/getting-started-todo-app   latest    1543656c9290   2 minutes ago    1.12GB
    ...
    ```

4. Para enviar a imagem, use o comando `docker push`.
   Certifique-se de substituir `usuário-do-docker` pelo seu nome de usuário:

   ```console
   $ docker push <usuário-do-docker>/getting-started-todo-app
   ```

   Dependendo da sua velocidade de upload, o envio pode levar alguns instantes.

{{< /tab >}}
{{< tab name="VS Code" >}}

1. Abra o Visual Studio Code.
   Certifique-se de ter a **extensão Docker para VS Code** instalada do [Mercado de Extensões](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

   ![Captura de tela do mercado de extensões do VS Code](images/install-docker-extension.webp)

2. No menu **File**, selecione **Open Folder**.
   Escolha **Clone Git Repository** e cole esta URL:
   [https://github.com/docker/getting-started-todo-app](https://github.com/docker/getting-started-todo-app)

   ![Captura de tela do VS Code mostrando como clonar um repositório](images/clone-the-repo.webp?border=true)

3. Clique com o botão direito no `Dockerfile` e selecione a opção **Build
   Image...** no menu.

   ![Captura de tela do VS Code mostrando o menu do botão direito e a opção "Build Image"](images/build-vscode-menu-item.webp?border=true)

4. Na caixa de diálogo que aparece, insira o nome
   `usuário-do-docker/getting-started-todo-app`, substituindo `usuário-do-docker`
   pelo seu nome de usuário do Docker.

5. Após pressionar **Enter**, um terminal para a criação da imagem será aberto.
   Assim que o processo for concluído, você pode fechá-lo.

6. Abra a Extensão Docker para VS Code selecionando o logotipo do Docker no menu
   de navegação esquerdo.

7. Encontre a imagem que você criou.
   Ela terá um nome de `docker.io/usuário-do-docker/getting-started-todo-app`.

8. Expanda a imagem para visualizar as tags (ou diferentes versões) da imagem.
   Você deve ver uma tag chamada `latest`, que é a tag padrão atribuída a uma
   imagem.

9. Clique com o botão direito no item **latest** e selecione a opção
   **Push...**.

   ![Captura de tela da extensão do Docker e do menu do botão direito para enviar uma imagem](images/build-vscode-push-image.webp)

10. Pressione **Enter** para confirmar e observe enquanto sua imagem é enviada
    para o Docker Hub.
    Dependendo da velocidade de upload da sua conexão, o envio da imagem pode
    levar alguns instantes.

    Assim que o upload for concluído, você pode fechar o terminal.

{{< /tab >}}
{{< /tabs >}}

## Recapitulando

Antes de prosseguir, reserve um momento para refletir sobre o que aconteceu
aqui.
Em poucos instantes, você conseguiu criar uma imagem de contêiner que empacota
sua aplicação e enviá-la para o Docker Hub.

No futuro, lembre-se de que:

- O Docker Hub é o registro de referência para encontrar conteúdo confiável.
  O Docker fornece uma coleção de conteúdo confiável, composta por Imagens
  Oficiais do Docker, Editores Verificados pelo Docker e Software de Código
  Aberto Patrocinado pelo Docker, para usar diretamente ou como base para suas
  imagens.

- O Docker Hub fornece um mercado para distribuir suas aplicações.
  Qualquer pessoa pode criar uma conta e distribuir imagens.
  Enquanto você distribui publicamente a imagem criada, repositórios privados
  podem garantir que suas imagens sejam acessíveis apenas a pessoas usuárias
  autorizadas.

> **Uso de outros registros**
>
> Embora o Docker Hub seja o registro padrão, os registros são padronizados e
> se tornaram interoperáveis por meio da
> [Open Container Initiative](https://opencontainers.org/).
> Isso permite que empresas e organizações executem seus próprios registros
> privados.
> Muitas vezes, o conteúdo confiável é espelhado (ou copiado) do Docker Hub para
> esses registros privados.

## Próximos passos

Agora que você criou uma imagem, é hora de discutir por que você, como pessoa
desenvolvedora, deve aprender mais sobre o Docker e como ele pode te ajudar nas
suas tarefas diárias.

{{< button text="O que vem a seguir" url="whats-next" >}}
