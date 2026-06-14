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

source_url: https://github.com/docker/docs/blob/main/content/manuals/engine/security/rootless/uid-gid-mapping.md
source_revision: 2d7809c7a74ba1e99609a44f421a15cbf8a4a4bc
translation_status: ready

description: >-
  Como os UIDs e GIDs dos contêineres são mapeados para o host no modo rootless.
keywords: segurança, namespaces, rootless, uid, gid, subuid, subgid
title: Mapeamento de UID/GID
weight: 15
---

O modo rootless e o modo [`userns-remap`](../userns-remap.md) mapeiam os UIDs e
GIDs dos contêineres para o host de maneiras diferentes.

- No modo `userns-remap`, o UID do contêiner `0` é mapeado para o primeiro UID
  subordinado listado em `/etc/subuid` para o usuário de remapeamento, e o UID
  do contêiner `n` é mapeado para `subuid + n`.
- No modo rootless, o UID do contêiner `0` é mapeado para o UID do host do
  usuário que executa o Docker sem privilégios de root (o resultado de `id -u`);
  O UID do contêiner `n` (para `n >= 1`) é mapeado para `subuid + (n - 1)`.

Os GIDs seguem as mesmas regras usando `/etc/subgid`.

Essa diferença é importante ao definir permissões de arquivo em diretórios
montados por bind: no modo rootless, os arquivos pertencentes ao usuário do host
aparecem como pertencentes ao `root` dentro do contêiner.
