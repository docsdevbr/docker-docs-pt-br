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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/extension.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

title: Extensões
description: >-
  Defina e reutilize fragmentos personalizados com extensões no Docker Compose.
keywords: >-
  compose, especificação do compose, extensões, referência do arquivo compose
aliases:
  - /compose/compose-file/11-extension/
weight: 80
---

{{% include "compose/extension.md" %}}

As extensões também podem ser usadas com [âncoras e apelidos](fragments.md).

Elas também podem ser usadas em qualquer estrutura de um arquivo Compose onde
não se espera a presença de chaves definidas pela pessoa usuária.
O Compose as usa para habilitar recursos experimentais, da mesma forma que
navegadores adicionam suporte a
[recursos CSS personalizados](https://www.w3.org/TR/2011/REC-CSS2-20110607/syndata.html#vendor-keywords).

## Exemplo 1

```yml
x-custom:
  foo:
    - bar
    - zot

services:
  webapp:
    image: example/webapp
    x-foo: bar
```

```yml
service:
  backend:
    deploy:
      placement:
        x-aws-role: "arn:aws:iam::XXXXXXXXXXXX:role/foo"
        x-aws-region: "eu-west-3"
        x-azure-region: "france-central"
```

## Exemplo 2

```yml
x-env: &env
  environment:
    - CONFIG_KEY
    - EXAMPLE_KEY

services:
  first:
    <<: *env
    image: my-image:latest
  second:
    <<: *env
    image: another-image:latest
```

Neste exemplo, as variáveis de ambiente não pertencem a nenhum dos serviços.
Elas foram totalmente extraídas para o campo de extensão `x-env`.
Isso define um novo nó que contém o campo de ambiente.
A âncora YAML `&env` é usada para que ambos os serviços possam referenciar o
valor do campo de extensão como `*env`.

## Exemplo 3

```yml
x-function: &function
 labels:
   function: "true"
 depends_on:
   - gateway
 networks:
   - functions
 deploy:
   placement:
     constraints:
       - 'node.platform.os == linux'
services:
 # O Node.js fornece informações do sistema operacional sobre o nó (host).
 nodeinfo:
   <<: *function
   image: functions/nodeinfo:latest
   environment:
     no_proxy: "gateway"
     https_proxy: $https_proxy
 # Usa `cat` para exibir a resposta; é a função mais rápida de executar.
 echoit:
   <<: *function
   image: functions/alpine:health
   environment:
     fprocess: "cat"
     no_proxy: "gateway"
     https_proxy: $https_proxy
```

Os serviços `nodeinfo` e `echoit` incluem a extensão `x-function` por meio da
âncora `&function` e, em seguida, definem suas respectivas imagens e variáveis
de ambiente.

## Exemplo 4

Usando a [mescalgem do YAML](https://yaml.org/type/merge.html) também é possível
empregar múltiplas extensões, bem como compartilhar e sobrescrever atributos
adicionais para atender a necessidades específicas:

```yml
x-environment: &default-environment
  FOO: BAR
  ZOT: QUIX
x-keys: &keys
  KEY: VALUE
services:
  frontend:
    image: example/webapp
    environment:
      << : [*default-environment, *keys]
      YET_ANOTHER: VARIABLE
```

> [!NOTE]
>
> A [mesclagem do YAML](https://yaml.org/type/merge.html) aplica-se apenas a
> mapeamentos e não pode ser usada com sequências.
>
> No exemplo acima, as variáveis de ambiente são declaradas usando a sintaxe de
> mapeamento `FOO: BAR`, enquanto a sintaxe de sequência `- FOO=BAR` só é válida
> quando não há fragmentos envolvidos.

## Notas históricas informativas

Esta seção é informativa.
No momento da redação deste documento, sabe-se da existência dos seguintes
prefixos:

| Prefixo    | Fornecedor/Organização |
| ---------- | ---------------------- |
| docker     | Docker                 |
| kubernetes | Kubernetes             |

## Especificação de valores de bytes

Os valores expressam uma quantidade de bytes como uma string no formato
`{quantidade}{unidade de byte}`: as unidades suportadas são `b` (bytes), `k` ou
`kb` (kilobytes), `m` ou `mb` (megabytes) e `g` ou `gb` (gigabytes).

```text
    2b
    1024kb
    2048k
    300m
    1gb
```

## Especificando durações

Os valores expressam uma duração como uma string no formato `{valor}{unidade}`.
As unidades suportadas são `us` (microssegundos), `ms` (milissegundos), `s`
(segundos), `m` (minutos) e `h` (horas).
Os valores podem combinar múltiplos valores sem separador.

```text
  10ms
  40s
  1m30s
  1h5m30s20ms
```
