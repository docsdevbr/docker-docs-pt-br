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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/develop.md
source_revision: f62d925a50f11aee9cef6f33e4bd215729143aaa
translation_status: ready

title: Especificação do Develop do Compose
description: Saiba mais sobre a Especificação do Develop do Compose
keywords: >-
  compose, especificação do compose, referência de arquivo do compose,
  especificação do develop do compose

compose, compose specification, compose file reference, compose develop specification
aliases:
  - /compose/compose-file/develop/
weight: 150
---

> [!NOTE]
>
> Develop é uma parte opcional da especificação do Compose.
> Está disponível a partir da versão 2.22.0 do Docker Compose.

{{% include "compose/services-develop.md" %}}

Esta página define como o Compose se comporta para auxiliar você de forma
eficiente e define as restrições e fluxos de trabalho de desenvolvimento
estabelecidos pelo Compose.
Apenas um subconjunto de serviços de arquivos do Compose pode exigir uma
subseção `develop`.

## Exemplo ilustrativo

```yaml
services:
  frontend:
    image: example/webapp
    build: ./webapp
    develop:
      watch:
        # sincroniza o conteúdo estático
        - path: ./webapp/html
          action: sync
          target: /var/www
          ignore:
            - node_modules/

  backend:
    image: example/backend
    build: ./backend
    develop:
      watch:
       # reconstrói a imagem e recria o serviço
        - path: ./backend/src
          action: rebuild
```

## Atributos

A subseção `develop` define opções de configuração aplicadas pelo Compose para
auxiliar no desenvolvimento de um serviço com fluxos de trabalho otimizados.

### `watch`

O atributo `watch` define uma lista de regras que controlam atualizações
automáticas do serviço com base em alterações em arquivos locais.
`watch` é uma sequência; cada item individual na sequência define uma regra a
ser aplicada pelo Compose para monitorar alterações no código-fonte.
Para obter mais informações, consulte
[Use o Compose Watch](/manuals/compose/how-tos/file-watch.md).

#### `action`

`action` define a ação a ser tomada quando alterações são detectadas.
Se `action` estiver definido como:

- `rebuild`: o Compose reconstrói a imagem do serviço com base na seção `build`
  e recria o serviço com a imagem atualizada.
- `restart`: o Compose reinicia o contêiner do serviço.
  Disponível a partir da versão 2.32.0 do Docker Compose.
- `sync`: o Compose mantém os contêineres de serviço existentes em execução, mas
  sincroniza os arquivos de origem com o conteúdo do contêiner conforme o
  atributo `target`.
- `sync+restart`: o Compose sincroniza os arquivos de origem com o conteúdo do
  contêiner conforme o atributo `target` e, em seguida, reinicia o contêiner.
  Disponível a partir da versão 2.23.0 do Docker Compose.
- `sync+exec`: o Compose sincroniza os arquivos de origem com o conteúdo do
  contêiner conforme o atributo `target` e, em seguida, executa um comando
  dentro do contêiner.
  Disponível a partir da versão 2.32.0 do Docker Compose.

#### `exec`

{{< summary-bar feature_name="Compose exec" >}}

`exec` só é relevante quando `action` está definido como `sync+exec`.
Assim como os [hooks de serviço](services.md#post_start), `exec` é usado para
definir o comando a ser executado dentro do contêiner após sua inicialização.

- `command`: especifica o comando a ser executado após a inicialização do
  contêiner.
  Este atributo é obrigatório e você pode optar por usar o formato shell ou o
  formato exec.
- `user`: o usuário que executará o comando.
  Se não for definido, o comando será executado com o mesmo usuário do comando
  principal do serviço.
- `privileged`: permite que o comando seja executado com acesso privilegiado.
- `working_dir`: o diretório de trabalho no qual o comando será executado.
  Se não for definido, ele será executado no mesmo diretório de trabalho do
  comando principal do serviço.
- `environment`: define as variáveis de ambiente para executar o comando.
  Embora o comando herde as variáveis de ambiente definidas para o comando
  principal do serviço, esta seção permite adicionar novas variáveis ou
  substituir as existentes.

```yaml
services:
  frontend:
    image: ...
    develop:
      watch:
        # sincroniza o conteúdo e executa o comando para recarregar o serviço
        # sem interrupção.
        - path: ./etc/config
          action: sync+exec
          target: /etc/config/
          exec:
            command: app reload
```

#### `ignore`

O atributo `ignore` é usado para definir uma lista de padrões para caminhos a
serem ignorados.
Qualquer arquivo atualizado que corresponda a um padrão, ou que pertença a uma
pasta que corresponda a um padrão, não acionará a recriação dos serviços.
A sintaxe é a mesma do arquivo `.dockerignore`:

- `*` corresponde a 0 ou mais caracteres em um nome de arquivo.
- `?` corresponde a um único caractere em um nome de arquivo.
- `*/*` corresponde a duas pastas aninhadas com nomes arbitrários.
- `**` corresponde a um número arbitrário de pastas aninhadas.

Se o contexto de construção incluir um arquivo `.dockerignore`, os padrões neste
arquivo serão carregados como conteúdo implícito para o arquivo `ignores`, e os
valores definidos no modelo Compose serão anexados.

#### `include`

Às vezes, é mais fácil selecionar os arquivos a serem monitorados em vez de
declarar aqueles que não devem ser monitorados com `ignore`.

O atributo `include` é usado para definir um padrão, ou uma lista de padrões,
para caminhos a serem considerados para monitoramento.
Somente os arquivos que corresponderem a esses padrões serão considerados ao
aplicar uma regra de monitoramento.
A sintaxe é a mesma que a de `ignore`.

```yaml
services:
  backend:
    image: example/backend
    develop:
      watch:
        # reconstrói a imagem e recria o serviço
        - path: ./src
          include: "*.go"
          action: rebuild
```

> [!NOTE]
>
> Em muitos casos, os padrões `include` começam com um caractere curinga (`*`).
> Isso tem um significado especial na sintaxe YAML para definir um
> [nó de apelido](https://yaml.org/spec/1.2.2/#alias-nodes), portanto, você
> precisa envolver a expressão do padrão com aspas.

#### `initial_sync`

Ao usar ações `sync+x`, pode ser útil garantir que os arquivos dentro dos
contêineres estejam atualizados no início de uma nova sessão de monitoramento.

O atributo `initial_sync` instrui o runtime do Compose, caso os contêineres para
o serviço já existam, a verificar se os arquivos do atributo `path` estão
sincronizados dentro dos contêineres do serviço.

#### `path`

O atributo `path` define o caminho para o código-fonte (relativo ao diretório do
projeto) a ser monitorado em busca de alterações.
Atualizações em qualquer arquivo dentro do caminho, que não corresponda a
nenhuma regra `ignore`, acionam a ação configurada.

#### `target`

O atributo `target` só se aplica quando `action` está configurado para `sync`.
Arquivos dentro de `path` que sofreram alterações são sincronizados com o
sistema de arquivos do contêiner, garantindo que este esteja sempre em execução
com conteúdo atualizado.
