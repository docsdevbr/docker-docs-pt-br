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

source_url: https://github.com/docker/docs/blob/main/content/includes/compose/configure-watch.md
source_revision: e3aa78b72c9faf56e97896681c33425cafc1c847
translation_status: ready
---

1. Adicione seções `watch` a um ou mais serviços no arquivo `compose.yaml`.
2. Execute `docker compose up --watch` para construir e iniciar um projeto
   Compose e ativar o modo de monitoramento de arquivos.
3. Edite os arquivos de origem do serviço usando sua IDE ou editor preferido.
