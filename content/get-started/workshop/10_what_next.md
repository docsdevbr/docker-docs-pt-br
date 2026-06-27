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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/10_what_next.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

title: O que vem depois do workshop do Docker
weight: 100
linkTitle: "Parte 9: o que vem a seguir"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop, ia, executor de modelos, mcp, agentes,
  imagens reforçadas, segurança
description: >-
  Explore os próximos passos após concluir o workshop do Docker, incluindo a
  segurança de suas imagens, desenvolvimento de IA e guias específicos por
  linguagem.
aliases:
  - /get-started/11_what_next/
  - /guides/workshop/10_what_next/
summary: >-
  Agora que você concluiu o workshop do Docker, pode explorar a segurança de
  suas imagens com Imagens Docker Reforçadas, criar aplicações com tecnologia de
  IA e mergulhar em guias específicos por linguagem.
notoc: true

secure-images:
- title: O que são Imagens Docker Reforçadas?
  description: >-
    Conheça imagens base seguras, minimalistas e prontas para produção, com
    praticamente zero CVEs.
  link: /dhi/explore/what/
- title: Comece a usar a DHI
  description: Baixe e execute sua primeira Imagem Docker Reforçada em minutos.
  link: /dhi/get-started/
- title: Use imagens reforçadas
  description: Aprenda a usar a DHI em seus Dockerfiles e pipelines de CI/CD.
  link: /dhi/how-to/use/
- title: Explore o catálogo da DHI
  description: >-
    Confira as imagens reforçadas, variantes e atestados de segurança
    disponíveis.
  link: /dhi/how-to/explore/

ai-development:
- title: Docker Model Runner
  description: >-
    Execute e gerencie modelos de IA localmente usando comandos familiares do
    Docker e APIs compatíveis com a OpenAI.
  link: /ai/model-runner/
- title: Kit de Ferramentas MCP
  description: >-
    Configure, gerencie e execute servidores MCP em contêineres para
    potencializar seus agentes de IA.
  link: /ai/mcp-catalog-and-toolkit/toolkit/
- title: Crie agentes de IA com o Docker Agent
  description: >-
    Crie times de agentes de IA especializados que colaboram para resolver
    problemas complexos.
  link: /ai/docker-agent/
- title: Use modelos de IA no Compose
  description: >-
    Defina dependências de modelos de IA em suas aplicações Docker Compose.
  link: /ai/compose/models-and-compose/

language-guides:
- title: Node.js
  description: Aprenda como conteinerizar e desenvolver aplicações Node.js.
  link: /guides/language/nodejs/
- title: Python
  description: Crie e execute aplicações Python em contêineres.
  link: /guides/language/python/
- title: Java
  description: Conteinerize aplicações Java com as boas práticas.
  link: /guides/language/java/
- title: Go
  description: Desenvolva e implante aplicações Go usando Docker.
  link: /guides/language/golang/
---

Parabéns por concluir o workshop de Docker.
Você aprendeu a conteinerizar aplicações, trabalhar com configurações
multicontêineres, usar o Docker Compose e aplicar as melhores práticas de
criação de imagens.

Veja o que explorar a seguir.

## Proteja suas imagens

Eleve o nível das suas habilidades de criação de imagens com as Imagens Docker
Reforçadas — imagens base seguras, minimalistas e prontas para produção, agora
gratuitas para todas as pessoas.

{{< grid items="secure-images" >}}

## Construa com IA

O Docker facilita a execução local de modelos de IA e a criação de aplicações de
IA com agentes.
Explore as ferramentas de IA do Docker e comece a criar aplicações com
tecnologia de IA.

{{< grid items="ai-development" >}}

## Guias específicos por linguagem

Aplique o que você aprendeu à sua linguagem de programação preferida com
tutoriais práticos.

{{< grid items="language-guides" >}}
