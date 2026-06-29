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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/profiles.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

linkTitle: Perfis
title: Aprenda a usar perfis no Docker Compose
description: Saiba mais sobre perfis.
keywords: >-
  compose, especificação do compose, perfis, referência de arquivo compose
aliases:
  - /compose/compose-file/15-profiles/
weight: 120
---

Com perfis, você pode definir um conjunto de perfis ativos para que o modelo da
sua aplicação Compose seja ajustado para diversos usos e ambientes.

O elemento de nível superior [services](services.md) suporta um atributo
`profiles` para definir uma lista de perfis nomeados.
Serviços sem um atributo `profiles` estão sempre habilitados.

Um serviço é ignorado pelo Compose quando nenhum dos `profiles` listados
corresponde aos ativos, a menos que o serviço seja explicitamente selecionado
por um comando.
Nesse caso, seu perfil é adicionado ao conjunto de perfis ativos.

> [!NOTE]
>
> Todos os outros elementos de nível superior não são afetados por `profiles` e
> estão sempre ativos.

Referências a outros serviços (por meio de `links`, `extends` ou sintaxe de
recurso compartilhado `service:xxx`) não habilitam automaticamente um componente
que, de outra forma, teria sido ignorado pelos perfis ativos.
Em vez disso, o Compose retorna um erro.

## Exemplo ilustrativo

```yaml
services:
  web:
    image: web_image

  test_lib:
    image: test_lib_image
    profiles:
      - test

  coverage_lib:
    image: coverage_lib_image
    depends_on:
      - test_lib
    profiles:
      - test

  debug_lib:
    image: debug_lib_image
    depends_on:
      - test_lib
    profiles:
      - debug
```

No exemplo acima:

- Se o modelo de aplicação Compose for analisado quando nenhum perfil estiver
  habilitado, ele conterá apenas o serviço `web`.
- Se o perfil `test` estiver habilitado, o modelo conterá os serviços `test_lib`
  e `coverage_lib`, e o serviço `web`, que está sempre habilitado.
- Se o perfil `debug` estiver habilitado, o modelo conterá os serviços `web` e
  `debug_lib`, mas não `test_lib` e `coverage_lib`, e, portanto, o modelo será
  inválido em relação à restrição `depends_on` de `debug_lib`.
- Se os perfis `debug` e `test` estiverem habilitados, o modelo conterá todos os
  serviços: `web`, `test_lib`, `coverage_lib` e `debug_lib`.
- Se o Compose for executado com `test_lib` como o serviço explícito a ser
  executado, `test_lib` e o perfil `test` estarão ativos mesmo que o perfil
  `test` não esteja habilitado.
- Se o Compose for executado com `coverage_lib` como o serviço explícito a ser
  executado, o serviço `coverage_lib` e o perfil `test` estarão ativos e
  `test_lib` será incluído pela restrição `depends_on`.
- Se o Compose for executado com `debug_lib` como o serviço explícito a ser
  executado, novamente o modelo será inválido em relação à restrição
  `depends_on` de `debug_lib`, já que `debug_lib` e `test_lib` não possuem
  `profiles` em comum listados.
- Se o Compose for executado com `debug_lib` como o serviço explícito a ser
  executado e o perfil `test` estiver habilitado, o perfil `debug` será
  automaticamente habilitado e o serviço `test_lib` será incluído como uma
  dependência, iniciando ambos os serviços `debug_lib` e `test_lib`.

Aprenda a usar `profiles` em
[Docker Compose](/manuals/compose/how-tos/profiles.md).
