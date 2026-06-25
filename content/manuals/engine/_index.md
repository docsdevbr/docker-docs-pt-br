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

source_url: https://github.com/docker/docs/blob/main/content/manuals/engine/_index.md
source_revision: ee71c80562c0edf1177ed79a8d67ddcd83a726e4
translation_status: ready

title: Docker Engine
weight: 10
description: >-
  Encontre uma visão geral completa da Docker Engine, incluindo instruções de
  instalação, detalhes de armazenamento, rede e muito mais.
keywords: Engine
params:
  sidebar:
    group: Application development
grid:
  - title: Instale a Docker Engine
    description: >-
      Aprenda como instalar a Docker Engine de código aberto para sua
      distribuição.
    icon: arrow-down-tray
    link: /engine/install
  - title: Armazenamento
    description: Use dados persistentes com contêineres Docker.
    icon: circle-stack
    link: /storage
  - title: Redes
    description: Gerencie conexões de rede entre contêineres.
    icon: server-stack
    link: /network
  - title: Logs de contêiner
    description: Aprenda como visualizar e ler logs de contêiner.
    icon: document-text
    link: /config/containers/logging/
  - title: Limpeza
    description: Remova os recursos não usados.
    icon: scissors
    link: /config/pruning
  - title: Configure o daemon
    description: Explore as opções de configuração do daemon do Docker.
    icon: adjustments-horizontal
    link: /config/daemon
  - title: Modo rootless
    description: Execute o Docker sem privilégios de root.
    icon: shield-check
    link: /engine/security/rootless
  - title: Recursos descontinuados
    description: >-
      Descubra quais recursos da Docker Engine você deve parar de usar.
    icon: folder-minus
    link: /engine/deprecated/
  - title: Notas de lançamento
    description: Leia as notas de lançamento da versão mais recente.
    icon: document-plus
    link: /engine/release-notes
aliases:
  - /edge/
  - /engine/ce-ee-node-activate/
  - /engine/migration/
  - /engine/misc/
  - /linux/
---

A Docker Engine é uma tecnologia de conteinerização de código aberto para
construir e conteinerizar suas aplicações.
A Docker Engine funciona como uma aplicação cliente-servidor com:

- Um servidor com um processo daemon de longa duração
  [`dockerd`](/reference/cli/dockerd).
- APIs que especificam interfaces que os programas podem usar para se comunicar
  e instruir o daemon do Docker.
- Um cliente de interface de linha de comando (CLI)
  [`docker`](/reference/cli/docker/).

A CLI usa as [APIs do Docker](/reference/api/engine/_index.md) para controlar ou
interagir com o daemon do Docker por meio de scripts ou comandos diretos da CLI.
Muitas outras aplicações Docker usam a API e a CLI subjacentes.
O daemon cria e gerencia objetos do Docker, como imagens, contêineres, redes e
volumes.

Para obter mais detalhes, consulte a
[Arquitetura do Docker](/get-started/docker-overview.md#docker-architecture).

{{< grid >}}

## Licenciamento

O uso comercial da Docker Engine obtido via Docker Desktop em grandes empresas
(com mais de 250 pessoas funcionárias OU com receita anual superior a US$ 10
milhões) requer uma
[assinatura paga](https://www.docker.com/pricing?ref=Docs&refAction=DocsEngine).
Licença Apache, versão 2.0.
Consulte o arquivo [LICENSE](https://github.com/moby/moby/blob/master/LICENSE)
para obter a licença completa.
