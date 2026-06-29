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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/merge.md
source_revision: 4c6f75f19facf9fcba938315035addd2949f180b
translation_status: ready

linkTitle: Mesclagem
title: Mescle arquivos Compose
description: >-
  Entenda como o Docker Compose mescla vários arquivos e resolve conflitos.
keywords: >-
  compose, especificação do compose, mesclar, referência de arquivos compose
aliases:
  - /compose/compose-file/13-merge/
weight: 100
---

{{% include "compose/merge.md" %}}

Essas regras estão descritas abaixo.

## Mapeamento

Um `mapeamento` YAML é mesclado adicionando as entradas ausentes e mesclando as
conflitantes.

Mesclando as seguintes árvores YAML de exemplo:

```yaml
services:
  foo:
    chave1: valor1
    chave2: valor2
```

```yaml
services:
  foo:
    chave2: VALOR
    chave3: valor3
```

Resulta em um modelo de aplicação Compose equivalente à árvore YAML:

```yaml
services:
  foo:
    chave1: valor1
    chave2: VALOR
    chave3: valor3
```

## Sequência

Uma `sequence` YAML é mesclada adicionando os valores do arquivo Compose que a
substitui ao arquivo anterior.

Mesclar as seguintes árvores YAML de exemplo:

```yaml
services:
  foo:
    DNS:
      - 1.1.1.1
```

```yaml
services:
  foo:
    DNS:
      - 8.8.8.8
```

Resulta em um modelo de aplicação Compose equivalente à árvore YAML:

```yaml
services:
  foo:
    DNS:
      - 1.1.1.1
      - 8.8.8.8
```

## Exceções

### Comandos do Shell

Ao mesclar arquivos Compose que usam os atributos de serviço
[command](services.md#command), [entrypoint](services.md#entrypoint) e
[healthcheck: `test`](services.md#healthcheck), o valor é sobrescrito pelo
arquivo Compose mais recente e não é anexado.

Mesclar as seguintes árvores YAML de exemplo:

```yaml
services:
  foo:
    command: ["echo", "foo"]
```

```yaml
services:
  foo:
    command: ["echo", "bar"]
```

Resulta em um modelo de aplicação Compose equivalente à árvore YAML:

```yaml
services:
  foo:
    command: ["echo", "bar"]
```

### Recursos únicos

Aplica-se aos atributos de serviço [ports](services.md#ports),
[volumes](services.md#volumes), [secrets](services.md#secrets) e
[configs](services.md#configs).
Embora esses tipos sejam modelados em um arquivo Compose como uma sequência,
eles possuem requisitos especiais de unicidade:

| Atributo | Chave única                       |
|----------|-----------------------------------|
| volumes  | target                            |
| secrets  | target                            |
| configs  | target                            |
| ports    | {ip, target, published, protocol} |

Ao mesclar arquivos Compose, o Compose adiciona novas entradas que não violam
uma restrição de unicidade e mescla entradas que compartilham uma chave única.

Mesclar as seguintes árvores YAML de exemplo:

```yaml
services:
  foo:
    volumes:
      - foo:/work
```

```yaml
services:
  foo:
    volumes:
      - bar:/work
```

Resulta em um modelo de aplicação Compose equivalente à árvore YAML:

```yaml
services:
  foo:
    volumes:
      - bar:/work
```

### Redefinir valor

Além do mecanismo descrito anteriormente, um arquivo Compose de sobrescrita
também pode ser usado para remover elementos do seu modelo de aplicação.
Para isso, a [tag YAML](https://yaml.org/spec/1.2.2/#24-tags) personalizada
`!reset` pode ser definida para sobrescrever um valor definido pelo arquivo
Compose personalizado.
Um valor válido para o atributo deve ser fornecido, mas será ignorado e o
atributo de destino será definido com o valor padrão do tipo ou `null`.

Para facilitar a leitura, recomenda-se definir explicitamente o valor do
atributo como nulo (`null`) ou um array vazio `[]` (com `!reset null` ou
`!reset []`) para que fique claro que o atributo resultante será limpo.

Um arquivo `compose.yaml` base:

```yaml
services:
  app:
    image: myapp
    ports:
      - "8080:80"
    environment:
      FOO: BAR
```

E um arquivo `compose.override.yaml`:

```yaml
services:
  app:
    image: myapp
    ports: !reset []
    environment:
      FOO: !reset null
```

Resultam em:

```yaml
services:
  app:
    image: myapp
```

### Substituir valor

{{< summary-bar feature_name="Compose replace file" >}}

Embora `!reset` possa ser usado para remover uma declaração de um arquivo
Compose usando um arquivo de sobrescrita, `!override` permite que você substitua
completamente um atributo, ignorando as regras de mesclagem padrão.
Um exemplo típico é substituir completamente uma definição de recurso, para
depender de um modelo distinto, mas usando o mesmo nome.

Um arquivo `compose.yaml` base:

```yaml
services:
  app:
    image: myapp
    ports:
      - "8080:80"
```

Para remover a porta original e expor uma nova, usa-se o seguinte arquivo de
sobrescrita:

```yaml
services:
  app:
    ports: !override
      - "8443:443"
```

Isso resulta em:

```yaml
services:
  app:
    image: myapp
    ports:
      - "8443:443"
```

Se `!override` não tivesse sido usado, tanto `8080:80` quanto `8443:443` seriam
expostos conforme as [regras de mesclagem descritas acima](#sequência).

## Recursos adicionais

Para obter mais informações sobre como o merge pode ser usado para criar um
arquivo Compose composto, consulte
[Trabalhando com vários arquivos Compose](/manuals/compose/how-tos/multiple-compose-files/_index.md).
