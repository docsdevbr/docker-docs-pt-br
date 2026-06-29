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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/include.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

linkTitle: Include
title: Use include para modularizar arquivos Compose
description: >-
  Referencie arquivos Compose externos usando o elemento de nível superior
  include.
keywords: compose, especificação compose, include, referência de arquivo compose
aliases:
  - /compose/compose-file/14-include/
weight: 110
---

{{< summary-bar feature_name="Composefile include" >}}

Você pode reutilizar e modularizar configurações do Docker Compose incluindo
outros arquivos Compose.
Isso é útil se:
- Você deseja reutilizar outros arquivos Compose.
- Você precisa fatorar partes do seu modelo de aplicação em arquivos Compose
  separados para poderem ser gerenciados separadamente ou compartilhados com
  outras pessoas.
- Os times precisam manter um arquivo Compose com apenas a complexidade
  necessária para a quantidade limitada de recursos que ele precisa declarar
  para seu próprio subdomínio em uma implantação maior.

A seção de nível superior `include` é usada para definir a dependência de outra
aplicação Compose ou subdomínio.
Cada caminho listado na seção `include` é carregado como um modelo de aplicação
Compose individual, com seu próprio diretório de projeto, para resolver caminhos
relativos.

Assim que a aplicação Compose incluída é carregada, todas as definições de
recursos são copiadas para o modelo de aplicação Compose atual.
O Compose exibe um aviso se houver conflito de nomes de recursos e não tenta
mesclá-los.
Para garantir isso, `include` é avaliado após os arquivos Compose selecionados
para definir o modelo de aplicação Compose serem analisados e mesclados, de
forma que conflitos entre arquivos Compose sejam detectados.

`include` aplica-se recursivamente, portanto, um arquivo Compose incluído que
declara sua própria seção `include` aciona a inclusão desses outros arquivos
também.

Quaisquer volumes, redes ou outros recursos obtidos do arquivo Compose incluído
podem ser usados pela aplicação Compose atual para referências entre serviços.
Por exemplo:

```yaml
include:
  - meu-include-compose.yaml  # com servico-b declarado
services:
  servico-a:
    build: .
    depends_on:
      - servico-b # usa o servico-b diretamente como se estivesse declarado
                  # neste arquivo Compose
```

O Compose também suporta o uso de variáveis interpoladas com `include`.
Recomenda-se que você [especifique variáveis obrigatórias](interpolation.md).
Por exemplo:

```text
include:
  -${INCLUDE_PATH:?FOO}/compose.yaml
```

## Sintaxe curta

A sintaxe curta define apenas caminhos para outros arquivos Compose.
O arquivo é carregado com a pasta pai como diretório do projeto e um arquivo
`.env` opcional carregado para definir os valores padrão de quaisquer variáveis
por interpolação.
O ambiente local do projeto pode sobrescrever esses valores.

```yaml
include:
  - ../commons/compose.yaml
  - ../outro_dominio/compose.yaml

services:
  webapp:
    depends_on:
      - servico-incluido # definido por outro_dominio
```

No exemplo anterior, tanto `../commons/compose.yaml` quanto
`../outro_dominio/compose.yaml` são carregados como projetos Compose
individuais.
Caminhos relativos em arquivos Compose referenciados por `include` são
resolvidos em relação ao seu próprio caminho de arquivo Compose, e não com base
no diretório do projeto local.
As variáveis são interpoladas usando valores definidos no arquivo opcional
`.env` na mesma pasta e são sobrescritas pelo ambiente do projeto local.

## Sintaxe longa

A sintaxe longa oferece mais controle sobre a análise do subprojeto:

```yaml
include:
   - path: ../commons/compose.yaml
     project_directory: ..
     env_file: ../outro/.env
```

### `path`

`path` é obrigatório e define a localização do(s) arquivo(s) Compose a serem
analisados e incluídos no modelo Compose local.

`path` pode ser definido como:

- Uma string: ao usar um único arquivo Compose.
- Uma lista de strings: quando vários arquivos Compose precisam ser
  [mesclados](merge.md) para definir o modelo Compose para a aplicação local.

```yaml
include:
   - path:
       - ../commons/compose.yaml
       - ./commons-override.yaml
```

### `project_directory`

`project_directory` define um caminho base para resolver caminhos relativos
definidos no arquivo Compose.
O padrão é o diretório do arquivo Compose incluído.

### `env_file`

`env_file` define um ou mais arquivos de ambiente para usar na definição de
valores padrão ao interpolar variáveis no arquivo Compose que está sendo
analisado.
O padrão é o arquivo `.env` no `project_directory` do arquivo Compose que está
sendo analisado.

`env_file` pode ser definido como uma string ou uma lista de strings quando
vários arquivos de ambiente precisam ser mesclados para definir um ambiente de
projeto.

```yaml
include:
   - path: ../outro/compose.yaml
     env_file:
       - ../outro/.env
       - ../outro/dev.env
```

O ambiente do projeto local tem precedência sobre os valores definidos pelo
arquivo Compose, permitindo que o projeto local substitua os valores para
personalização.

## Recursos adicionais

Para obter mais informações sobre como usar `include`, consulte
[Trabalhando com vários arquivos Compose](/manuals/compose/how-tos/multiple-compose-files/_index.md).
