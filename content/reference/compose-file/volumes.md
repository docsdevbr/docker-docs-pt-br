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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/volumes.md
source_revision: 80faf488d21a7598b8101185bfa5ce5e38b4915b
translation_status: ready

linkTitle: Volumes
title: Defina e gerencie volumes no Docker Compose
description: >-
  Controle como os volumes são declarados e compartilhados entre serviços usando
  o elemento de nível superior volumes.
keywords: >-
  compose, especificação do compose, volumes, referência do arquivo compose
aliases:
  - /compose/compose-file/07-volumes/
weight: 40
---

{{% include "compose/volumes.md" %}}

Para usar um volume em vários serviços, você deve conceder acesso explicitamente
a cada serviço usando o atributo [volumes](services.md#volumes) dentro do
elemento de nível superior `services`.
O atributo `volumes` possui uma sintaxe adicional que oferece um controle mais
granular.

> [!TIP]
>
> Trabalhando com repositórios grandes ou monorepos, ou com sistemas de arquivos
> virtuais que não escalam mais com sua base de código?
> O Compose agora aproveita os
> [Compartilhamentos de arquivos sincronizados](/manuals/desktop/features/synchronized-file-sharing.md)
> e cria automaticamente compartilhamentos de arquivos para montagens de volume.
> Certifique-se de ter acessado o Docker com uma assinatura paga e de ter
> habilitado as opções **Access experimental features** e **Manage Synchronized
> file shares with Compose** nas configurações do Docker Desktop.

## Exemplo

O exemplo a seguir mostra uma configuração com dois serviços, onde o diretório
de dados de um banco de dados é compartilhado com outro serviço como um volume,
chamado `db-data`, para poder ser copiado periodicamente.

```yml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

  backup:
    image: backup-service
    volumes:
      - db-data:/var/lib/backup/data

volumes:
  db-data:
```

O volume `db-data` é montado nos caminhos de contêiner `/var/lib/backup/data` e
`/etc/data` para backup e backend, respectivamente.

Executar `docker compose up` cria o volume se ele ainda não existir.
Caso contrário, o volume existente é usado e é recriado se for excluído
manualmente fora do Compose.

## Atributos

Uma entrada na seção `volumes` de nível superior pode estar vazia, caso em que
usa a configuração padrão da engine de contêiner para criar um volume.
Opcionalmente, você pode configurá-la com as seguintes chaves:

### `driver`

Especifica qual driver de volume deve ser usado.
Se o driver não estiver disponível, o Compose retorna um erro e não implanta a
aplicação.

```yml
volumes:
  db-data:
    driver: foobar
```

### `driver_opts`

`driver_opts` especifica uma lista de opções como pares chave-valor para passar
ao driver para este volume.
As opções dependem do driver.

```yml
volumes:
  example:
    driver_opts:
      type: "nfs"
      o: "addr=10.40.0.199,nolock,soft,rw"
      device: ":/docker/example"
```

Se você deseja uma montagem de volume nomeada, use o driver `local` com
`driver_opts`.
Esse padrão atribui um nome estável ao volume do Compose, mapeando-o para um
caminho específico do host.

```yaml
volumes:
  app-data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /srv/app-data # deve ser o caminho absoluto do host e já existir
```

As chaves `type`, `o` e `device` são passadas para o driver local.
Para uma montagem única de caminho de host em um único serviço, consulte
[montagens de vinculação](/manuals/engine/storage/bind-mounts.md).

### `external`

Se definido como `true`:
  - `external` especifica que este volume já existe na plataforma e seu ciclo de
    vida é gerenciado fora da aplicação.
    O Compose não cria o volume e retorna um erro se o volume não existir.
  - Todos os outros atributos, exceto `name`, são irrelevantes.
    Se o Compose detectar qualquer outro atributo, ele rejeitará o arquivo
    Compose como inválido.

No exemplo a seguir, em vez de tentar criar um volume chamado
`{nome_do_projeto}_db-data`, o Compose procura um volume existente simplesmente
chamado `db-data` e o monta nos contêineres do serviço `backend`.

```yml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

volumes:
  db-data:
    external: true
```

### `labels`

`labels` são usadas para adicionar metadados aos volumes.
Você pode usar um array ou um dicionário.

Recomenda-se o uso da notação DNS reversa para evitar conflitos entre suas
labels e as usadas por outros softwares.

```yml
volumes:
  db-data:
    labels:
      com.example.description: "Database volume"
      com.example.department: "IT/Ops"
      com.example.label-with-empty-value: ""
```

```yml
volumes:
  db-data:
    labels:
      - "com.example.description=Database volume"
      - "com.example.department=IT/Ops"
      - "com.example.label-with-empty-value"
```

O Compose define as labels `com.docker.compose.project` e
`com.docker.compose.volume`.

> [!NOTE]
>
> As labels definidas aqui se aplicam somente a volumes nomeados.
> Elas são armazenadas no recurso de volume e visíveis por meio de
> `docker volume inspect`.
> Elas não se aplicam a montagens de volume e não alteram a semântica de
> montagem.

### `name`

`name` define um nome personalizado para um volume.
O campo `name` pode ser usado para referenciar volumes que contêm caracteres
especiais.
O nome é usado como está e não é limitado pelo nome da pilha.

```yml
volumes:
  db-data:
    name: "my-app-data"
```

Isso possibilita que esse nome de pesquisa seja um parâmetro do arquivo Compose,
de forma que o ID do modelo para o volume seja codificado, mas o ID real do
volume na plataforma seja definido em tempo de execução durante a implantação.

Por exemplo, se `DATABASE_VOLUME=my_volume_001` estiver no seu arquivo `.env`:

```yml
volumes:
  db-data:
    name: ${DATABASE_VOLUME}
```

Executar `docker compose up` usa o volume chamado `my_volume_001`.

Ele também pode ser usado em conjunto com a propriedade `external`.
Isso significa que o nome usado para localizar o volume real na plataforma é
definido separadamente do nome usado para se referir ao volume dentro do arquivo
Compose:

```yml
volumes:
  db-data:
    external: true
    name: nome-real-do-volume
```
