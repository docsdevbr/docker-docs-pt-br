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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/running-containers/publishing-ports.md
source_revision: b1256d572f75131b9feb85c30fb1c25f9813dcb5
translation_status: ready

title: Publicando e expondo portas
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará a importância de publicar e expor portas no
  Docker.
weight: 1
aliases:
 - /guides/docker-concepts/running-containers/publishing-ports/
---

{{< youtube-embed 9JnqOmJ96ds >}}

## Explicação

Se você seguiu os guias até aqui, já deve ter entendido que os contêineres
fornecem processos isolados para cada componente da sua aplicação.
Cada componente - um frontend React, uma API Python e um banco de dados Postgres
\- é executado em seu próprio ambiente isolado, completamente livre de tudo o
\- mais na sua máquina host.
Esse isolamento é ótimo para segurança e gerenciamento de dependências, mas
também significa que você não pode acessá-los diretamente.
Por exemplo, você não pode acessar a aplicação web no seu navegador.

É aí que entra a publicação de portas.

### Publicando portas

Publicar uma porta permite quebrar um pouco o isolamento da rede, configurando
uma regra de encaminhamento.
Por exemplo, você pode indicar que as requisições na porta `8080` do seu host
devem ser encaminhadas para a porta `80` do contêiner.
A publicação de portas ocorre durante a criação do contêiner usando a flag `-p`
(ou `--publish`) com `docker run`.
A sintaxe é:

```console
$ docker run -d -p PORTA_DO_HOST:PORTA_DO_CONTÊINER nginx
```

- `PORTA_DO_HOST`: O número da porta na sua máquina host onde você deseja
  receber tráfego.
- `PORTA_DO_CONTÊINER`: O número da porta dentro do contêiner que está escutando
  conexões.

Por exemplo, para publicar a porta `80` do contêiner na porta `8080` do host:

```console
$ docker run -d -p 8080:80 nginx
```

Agora, qualquer tráfego enviado para a porta `8080` na sua máquina host será
encaminhado para a porta `80` dentro do contêiner.

> [!IMPORTANT]
>
> Quando uma porta é publicada, ela é publicada em todas as interfaces de rede
> por padrão.
> Isso significa que qualquer tráfego que chegue à sua máquina poderá acessar a
> aplicação publicada.
> Tenha cuidado ao publicar bancos de dados ou qualquer informação sensível.
> [Saiba mais sobre portas publicadas aqui](/engine/network/#published-ports).

### Publicando em portas efêmeras

Às vezes, você pode simplesmente querer publicar a porta, mas não se importa com
qual porta do host será usada.
Nesses casos, você pode deixar o Docker escolher a porta para você.
Para fazer isso, basta omitir a configuração `PORTA_DO_HOST`.

Por exemplo, o comando a seguir publicará a porta `80` do contêiner em uma porta
efêmera no host:

```console
$ docker run -p 80 nginx
```

Assim que o contêiner estiver em execução, o comando `docker ps` mostrará a
porta escolhida:

```console
docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                    NAMES
a527355c9c53   nginx         "/docker-entrypoint.…"   4 seconds ago    Up 3 seconds    0.0.0.0:54772->80/tcp    romantic_williamson
```

Neste exemplo, a aplicação é exposta no host na porta `54772`.

### Publicando todas as portas

Ao criar uma imagem de contêiner, a instrução `EXPOSE` é usada para indicar que
a aplicação empacotada usará a porta especificada.
Essas portas não são publicadas por padrão.

Com a flag `-P` ou `--publish-all`, você pode publicar automaticamente todas as
portas expostas em portas efêmeras.
Isso é bastante útil quando você está tentando evitar conflitos de portas em
ambientes de desenvolvimento ou teste.

Por exemplo, o comando a seguir publicará todas as portas expostas configuradas
pela imagem:

```console
$ docker run -P nginx
```

## Experimente

Neste guia prático, você aprenderá como publicar portas de contêineres usando a
CLI e o Docker Compose para implantar uma aplicação web.

### Usando a CLI do Docker

Nesta etapa, você executará um contêiner e publicará sua porta usando a CLI do
Docker.

1. [Baixe e instale](/get-started/get-docker/) o Docker Desktop.

2. Em um terminal, execute o seguinte comando para iniciar um novo contêiner:

   ```console
   $ docker run -d -p 8080:80 docker/welcome-to-docker
   ```

   O primeiro `8080` refere-se à porta do host.
   Esta é a porta na sua máquina local que será usada para acessar a aplicação
   em execução dentro do contêiner.
   O segundo `80` refere-se à porta do contêiner.
   Esta é a porta que a aplicação dentro do contêiner usa para aguardar conexões
   de entrada.
   Portanto, o comando vincula a porta `8080` do host à porta `80` do sistema do
   contêiner.

3. Verifique a porta publicada acessando a visualização **Containers** do painel
   do Docker Desktop.

   ![Uma captura de tela do painel do Docker Desktop mostrando a porta publicada](images/published-ports.webp?w=5000&border=true)

4. Abra o site selecionando o link na coluna **Port(s)** do seu contêiner ou
   acessando [http://localhost:8080](http://localhost:8080) no seu navegador.

   ![Uma captura de tela da página inicial do servidor web Nginx em execução em um contêiner](/get-started/docker-concepts/the-basics/images/access-the-frontend.webp?border=true)

### Use o Docker Compose

Este exemplo executará a mesma aplicação usando o Docker Compose:

1. Crie um novo diretório e, dentro dele, crie um arquivo `compose.yaml` com o
   seguinte conteúdo:

   ```yaml
   services:
     app:
       image: docker/welcome-to-docker
       ports:
         - 8080:80
   ```

   A configuração `ports` aceita algumas sintaxes diferentes para a definição da
   porta.
   Neste caso, você está usando a mesma sintaxe
   `PORTA_DO_HOST:PORTA_DO_CONTÊINER` usada no comando `docker run`.

2. Abra um terminal e navegue até o diretório que você criou no passo anterior.

3. Use o comando `docker compose up` para iniciar a aplicação.

4. Abra seu navegador e acesse [http://localhost:8080](http://localhost:8080).

## Recursos adicionais

Se você quiser se aprofundar neste tópico, confira os seguintes recursos:

- [Referência da CLI do `docker container port`](/reference/cli/docker/container/port/)
- [Portas publicadas](/engine/network/#published-ports)

## Próximos passos

Agora que você entende como publicar e expor portas, pode aprender como
sobrescrever as configurações padrão do contêiner usando o comando `docker run`.

{{< button text="Sobrescrevendo as configurações padrão do contêiner" url="overriding-container-defaults" >}}
