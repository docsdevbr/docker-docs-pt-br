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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/models.md
source_revision: 479885a3d85077d831ab48c94cc78e80e6ead2fa
translation_status: ready

title: Modelos
description: Saiba mais sobre o elemento de nível superior models.
keywords: compose, especificação compose, modelos, referência de arquivo compose
weight: 120
---

{{< summary-bar feature_name="Compose models" >}}

A seção de nível superior `models` declara os modelos de IA usados pela sua
aplicação Compose.
Esses modelos são normalmente obtidos como artefatos OCI, executados por um
executor de modelos e expostos como uma API que seus contêineres de serviço
podem consumir.

Os serviços só podem acessar os modelos quando explicitamente concedido por um
atributo [`models` attribute](services.md#models) dentro do elemento de nível
superior `services`.

## Exemplos

### Exemplo 1

```yaml
services:
  app:
    image: app
    models:
      - ai_model


models:
  ai_model:
    model: ai/model
```

Neste exemplo básico:

- O serviço app usa o `ai_model`.
- O `ai_model` é definido como um artefato OCI (`ai/model`) que é obtido e
  servido pelo executor de modelos.
- O Docker Compose injeta informações de conexão, por exemplo, `AI_MODEL_URL`,
  no contêiner.

### Exemplo 2

```yaml
services:
  app:
    image: app
    models:
      my_model:
        endpoint_var: MODEL_URL

models:
  my_model:
    model: ai/model
    context_size: 1024
    runtime_flags:
      - "--a-flag"
      - "--another-flag=42"
```

Nesta configuração avançada:

- O serviço app referencia `my_model` usando a sintaxe longa.
- O Compose injeta a URL do executor de modelos como a variável de ambiente
  `MODEL_URL`.

## Atributos

- `model` (obrigatório): o identificador do artefato OCI para o modelo.
  É isso que o Compose extrai e executa por meio do executor de modelos.
- `context_size`: define o tamanho máximo do contexto de token para o modelo.
- `runtime_flags`: uma lista de parâmetros de linha de comando brutos passados
  para a engine de inferência quando o modelo é iniciado.

## Recursos adicionais

Para mais exemplos e informações sobre como usar `model`, consulte
[Use modelos de IA no Compose](/manuals/ai/compose/models-and-compose.md).
