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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/fragments.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

title: Fragmentos
description: Reutilize configurações com âncoras e fragmentos YAML.
keywords: >-
  compose, especificação do compose, fragmentos, referência do arquivo compose
aliases:
  - /compose/compose-file/10-fragments/
weight: 70
---

{{% include "compose/fragments.md" %}}

Âncoras são criadas usando o símbolo `&`.
Esse símbolo é seguido por um apelido.
Você pode usar esse apelido posteriormente com o símbolo `*` para referenciar o
valor que segue a âncora.
Certifique-se de que não haja espaço entre os caracteres `&` e `*` e o nome do
apelido que os segue.

Você pode usar mais de uma âncora e apelido em um único arquivo Compose.

## Exemplo 1

```yml
volumes:
  db-data: &default-volume
    driver: default
  metrics: *default-volume
```

No exemplo acima, uma âncora `default-volume` é criada com base no volume
`db-data`.
Ela é posteriormente reutilizada pelo apelido `*default-volume` para definir o
volume `metrics`.

A resolução de âncoras ocorre antes da
[interpolação de variáveis](interpolation.md); portanto, variáveis não podem ser
usadas para definir âncoras ou apelidos.

## Exemplo 2

```yml
services:
  first:
    image: my-image:latest
    environment: &env
      - CONFIG_KEY
      - EXAMPLE_KEY
      - DEMO_VAR
  second:
    image: another-image:latest
    environment: *env
```

Se você tiver uma âncora que deseja usar em mais de um serviço, use-a em
conjunto com uma [extensão](extension.md) para facilitar a manutenção do seu
arquivo Compose.

## Exemplo 3

Você pode querer substituir valores parcialmente.
O Compose segue a regra definida pelo
[tipo de mesclagem do YAML](https://yaml.org/type/merge.html).

No exemplo a seguir, a especificação do volume `metrics` usa um apelido para
evitar repetições, mas substitui o atributo `name`:

```yml
services:
  backend:
    image: example/database
    volumes:
      - db-data
      - metrics
volumes:
  db-data: &default-volume
    driver: default
    name: "data"
  metrics:
    <<: *default-volume
    name: "metrics"
```

## Exemplo 4

Você também pode estender a âncora para adicionar valores extras.

```yml
services:
  first:
    image: my-image:latest
    environment: &env
      FOO: BAR
      ZOT: QUIX
  second:
    image: another-image:latest
    environment:
      <<: *env
      YET_ANOTHER: VARIABLE
```

> [!NOTE]
>
> A [mesclagem do YAML](https://yaml.org/type/merge.html) aplica-se apenas a
> mapeamentos e não pode ser usada com sequências.

No exemplo acima, as variáveis de ambiente devem ser declaradas usando a sintaxe
de mapeamento `FOO: BAR`, enquanto a sintaxe de sequência `- FOO=BAR` só é
válida quando não há fragmentos envolvidos.
