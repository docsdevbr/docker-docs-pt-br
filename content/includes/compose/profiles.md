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

source_url: https://github.com/docker/docs/blob/main/content/includes/compose/profiles.md
source_revision: f54284dfb157b03b906c6d3ebb344148eda4b15e
translation_status: ready
---

Os profiles (perfis) ajudam você a ajustar sua aplicação Compose para diferentes
ambientes ou casos de uso, ativando serviços seletivamente.
Os serviços podem ser atribuídos a um ou mais perfis; serviços não atribuídos
iniciam/param por padrão, enquanto os atribuídos só iniciam/param quando seu
perfil está ativo.
Essa configuração significa que serviços específicos, como aqueles para
depuração ou desenvolvimento, podem ser incluídos em um único arquivo
`compose.yml` e ativados somente quando necessário.
