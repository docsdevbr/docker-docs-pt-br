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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/02_our_app.md
source_revision: ca0e85cf12f7a517cb633f75e414c2a4169dd5dd
translation_status: ready

title: Conteinerize uma aplicação
weight: 20
linkTitle: "Parte 1: conteinerize uma aplicação"
keywords: >-
  exemplo de dockerfile, conteinerize uma aplicação, executar dockerfile,
  executando dockerfile, como executar dockerfile, exemplo de dockerfile, como
  criar um contêiner docker, criar dockerfile, dockerfile simples, criando
  contêineres
description: >-
  Siga este guia passo a passo para aprender como criar e executar uma aplicação
  conteinerizada usando o Docker.
aliases:
  - /get-started/part2/
  - /get-started/02_our_app/
  - /guides/workshop/02_our_app/
  - /guides/walkthroughs/containerize-your-app/
---

Para o restante deste guia, você trabalhará com um gerenciador de listas de
tarefas simples que roda em Node.js.
Se você não tiver familiaridade com Node.js, não se preocupe.
Este guia não exige nenhuma experiência prévia com JavaScript.

## Pré-requisitos

- Você instalou a versão mais recente do
  [Docker Desktop](/get-started/get-docker.md).
- Você instalou um [cliente Git](https://git-scm.com/downloads).
- Você tem uma IDE ou um editor de texto para editar arquivos.
  O Docker recomenda o uso do
  [Visual Studio Code](https://code.visualstudio.com/).

## Obtenha a aplicação

Antes de executar a aplicação, você precisa baixar o código-fonte da aplicação
em sua máquina.

1. Clone o repositório
   [getting-started-app](https://github.com/docker/getting-started-app/tree/main)
   usando o seguinte comando:

   ```console
   $ git clone https://github.com/docker/getting-started-app.git
   ```

2. Visualize o conteúdo do repositório clonado.
   Você deverá ver os seguintes arquivos e subdiretórios.

   ```text
   ├── getting-started-app/
   │ ├── .dockerignore
   │ ├── package.json
   │ ├── package-lock.json
   │ ├── README.md
   │ ├── spec/
   │ ├── src/
   ```

## Construa a imagem da aplicação

Para construir a imagem, você precisará usar um Dockerfile.
Um Dockerfile é simplesmente um arquivo de texto sem extensão que contém um
script de instruções.
O Docker usa esse script para construir uma imagem de contêiner.

1. No diretório `getting-started-app`, no mesmo local do arquivo `package.json`,
   crie um arquivo chamado `Dockerfile` com o seguinte conteúdo:

   ```dockerfile
   # syntax=docker/dockerfile:1

   FROM node:24-alpine
   WORKDIR /app
   COPY . .
   RUN npm install --omit=dev
   CMD ["node", "src/index.js"]
   EXPOSE 3000
   ```

   Este Dockerfile faz o seguinte:

   - Usa `node:24-alpine` como imagem base, uma imagem Linux leve com Node.js
     pré-instalado.
   - Define `/app` como o diretório de trabalho.
   - Copia o código-fonte para a imagem.
   - Instala as dependências necessárias.
   - Especifica o comando para iniciar a aplicação.
   - Documenta que a aplicação está escutando na porta 3000.

2. Construa a imagem usando os seguintes comandos:

   No terminal, certifique-se de estar no diretório `getting-started-app`.
   Substitua `/caminho/para/getting-started-app` pelo caminho para o seu
   diretório `getting-started-app`.

   ```console
   $ cd /caminho/para/getting-started-app
   ```

   Construa a imagem.

   ```console
   $ docker build -t getting-started .
   ```

   O comando `docker build` usa o Dockerfile para construir uma nova imagem.
   Você deve ter notado que o Docker baixou várias "camadas".
   Isso ocorre porque você instruiu o construtor a começar com a imagem
   `node:24-alpine`.
   Como você não tinha essa imagem na sua máquina, o Docker precisou baixá-la.

   Após o Docker baixar a imagem, as instruções do Dockerfile foram copiadas
   para a sua aplicação e o `npm` foi usado para instalar as dependências da sua
   aplicação.

   Por fim, a flag `-t` cria uma tag da sua imagem.
   Pense nisso como um nome legível para a imagem final.
   Como você nomeou a imagem como `getting-started`, você pode se referir a ela
   ao executar um contêiner.

   O ponto (`.`) no final do comando `docker build` indica ao Docker que ele
   deve procurar o `Dockerfile` no diretório atual.

## Inicie um contêiner de aplicação

Agora que você tem uma imagem, pode executar a aplicação em um contêiner usando
o comando `docker run`.

1. Execute seu contêiner usando o comando `docker run` e especifique o nome da
   imagem que você acabou de criar:

   ```console
   $ docker run -d -p 127.0.0.1:3000:3000 getting-started
   ```

   A opção `-d` (abreviação de `--detach`) executa o contêiner em segundo plano.
   Isso significa que o Docker inicia o seu contêiner e retorna você ao prompt
   do terminal.
   Além disso, ele não exibe logs no terminal.

   A opção `-p` (abreviação de `--publish`) cria um mapeamento de porta entre o
   host e o contêiner.
   A opção `-p` aceita um valor de string no formato de `HOST:CONTÊINER`, onde
   `HOST` é o endereço no host e `CONTÊINER` é a porta no contêiner.
   O comando publica a porta 3000 do contêiner para `127.0.0.1:3000`
   (`localhost:3000`) no host.
   Sem o mapeamento de porta, você não conseguiria acessar a aplicação a partir
   do host.

2. Após alguns segundos, abra seu navegador em
   [http://localhost:3000](http://localhost:3000).
   Você deverá ver sua aplicação.

   ![Lista de tarefas vazia](images/todo-list-empty.webp)

3. Adicione um ou dois itens e veja se funciona como esperado.
   Você pode marcar itens como concluídos e removê-los.
   Seu frontend está armazenando itens com sucesso no backend.

Neste ponto, você tem um gerenciador de listas de tarefas em execução com alguns
itens.

Se você der uma olhada rápida em seus contêineres, deverá ver pelo menos um
contêiner em execução usando a imagem `getting-started` e na porta `3000`.
Para ver seus contêineres, você pode usar a CLI ou a interface gráfica do Docker
Desktop.

{{< tabs >}}
{{< tab name="CLI" >}}

Execute o comando `docker ps` em um terminal para listar seus contêineres.

```console
$ docker ps
```

Deverá aparecer um resultado semelhante ao seguinte.

```console
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
df784548666d        getting-started     "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        127.0.0.1:3000->3000/tcp   priceless_mcclintock
```

{{< /tab >}}
{{< tab name="Docker Desktop" >}}

No Docker Desktop, selecione a aba **Containers** para ver uma lista dos seus
contêineres.

![Docker Desktop com o contêiner get-started em execução](images/dashboard-two-containers.webp)

{{< /tab >}}
{{< /tabs >}}

## Resumo

Nesta seção, você aprendeu o básico sobre como criar um Dockerfile para
construir uma imagem.
Após construir a imagem, você iniciou um contêiner e viu a aplicação em
execução.

Informações relacionadas:

- [Referência do Dockerfile](/reference/dockerfile/)
- [Referência da CLI do docker](/reference/cli/docker/)

## Próximos passos

A seguir, você fará uma modificação na sua aplicação e aprenderá como
atualizá-lo com uma nova imagem.
Ao longo do processo, você aprenderá alguns outros comandos úteis.

{{< button text="Atualizar a aplicação" url="03_updating_app.md" >}}
