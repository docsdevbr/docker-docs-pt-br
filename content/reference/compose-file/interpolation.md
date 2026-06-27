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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/interpolation.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

title: Interpolação
description: >-
  Substitua variáveis de ambiente em arquivos do Docker Compose usando a sintaxe
  de interpolação.
keywords: >-
  compose, especificação do compose, interpolação, referência do arquivo compose
aliases:
  - /compose/compose-file/12-interpolation/
weight: 90
---

{{% include "compose/interpolation.md" %}}

Para expressões entre chaves, os seguintes formatos são suportados:
- Substituição direta
  - `${VAR}` -> valor de `VAR`
- Valor padrão
  - `${VAR:-default}` -> valor de `VAR` se definido e não vazio; caso contrário,
    `default`.
  - `${VAR-default}` -> valor de `VAR` se definido; caso contrário, `default`.
- Valor obrigatório
  - `${VAR:?error}` -> valor de `VAR` se definido e não vazio; caso contrário,
    encerra com erro.
  - `${VAR?error}` -> valor de `VAR` se definido; caso contrário, encerra com
    erro.
- Valor alternativo
  - `${VAR:+replacement}` -> `replacement` se `VAR` estiver definido e não
    vazio; caso contrário, vazio.
  - `${VAR+replacement}` -> `replacement` se `VAR` estiver definido; caso
    contrário, vazio.

A interpolação também pode ser aninhada:

- `${VARIABLE:-${FOO}}`
- `${VARIABLE?$FOO}`
- `${VARIABLE:-${FOO:-default}}`

Outros recursos estendidos no estilo shell, como `${VARIABLE/foo/bar}`, não são
suportados pelo Compose.

O Compose processa qualquer string que siga um sinal de `$` desde que ela forme
uma definição de variável válida — seja um nome alfanumérico
(`[_a-zA-Z][_a-zA-Z0-9]*`) ou uma string entre chaves começando com `${`.
Em outras circunstâncias, ela será preservada sem tentativa de interpolação de
valor.

Você pode usar `$$` (sinal de cifrão duplo) quando sua configuração precisar de
um sinal de cifrão literal.
Isso também impede que o Compose interpole um valor; portanto, `$$` permite
referenciar variáveis de ambiente que você não deseja que sejam processadas pelo
Compose.

```yml
web:
  build: .
  command: "$$VARIAVEL_NAO_INTERPOLADA_PELO_COMPOSE"
```

Se o Compose não conseguir resolver uma variável substituída e nenhum valor
padrão estiver definido, ele exibirá um aviso e substituirá a variável por uma
string vazia.

Como quaisquer valores em um arquivo do Compose podem ser interpolados via
substituição de variáveis, incluindo a notação de string compacta para elementos
complexos, a interpolação é aplicada antes da mesclagem, sendo realizada arquivo
por arquivo.

A interpolação aplica-se apenas aos valores YAML, não às chaves.
Nos poucos casos em que as chaves são, de fato, strings arbitrárias definidas
pela pessoa usuária, como em [labels](services.md#labels) ou
[environment](services.md#environment), deve-se usar uma sintaxe alternativa com
sinal de igual para que a interpolação ocorra.
Por exemplo:

```yml
services:
  foo:
    labels:
      "$VAR_NOT_INTERPOLATED_BY_COMPOSE": "BAR"
```

```yml
services:
  foo:
    labels:
      - "$VAR_INTERPOLATED_BY_COMPOSE=BAR"
```
