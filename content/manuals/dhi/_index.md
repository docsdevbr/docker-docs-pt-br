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

source_url: https://github.com/docker/docs/blob/main/content/manuals/dhi/_index.md
source_revision: b4f08ba8de3b2c904002aa2cc75f7a249bbbb9cd
translation_status: ready

title: Imagens Docker Reforçadas
description: Imagens base seguras, minimalistas e prontas para produção.
weight: 8
params:
  sidebar:
    group: Supply chain security
  grid_sections:
    - title: Início rápido
      description: >-
        Siga um guia passo a passo para explorar e executar uma Imagem Docker
        Reforçada.
      icon: rocket-launch
      link: /dhi/get-started/
    - title: Explore
      description: >-
        Aprenda o que são Imagens Docker Reforçadas, como elas são construídas e
        o que as diferencia das imagens base típicas.
      icon: information-circle
      link: /dhi/explore/
    - title: Recursos
      description: >-
        Descubra os recursos de segurança, conformidade e prontidão corporativa
        integrados às Imagens Docker Reforçadas.
      icon: lock-closed
      link: /dhi/features/
    - title: Tutoriais
      description: >-
        Guias passo a passo para usar, verificar, analisar e migrar para Imagens
        Docker Reforçadas.
      icon: play
      link: /dhi/how-to/
    - title: Conceitos básicos
      description: >-
        Compreenda os princípios da cadeia de suprimentos segura que tornam as
        Imagens Docker Reforçadas prontas para produção.
      icon: clipboard-document-check
      link: /dhi/core-concepts/
    - title: Solução de problemas
      description: >-
        Resolva problemas comuns na criação, execução ou depuração de Imagens
        Docker Reforçadas.
      icon: question-mark-circle
      link: /dhi/troubleshoot/
    - title: Recursos adicionais
      description: >-
        Guias, catálogo do Docker Hub, repositórios do GitHub e muito mais.
      icon: link
      link: /dhi/resources/
    - title: Notas de lançamento
      description: >-
        Novos recursos, melhorias e alterações nas Imagens Docker Reforçadas.
      icon: newspaper
      link: /dhi/release-notes/platform/
---

As Imagens Docker Reforçadas (DHI) fornecem imagens de contêiner, gráficos Helm
e pacotes de sistema mínimos, seguros e prontos para produção, mantidos pela
Docker.
Projetadas para reduzir vulnerabilidades e simplificar a conformidade, as DHI se
integram facilmente aos seus fluxos de trabalho existentes baseados em Docker,
com pouca ou nenhuma necessidade de reconfiguração.

As DHI estão disponíveis nas seguintes três assinaturas:

| Recurso                                                                                            | Community | Select  | Enterprise                            |
|----------------------------------------------------------------------------------------------------|-----------|---------|---------------------------------------|
| Imagens reforçadas e mínimas                                                                       | ✅         | ✅       | ✅                                     |
| Quase zero CVEs                                                                                    | ✅         | ✅       | ✅                                     |
| Proveniência verificável de SBOMs e SLSA Build L3                                                  | ✅         | ✅       | ✅                                     |
| Visibilidade completa e sem supressão de CVEs                                                      | ✅         | ✅       | ✅                                     |
| Adoção imediata, sem alterações no fluxo de trabalho                                               | ✅         | ✅       | ✅                                     |
| Catálogo completo de imagens de código aberto sob Apache 2.0                                       | ✅         | ✅       | ✅                                     |
| Construído com Pacotes de Sistema Reforçados do Docker                                             | ✅         | ✅       | ✅                                     |
| Cadência upstream para patches lançados pelo Docker                                                | ✅         | ✅       | ✅                                     |
| Variantes FIPS/STIG                                                                                | ❌         | ✅       | ✅                                     |
| Correções de CVE críticas em menos de 7 dias com aplicação contínua de patches com garantia de SLA | ❌         | ✅       | ✅                                     |
| Personalizações                                                                                    | ❌         | ✅ Até 5 | ✅ Ilimitado                           |
| Acesso ao repositório de Pacotes de Sistema Reforçados                                             | ❌         | ❌       | ✅                                     |
| Acesso ao catálogo completo disponível                                                             | ❌         | ❌       | ✅                                     |
| Suporte ao Ciclo de Vida Estendido disponível                                                      | ❌         | ❌       | ✅ +5 anos de atualizações reforçadas. |

Para preços e mais detalhes, consulte a
[comparação de assinaturas das Imagens Docker Reforçadas](https://www.docker.com/products/hardened-images/#compare).

Explore as seções abaixo para começar a usar as Imagens Docker Reforçadas,
integrá-las ao seu fluxo de trabalho e aprender o que as torna seguras e prontas
para uso corporativo.

{{< grid
  items="grid_sections"
>}}
