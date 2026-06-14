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

source_url: https://github.com/docker/docs/blob/main/content/includes/compose/secrets.md
source_revision: e3aa78b72c9faf56e97896681c33425cafc1c847
translation_status: ready
---

O Docker Compose permite usar segredos sem precisar usar variáveis de ambiente
para armazenar informações.
Se você estiver injetando senhas e chaves de API como variáveis de ambiente,
pode expor informações acidentalmente.
Os serviços só podem acessar segredos quando explicitamente autorizados por um
atributo `secrets` dentro do elemento de nível superior `services`.
