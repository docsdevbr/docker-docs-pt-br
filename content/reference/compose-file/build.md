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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/build.md
source_revision: cbceafed09b61a5ca148211aa6db1e5ae2349e21
translation_status: ready

title: Especificação de Build do Compose
description: Saiba mais sobre a Especificação de Build do Compose.
keywords: >-
  compose, especificação do compose, referência de arquivos do compose,
  especificação de build do compose
aliases:
  - /compose/compose-file/build/
weight: 130
---

{{% include "compose/build.md" %}}

Quando `build` é especificado como uma string, todo o caminho é usado como um
contexto Docker para executar uma construção Docker, procurando por um
`Dockerfile` canônico na raiz do diretório.

Quando `build` é especificado como uma estrutura detalhada, argumentos de
construção podem ser especificados, incluindo um local alternativo para o
`Dockerfile`.

Em ambos os casos, o caminho pode ser absoluto ou relativo.
Se for relativo, ele é resolvido a partir do diretório que contém seu arquivo
Compose.
Se for absoluto, o caminho impede que o arquivo Compose seja portátil, então o
Compose exibe um aviso.

## Usando `build` e `image`

Quando o Compose se depara com uma subseção `build` para um serviço e um
atributo `image`, ele segue as regras definidas pelo atributo
[`pull_policy`](services.md#pull_policy).

Se `pull_policy` estiver ausente da definição do serviço, o Compose tenta
primeiro obter a imagem e, em seguida, compila a partir do código-fonte caso a
imagem não seja encontrada no registro ou no cache da plataforma.

## Publicando imagens construídas

O Compose com suporte a `build` oferece uma opção para enviar imagens
construídas para um registro.
Ao fazer isso, ele não tenta enviar imagens de serviço sem um atributo `image`.
O Compose alerta sobre a ausência do atributo `image`, o que impede o envio de
imagens.

## Exemplo ilustrativo

O exemplo a seguir ilustra os conceitos da Especificação de Build do Compose com
uma aplicação de exemplo concreto.
O exemplo não é normativo.

```yaml
services:
  frontend:
    image: exemplo/webapp
    build: ./webapp

  backend:
    image: exemplo/database
    build:
      context: backend
      dockerfile: ../backend.Dockerfile

  custom:
    build: ~/custom
```

Quando usado para construir imagens de serviço a partir do código-fonte, o
arquivo Compose cria três imagens Docker:

- `exemplo/webapp`: uma imagem Docker é construída usando o subdiretório
  `webapp`, dentro da pasta do arquivo Compose, como contexto de construção do
  Docker.
  A ausência de um arquivo `Dockerfile` dentro desta pasta retorna um erro.
- `exemplo/database`: uma imagem Docker é construída usando o subdiretório`
  `backend dentro da pasta do arquivo Compose.
  O arquivo `backend.Dockerfile` é usado para definir as etapas de construção;
  este arquivo é pesquisado em relação ao caminho de contexto, o que significa
  que `..` resolve para a pasta do arquivo Compose, portanto,
  `backend.Dockerfile` é um arquivo irmão.
- Uma imagem Docker é construída usando o diretório `custom` com o `$HOME` do
  usuário como contexto do Docker.
  O Compose exibe um aviso sobre o caminho não portátil usado para construir a
  imagem.

Ao enviar as imagens, tanto a imagem `exemplo/webapp` quanto a imagem
`exemplo/database` do Docker são enviadas para o registro padrão.
A imagem de serviço `custom` é ignorada, pois nenhum atributo `image` foi
definido e o Compose exibe um aviso sobre a ausência desse atributo.

## Atributos

A subseção `build` define as opções de configuração aplicadas pelo Compose para
construir imagens Docker a partir do código-fonte.

`build` pode ser especificado como uma string contendo o caminho para o contexto
de construção ou como uma estrutura detalhada:

Usando a sintaxe de string, apenas o contexto de construção pode ser configurado
como:

- Um caminho relativo para a pasta do arquivo Compose.
  Este caminho deve ser um diretório e deve conter um arquivo `Dockerfile`.

  ```yml
  services:
    webapp:
      build: ./dir
  ```

- Uma URL de repositório Git.
  As URLs do Git aceitam configuração de contexto em sua seção de fragmento,
  separada por dois pontos (`:`).
  A primeira parte representa a referência que o Git extrai e pode ser um
  branch, uma tag ou uma referência remota.
  A segunda parte representa um subdiretório dentro do repositório usado como
  contexto de construção.

  ```yml
  services:
    webapp:
      build: https://github.com/minhacompanhia/exemplo.git#branch_ou_tag:subdiretorio
  ```

Alternativamente, `build` pode ser um objeto com campos definidos da seguinte
forma:

### `additional_contexts`

{{< summary-bar feature_name="Build additional contexts" >}}

`additional_contexts` define uma lista de contextos nomeados que o construtor de
imagens deve usar durante a construção da imagem.

`additional_contexts` pode ser um mapeamento ou uma lista:

```yml
build:
  context: .
  additional_contexts:
    - resources=/caminho/para/resources
    - app=docker-image://minha-app:latest
    - source=https://github.com/meuusuario/projeto.git
```

```yml
build:
  context: .
  additional_contexts:
    resources: /caminho/para/resources
    app: docker-image://minha-app:latest
    source: https://github.com/meuusuario/projeto.git
```

Quando usado como uma lista, a sintaxe segue o formato `NOME=VALOR`, onde
`VALOR` é uma string.
A validação além disso é responsabilidade do construtor da imagem (e é
específica do construtor).
O Compose suporta pelo menos caminhos absolutos e relativos para um diretório e
URLs de repositório Git, como faz o [context](#context).
Outros tipos de contexto devem ser prefixados para evitar ambiguidade com um
prefixo `tipo://`.

O Compose alerta se o construtor da imagem não suporta contextos adicionais e
pode listar os contextos não usados.

Consulte a documentação de referência para
[`docker buildx build --build-context`](https://github.com/docker/buildx/blob/master/docs/reference/buildx_build.md#-additional-build-contexts---build-context)
para exemplos de uso.

`additional_contexts` também pode se referir a uma imagem criada por outro
serviço.
Isso permite que uma imagem de serviço seja construída usando outra imagem de
serviço como imagem base e que camadas sejam compartilhadas entre imagens de
serviço.

```yaml
services:
  base:
    build:
      context: .
      dockerfile_inline: |
        FROM alpine
        RUN ...
  my-service:
    build:
      context: .
      dockerfile_inline: |
        FROM base # imagem criada para base de serviços
        RUN ...
      additional_contexts:
        base: service:base
```

### `args`

`args` define os argumentos de construção, ou seja, os valores de `ARG` do
Dockerfile.

Usando o seguinte Dockerfile como exemplo:

```Dockerfile
ARG GIT_COMMIT
RUN echo "Baseado no commit: $GIT_COMMIT"
```

`args` pode ser definido no arquivo Compose, na chave `build`, para definir
`GIT_COMMIT`.
`args` pode ser definido como um mapeamento ou uma lista:

```yml
build:
  context: .
  args:
    GIT_COMMIT: cdc3b19
```

```yml
build:
  context: .
  args:
    - GIT_COMMIT=cdc3b19
```

Os valores podem ser omitidos ao especificar um argumento de construção, caso em
que seu valor no momento da construção deve ser obtido por interação da pessoa
usuária.
Caso contrário, o argumento de construção não será definido ao construir a
imagem Docker.

```yml
args:
  - GIT_COMMIT
```

### `cache_from`

`cache_from` define uma lista de fontes que o construtor de imagens deve usar
para resolução de cache.

A sintaxe de localização do cache segue o formato global
`[NOME|tipo=TIPO[,CHAVE=VALOR]]`.
O simples `NOME` é, na verdade, uma notação abreviada para
`tipo=registro,ref=NOME`.

As implementações do Compose Build podem suportar tipos personalizados.
A especificação do Compose define os tipos canônicos que devem ser suportados:

- `registry` para recuperar o cache de cosntrução de uma imagem OCI definida
  pela chave `ref`.

```yml
build:
  context: .
  cache_from:
    - alpine:latest
    - type=local,src=caminho/para/o/cache
    - type=gha
```

Os caches não suportados são ignorados e não impedem a criação de imagens.

### `cache_to`

`cache_to` define uma lista de locais de exportação a serem usados para
compartilhar o cache de construção com construções futuras.

```yml
build:
  context: .
  cache_to:
    - usuario/app:cache
    - type=local,dest=caminho/para/o/cache
```

O destino do cache é definido usando a mesma sintaxe `type=TYPE[,KEY=VALUE]`
definida por [`cache_from`](#cache_from).

Caches não suportados são ignorados e não impedem a criação de imagens.

### `context`

`context` define o caminho para um diretório que contém um Dockerfile ou a URL
de um repositório Git.

Quando o valor fornecido é um caminho relativo, ele é interpretado como relativo
ao diretório do projeto.
O Compose alerta sobre caminhos absolutos usados para definir o contexto de
construção, pois esses caminhos impedem que o arquivo Compose seja portátil.

```yml
build:
  context: ./dir
```

```yml
services:
  webapp:
    build: https://github.com/minhacompanhia/webapp.git
```

Se não for definido explicitamente, `context` assume como padrão o diretório do
projeto (`.`).

### `dockerfile`

`dockerfile` define um Dockerfile alternativo.
Um caminho relativo é resolvido a partir do contexto de construção.
O Compose alerta sobre o caminho absoluto usado para definir o Dockerfile, pois
isso impede que os arquivos Compose sejam portáteis.

Quando definido, o atributo `dockerfile_inline` não é permitido e o Compose
rejeita qualquer arquivo Compose que tenha ambos definidos.

```yml
build:
  context: .
  dockerfile: webapp.Dockerfile
```

### `dockerfile_inline`

{{< summary-bar feature_name="Build dockerfile inline" >}}

`dockerfile_inline` define o conteúdo do Dockerfile como uma string embutida em
um arquivo Compose.
Quando definido, o atributo `dockerfile` não é permitido e o Compose rejeita
qualquer arquivo Compose que tenha ambos definidos.

Recomenda-se o uso da sintaxe de string multilinha YAML para definir o conteúdo
do Dockerfile:

```yml
build:
  context: .
  dockerfile_inline: |
    FROM baseimage
    RUN some command
```

### `entitlements`

{{< summary-bar feature_name="Build entitlements" >}}

`entitlements` define privilégios adicionais que serão permitidos durante a
construção.

```yaml
entitlements:
  - network.host
  - security.insecure
```

### `extra_hosts`

`extra_hosts` adiciona mapeamentos de nomes de host em tempo de construção.
Use a mesma sintaxe de [`extra_hosts`](services.md#extra_hosts).

```yml
extra_hosts:
  - "algumhost=162.242.195.82"
  - "outrohost=50.31.209.229"
  - "meuhostv6=::1"
```

Endereços IPv6 podem ser delimitados por colchetes, por exemplo:

```yml
extra_hosts:
  - "meuhostv6=[::1]"
```

O separador `=` é preferencial, mas `:` também pode ser usado.
Introduzido na versão
[2.24.1](https://github.com/docker/compose/releases/tag/v2.24.1) do Docker
Compose.
Por exemplo:

```yml
extra_hosts:
  - "algumhost:162.242.195.82"
  - "meuhostv6:::1"
```

O Compose cria uma entrada correspondente com o endereço IP e o nome do host na
configuração de rede do contêiner.
Isso significa que, para o Linux, o arquivo `/etc/hosts` receberá linhas
adicionais:

```text
162.242.195.82  algumhost
50.31.209.229   outrohost
::1             meuhostv6
```

### `isolation`

`isolation` especifica a tecnologia de isolamento de contêiner de uma
construção.
Assim como [isolation](services.md#isolation), os valores suportados são
específicos da plataforma.

### `labels`

`labels` adiciona metadados à imagem resultante.
`labels` pode ser definido como uma matriz ou um mapa.

Recomenda-se o uso da notação DNS reversa para evitar conflitos entre seus
rótulos e outros softwares.

```yml
build:
  context: .
  labels:
    com.example.description: "Aplicação web de contabilidade"
    com.example.department: "Finanças"
    com.example.rotulo-com-valor-vazio: ""
```

```yml
build:
  context: .
  labels:
    - "com.example.description=Aplicação web de contabilidade"
    - "com.example.department=Finanças"
    - "com.example.rotulo-com-valor-vazio"
```

### `network`

Configure a rede à qual os contêineres se conectam para as instruções `RUN`
durante a construção.

```yaml
build:
  context: .
  network: host
```

```yaml
build:
  context: .
  network: rede_personalizada_1
```

Use `none` para desativar a rede durante a construção:

```yaml
build:
  context: .
  network: none
```

### `no_cache`

`no_cache` desativa o cache do construtor de imagens e força uma reconstrução
completa a partir do código-fonte para todas as camadas da imagem.
Isso se aplica somente às camadas declaradas no Dockerfile; as imagens
referenciadas podem ser recuperadas do armazenamento de imagens local sempre que
a tag for atualizada no registro (consulte [pull](#pull)).

### `platforms`

`platforms` define uma lista de [plataformas](services.md#platform) de destino.

```yml
build:
  context: "."
  platforms:
    - "linux/amd64"
    - "linux/arm64"
```

Quando o atributo `platforms` é omitido, o Compose inclui a plataforma do
serviço na lista de plataformas de destino de construção padrão.

Quando o atributo `platforms` é definido, o Compose inclui a plataforma do
serviço; caso contrário, as pessoas usuárias não poderão executar as imagens que
construíram.

O Compose reporta um erro nos seguintes casos:

- Quando a lista contém várias plataformas, mas a implementação não é capaz de
  armazenar imagens multiplataforma.
- Quando a lista contém uma plataforma não suportada.

  ```yml
  build:
    context: "."
    platforms:
      - "linux/amd64"
      - "naosuportada/naosuportada"
  ```

- Quando a lista não estiver vazia e não contiver a plataforma do serviço.

  ```yml
  services:
    frontend:
      platform: "linux/amd64"
      build:
        context: "."
        platforms:
          - "linux/arm64"
  ```

### `privileged`

{{< summary-bar feature_name="Build privileged" >}}

`privileged` configura a imagem do serviço para ser compilada com privilégios
elevados.
O suporte e os impactos reais variam de acordo com a plataforma.

```yml
build:
  context: .
  privileged: true
```

### `provenance`

{{< summary-bar feature_name="Compose provenance" >}}

`provenance` configura o construtor para adicionar uma
[atestação de proveniência](https://slsa.dev/provenance/v0.2#schema) à imagem
publicada.

O valor pode ser um booleano para habilitar/desabilitar a atestação de
proveniência ou uma string chave=valor para definir a configuração de
proveniência.
Você pode usar isso para selecionar o nível de detalhe a ser incluído na
atestação de proveniência definindo o parâmetro `mode`.

```yaml
build:
  context: .
  provenance: true
```

```yaml
build:
  context: .
  provenance: mode=max
```

### `pull`

`pull` exige que o construtor de imagens baixe as imagens referenciadas
(diretiva `FROM` do Dockerfile), mesmo que elas já estejam disponíveis no
armazenamento de imagens local.

### `sbom`

{{< summary-bar feature_name="Compose sbom" >}}

`sbom` configura o construtor para adicionar uma
[atestação de proveniência](https://slsa.dev/provenance/v0.2#schema) à imagem
publicada.
O valor pode ser um booleano para habilitar/desabilitar a atestação sbom ou uma
string chave=valor para definir a configuração do gerador SBOM.
Isso permite que você selecione uma imagem alternativa para o gerador SBOM
(consulte
https://github.com/moby/buildkit/blob/master/docs/attestations/sbom-protocol.md).

```yaml
build:
  context: .
  sbom: true
```

```yaml
build:
  context: .
  sbom: generator=docker/scout-sbom-indexer:latest # Usa um gerador SBOM alternativo.
```

### `secrets`

`secrets` concede acesso a dados sensíveis definidos por
[secrets](services.md#secrets) para cada construção de serviço.
Duas variantes de sintaxe são suportadas: a sintaxe curta e a sintaxe longa.

O Compose reporta um erro se o segredo não estiver definido na seção
[`secrets`](secrets.md) deste arquivo Compose.

#### Sintaxe curta

A variante de sintaxe curta especifica apenas o nome do segredo.
Isso concede ao contêiner acesso ao segredo e o monta como somente leitura em
`/run/secrets/<nome_do_segredo>` dentro do contêiner.
O nome de origem e o ponto de montagem de destino são ambos definidos com o nome
do segredo.

O exemplo a seguir usa a sintaxe curta para conceder ao build do serviço
`frontend` acesso ao segredo `server-certificate`.
O valor de `server-certificate` é definido com o conteúdo do arquivo
`./server.cert`.

```yml
services:
  frontend:
    build:
      context: .
      secrets:
        - server-certificate
secrets:
  server-certificate:
    file: ./server.cert
```

#### Sintaxe longa

A sintaxe longa oferece mais granularidade na forma como o segredo é criado nos
contêineres do serviço.

- `source`: o nome do segredo como ele existe na plataforma.
- `target`: o ID do segredo conforme declarado no Dockerfile.
  O padrão é `source` se não for especificado.
- `uid` e `gid`: o uid ou gid numérico que possui o arquivo em `/run/secrets/`
  nos contêineres da tarefa do serviço.
  O valor padrão é `USER`.
- `mode`: as [permissões](https://wintelguy.com/permissions-calc.pl) para o
  arquivo a ser montado em `/run/secrets/` nos contêineres da tarefa do serviço,
  em notação octal.
  O valor padrão são permissões de leitura para todos (modo `0444`).
  O bit de escrita deve ser ignorado se definido.
  O bit de execução pode ser definido.

O exemplo a seguir define o nome do arquivo de segredo `server-certificate` como
`server.crt` dentro do contêiner, define o modo como `0440` (leitura pelo grupo)
e define o usuário e o grupo como `103`.
O valor do segredo `server-certificate` é fornecido pela plataforma por meio de
uma pesquisa e o ciclo de vida do segredo não é gerenciado diretamente pelo
Compose.

```yml
services:
  frontend:
    build:
      context: .
      secrets:
        - source: server-certificate
          target: cert # ID do segredo no Dockerfile
          uid: "103"
          gid: "103"
          mode: 0440
secrets:
  server-certificate:
    external: true
```

```dockerfile
# Dockerfile
FROM nginx
RUN --mount=type=secret,id=cert,required=true,target=/root/cert ...
```

As construções de serviço podem ter acesso a vários segredos.
Sintaxes longas e curtas para segredos podem ser usadas no mesmo arquivo
Compose.
Definir um segredo no `secrets` de nível superior não deve implicar conceder
acesso a ele a nenhuma construção de serviço.
Essa concessão deve ser explícita na especificação do serviço como o elemento de
serviço [secrets](services.md#secrets).

### `ssh`

`ssh` define as autenticações SSH que o construtor de imagem deve usar durante a
construção da imagem (por exemplo, clonar um repositório privado).

A sintaxe da propriedade `ssh` pode ser:

- `default`: permite que o construtor se conecte ao agente SSH.
- `ID=caminho`: uma definição de chave/valor de um ID e o caminho associado.
  Pode ser um arquivo [PEM](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail)
  ou o caminho para o socket do agente SSH.

```yaml
build:
  context: .
  ssh:
    - default # monta o agente SSH padrão
```

ou

```yaml
build:
  context: .
  ssh: ["default"] # monta o agente SSH padrão
```

Usando um ID personalizado `meuprojeto` com o caminho para uma chave SSH local:

```yaml
build:
  context: .
  ssh:
    - meuprojeto=~/.ssh/meuprojeto.pem
```

O construtor de imagens pode então usar isso para montar a chave SSH durante a
construção.

Por exemplo, as
[montagens SSH](https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/reference.md#run---mounttypessh)
podem ser usadas para montar a chave SSH definida por ID e acessar um recurso
protegido:

```console
RUN --mount=type=ssh,id=meuprojeto git clone ...
```

### `shm_size`

`shm_size` define o tamanho da memória compartilhada (partição `/dev/shm` no
Linux) alocada para a construção de imagens Docker.
Especifique como um valor inteiro representando o número de bytes ou como uma
string expressando um [valor em bytes](extension.md#specifying-byte-values).

```yml
build:
  context: .
  shm_size: "2gb"
```

```yaml
build:
  context: .
  shm_size: 10000000
```

### `tags`

`tags` define uma lista de mapeamentos de tags que devem ser associados à imagem
de compilação.
Esta lista é adicional à propriedade `image`
[definida na seção de serviço](services.md#image).

```yml
tags:
  - "minhaimagem:minhatag"
  - "registro/usuario/meu-repo:minha-outra-tag"
```

### `target`

`target` define o estágio a ser construído, conforme definido em um `Dockerfile`
com vários estágios.

```yml
build:
  context: .
  target: prod
```

### `ulimits`

{{< summary-bar feature_name="Build ulimits" >}}

`ulimits` substitui o `ulimits` padrão para um contêiner.
Ele é especificado como um número inteiro para um único limite ou como um
mapeamento para limites flexíveis/rígidos.

```yml
services:
  frontend:
    build:
      context: .
      ulimits:
        nproc: 65535
        nofile:
          soft: 20000
          hard: 40000
```
