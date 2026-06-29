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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/secrets.md
source_revision: 80faf488d21a7598b8101185bfa5ce5e38b4915b
translation_status: ready

title: Segredos
description: >-
  Explore todos os atributos que o elemento de nível superior secrets pode ter.
keywords: >-
  compose, especificação do compose, segredos, referência de arquivo compose
aliases:
  - /compose/compose-file/09-secrets/
weight: 60
---

Segredos são uma variação de [Configurações](configs.md) com foco em dados
sensíveis, com restrições específicas para esse uso.

Os serviços só podem acessar segredos quando explicitamente concedidos por um
[atributo `secrets`](services.md#secrets) dentro do elemento de nível superior
`services`.

A declaração de nível superior `secrets` define ou referencia dados sensíveis
concedidos aos serviços em sua aplicação Compose.
A origem do segredo é `file` ou `environment`.

- `file`: O segredo é criado com o conteúdo do arquivo no caminho especificado.
- `environment`: O segredo é criado com o valor de uma variável de ambiente no
  host.
  Isso só é compatível com o Docker Compose.
  Não é compatível com a implantação usando [`docker stack deploy`](/manuals/engine/swarm/stack-deploy.md).

## Exemplo 1

O segredo `server-certificate` é criado como
`<nome_do_projeto>_server-certificate` quando a aplicação é implantada,
registrando o conteúdo do arquivo `server.cert` como um segredo da plataforma.

```yml
secrets:
  server-certificate:
    file: ./server.cert
```

## Exemplo 2

O segredo `token` é criado como `<nome_do_projeto>_token` quando a aplicação é
implantada, registrando o conteúdo da variável de ambiente `OAUTH_TOKEN` como um
segredo da plataforma.

```yml
secrets:
  token:
    environment: "OAUTH_TOKEN"
```

> [!NOTE]
>
> Segredos `environment` não são suportados ao implantar com
> `docker stack deploy`.
> Use `file` ou `external` como fonte de segredos.

## Recursos adicionais

Para mais informações, consulte
[Como usar segredos no Compose](/manuals/compose/how-tos/use-secrets.md).
