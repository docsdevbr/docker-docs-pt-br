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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/_index.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

description: >-
  Encontre a versão mais recente recomendada do formato de arquivo Docker
  Compose para definir aplicações multicontêineres.
keywords: >-
  arquivo docker compose, docker compose yml, referência do docker compose,
  comando docker compose, usuário docker compose, imagem docker compose, sintaxe
  do docker compose, especificação yaml, especificação docker compose
title: Referência do arquivo compose
toc_max: 4
toc_min: 1
grid:
  - title: Elementos de nível superior version e name
    description: Entenda os atributos version e name do Compose.
    icon: document-text
    link: /reference/compose-file/version-and-name/
  - title: Elemento de nível superior services
    description: Explore todos os atributos de services do Compose.
    icon: wrench-screwdriver
    link: /reference/compose-file/services/
  - title: Elemento de nível superior networks
    description: Encontre todos os atributos de networks do Compose.
    icon: server-stack
    link: /reference/compose-file/networks/
  - title: Elemento de nível superior volumes
    description: Explore todos os atributos de volumes do Compose.
    icon: circle-stack
    link: /reference/compose-file/volumes/
  - title: Elemento de nível superior configs
    description: Saiba mais sobre configs no Compose.
    icon: cog-6-tooth
    link: /reference/compose-file/configs/
  - title: Elemento de nível superior secrets
    description: Saiba mais sobre secrets no Compose.
    icon: lock-closed
    link: /reference/compose-file/secrets/
aliases:
  - /compose/compose-file/
  - /compose/reference/overview/
---

> **Iniciando no Docker Compose?**
>
> Encontre mais informações sobre os
> [principais recursos e casos de uso do Docker Compose](/manuals/compose/intro/features-uses.md)
> ou [experimente o guia de início rápido](/manuals/compose/gettingstarted.md).

A Especificação do Compose é a versão mais recente e recomendada do formato de
arquivo Compose.
Ela te ajuda a definir um
[arquivo Compose](/manuals/compose/intro/compose-application-model.md) usado
para configurar os serviços, redes, volumes da sua aplicação Docker e muito
mais.

As versões legadas 2.x e 3.x do formato de arquivo Compose foram incorporadas à
Especificação do Compose.
Ela é implementada nas versões 1.27.0 e superiores (também conhecidas como
Compose v2) da CLI do Docker Compose.

A Especificação do Compose na documentação do Docker é a implementação do Docker
Compose.
Se você deseja implementar sua própria versão da Especificação do Compose,
consulte o
[repositório da Especificação do Compose](https://github.com/compose-spec/compose-spec).

Use os links a seguir para navegar pelas seções principais da Especificação do
Compose.

> [!TIP]
>
> Quer uma experiência de edição melhor para arquivos Compose no VS Code?
> Confira a extensão
> [Docker DX](https://marketplace.visualstudio.com/items?itemName=docker.docker)
> para linting, navegação de código e verificação de vulnerabilidades.

{{< grid >}}
