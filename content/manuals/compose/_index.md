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

source_url: https://github.com/docker/docs/blob/main/content/manuals/compose/_index.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

title: Docker Compose
weight: 30
description: >-
  Aprenda a usar o Docker Compose para definir e executar aplicações
  multicontêineres com esta introdução detalhada à ferramenta.
keywords: >-
  docker compose, docker-compose, compose.yaml, comando docker compose,
  aplicações multicontêineres, orquestração de contêineres, docker cli
params:
  sidebar:
    group: Application development
grid:
  - title: Por que usar o Compose?
    description: Entenda os principais benefícios do Docker Compose.
    icon: magnifying-glass
    link: /compose/intro/features-uses/
  - title: Como o Compose funciona
    description: Entenda como o Compose funciona.
    icon: squares-2x2
    link: /compose/intro/compose-application-model/
  - title: Instale o Compose
    description: Siga as instruções para instalar o Docker Compose.
    icon: arrow-down-tray
    link: /compose/install
  - title: Início rápido
    description: >-
      Aprenda os principais conceitos do Docker Compose enquanto cria uma
      aplicação web simples em Python.
    icon: magnifying-glass-plus
    link: /compose/gettingstarted
  - title: Consulte as notas de lançamento
    description: >-
      Descubra as melhorias e correções de bugs mais recentes.
    icon: document-plus
    link: "https://github.com/docker/compose/releases"
  - title: Explore a referência do arquivo Compose
    description: >-
      Encontre informações sobre como definir serviços, redes e volumes para uma
      aplicação Docker.
    icon: arrows-right-left
    link: /reference/compose-file
  - title: Use o Compose Bridge
    description: >-
      Transforme seu arquivo de configuração do Compose em arquivos de
      configuração para diferentes plataformas, como o Kubernetes.
    icon: arrow-down
    link: /compose/bridge
  - title: Navegue pelas perguntas frequentes mais comuns
    description: >-
      Explore as perguntas frequentes gerais e descubra como enviar feedback.
    icon: question-mark-circle
    link: /compose/faq
aliases:
  - /compose/overview/
  - /compose/swarm/
  - /compose/releases/migrate/
---

O Docker Compose é uma ferramenta para definir e executar aplicações
multicontêineres.
É a chave para uma experiência de desenvolvimento e implantação simplificada e
eficiente.

O Compose simplifica o controle de toda a sua pilha de aplicações, facilitando o
gerenciamento de serviços, redes e volumes em um único arquivo de configuração
YAML.
Em seguida, com um único comando, você cria e inicia todos os serviços a partir
do seu arquivo de configuração.

O Compose funciona em todos os ambientes - produção, homologação,
desenvolvimento, testes e fluxos de trabalho de CI.
Ele também possui comandos para gerenciar todo o ciclo de vida da sua aplicação:

- Iniciar, parar e reconstruir serviços.
- Visualizar o status dos serviços em execução.
- Transmitir a saída de log dos serviços em execução.
- Executar um comando único em um serviço.

{{< grid >}}
