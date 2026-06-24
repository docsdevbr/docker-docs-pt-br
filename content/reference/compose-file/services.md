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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/services.md
source_revision: 80faf488d21a7598b8101185bfa5ce5e38b4915b
translation_status: wip

linkTitle: Serviços
title: Defina serviços no Docker Compose
description: >-
  Explore todos os atributos que o elemento de nível superior `services` pode
  ter.
keywords: >-
  compose, especificação do compose, serviços, referência do arquivo compose
aliases:
  - /compose/compose-file/05-services/
weight: 20
---

{{% include "compose/services.md" %}}

Um arquivo Compose deve declarar um elemento de nível superior `services` como
um mapa cujas chaves são representações em string de nomes de serviços e cujos
valores são definições de serviços.
Uma definição de serviço contém a configuração aplicada a cada contêiner de
serviço.

Cada serviço também pode incluir uma seção `build`, que define como criar a
imagem Docker para o serviço.
O Compose oferece suporte à criação de imagens Docker usando essa definição de
serviço.
Se não for usada, a seção `build` é ignorada e o arquivo Compose ainda é
considerado válido.
O suporte à criação é um aspecto opcional da Especificação do Compose e é
descrito em detalhes na documentação da
[Especificação de Build do Compose](build.md).

Cada serviço define restrições e requisitos de tempo de execução para executar
seus contêineres.
A seção `deploy` agrupa essas restrições e permite que a plataforma ajuste a
estratégia de implantação para melhor atender às necessidades dos contêineres
com os recursos disponíveis.
O suporte à implantação é um aspecto opcional da Especificação do Compose e é
descrito em detalhes na documentação da
[Especificação de Deploy do Compose](deploy.md).
Caso não seja implementada, a seção `deploy` é ignorada e o arquivo Compose
ainda é considerado válido.

## Exemplos

### Exemplo simples

O exemplo a seguir demonstra como definir dois serviços simples, definir suas
imagens, mapear portas e configurar variáveis de ambiente básicas usando o
Docker Compose.

```yaml
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"

  db:
    image: postgres:18
    environment:
      POSTGRES_USER: example
      POSTGRES_DB: exampledb
```

### Exemplo avançado

No exemplo a seguir, o serviço `proxy` usa a imagem do Nginx, monta um arquivo
de configuração local do Nginx no contêiner, expõe a porta `80` e depende do
serviço `backend`.

O serviço `backend` constrói uma imagem a partir do Dockerfile localizado no
diretório `backend`, configurado para realizar a construção no estágio
`builder`.

```yaml
services:
  proxy:
    image: nginx
    volumes:
      - type: bind
        source: ./proxy/nginx.conf
        target: /etc/nginx/conf.d/default.conf
        read_only: true
    ports:
      - 80:80
    depends_on:
      - backend

  backend:
    build:
      context: backend
      target: builder
```

Para mais exemplos de arquivos Compose, explore os
[exemplos do Awesome Compose](https://github.com/docker/awesome-compose).

## Atributos

<!-- vale off(Docker.HeadingSentenceCase.yml) -->

### `annotations`

`annotations` define anotações para o contêiner.
`annotations` pode usar um array ou um mapa.

```yml
annotations:
  com.example.foo: bar
```

```yml
annotations:
  - com.example.foo=bar
```

### `attach`

{{< summary-bar feature_name="Compose attach" >}}

Quando `attach` está definido como `false`, o Compose não coleta os logs do
serviço, a menos que você solicite isso explicitamente.

A configuração padrão do serviço é `attach: true`.

### `build`

`build` especifica a configuração de build para criar uma imagem de contêiner a
partir do código-fonte, conforme definido na
[Especificação de Build do Compose](build.md).

### `blkio_config`

`blkio_config` define um conjunto de opções de configuração para estabelecer
limites de E/S de bloco para um serviço.

```yml
services:
  foo:
    image: busybox
    blkio_config:
       weight: 300
       weight_device:
         - path: /dev/sda
           weight: 400
       device_read_bps:
         - path: /dev/sdb
           rate: '12mb'
       device_read_iops:
         - path: /dev/sdb
           rate: 120
       device_write_bps:
         - path: /dev/sdb
           rate: '1024k'
       device_write_iops:
         - path: /dev/sdb
           rate: 30
```

#### `device_read_bps`, `device_write_bps`

Define um limite em bytes por segundo para operações de leitura/escrita em um
dispositivo específico.
Cada item da lista deve conter duas chaves:

- `path`: Define o caminho simbólico para o dispositivo afetado.
- `rate`: Pode ser um valor inteiro representando o número de bytes ou uma
  string expressando um valor em bytes.

#### `device_read_iops`, `device_write_iops`

Define um limite em operações por segundo para operações de leitura/escrita em
um dispositivo específico.
Cada item da lista deve conter duas chaves:

- `path`: Define o caminho simbólico para o dispositivo afetado.
- `rate`: Um valor inteiro representando o número permitido de operações por
  segundo.

#### `weight`

Modifica a proporção de largura de banda alocada a um serviço em relação a
outros serviços.
Assume um valor inteiro entre 10 e 1000, sendo 500 o valor padrão.

#### `weight_device`

Ajusta a alocação de largura de banda por dispositivo.
Cada item da lista deve ter duas chaves:

- `path`: Define o caminho simbólico para o dispositivo afetado.
- `weight`: Um valor inteiro entre 10 e 1000.

### `cpu_count`

`cpu_count` define o número de CPUs utilizáveis para o contêiner de serviço.

### `cpu_percent`

`cpu_percent` define a porcentagem utilizável das CPUs disponíveis.

### `cpu_shares`

`cpu_shares` define, como um valor inteiro, o peso relativo de CPU de um
contêiner de serviço em relação a outros contêineres.

### `cpu_period`

`cpu_period` configura o período do CFS (Completely Fair Scheduler) da CPU
quando uma plataforma é baseada no kernel Linux.

### `cpu_quota`

`cpu_quota` configura a cota de CPU do CFS (Completely Fair Scheduler) quando a
plataforma é baseada no kernel Linux.

### `cpu_rt_runtime`

`cpu_rt_runtime` configura os parâmetros de alocação de CPU para plataformas com
suporte a escalonador de tempo real.
Pode ser um valor inteiro em microssegundos ou uma
[duração](extension.md#specifying-durations).

```yml
 cpu_rt_runtime: '400ms'
 cpu_rt_runtime: '95000'
```

### `cpu_rt_period`

`cpu_rt_period` configura os parâmetros de alocação de CPU para plataformas com
suporte a escalonador de tempo real.
Pode ser um valor inteiro usando microssegundos como unidade ou uma
[duração](extension.md#specifying-durations).

```yml
 cpu_rt_period: '1400us'
 cpu_rt_period: '11000'
```

### `cpus`

`cpus` define o número de CPUs (potencialmente virtuais) a serem alocadas para
os contêineres de serviço.
Este é um número fracionário.
`0.000` significa sem limite.

Quando definido, `cpus` deve ser consistente com o atributo `cpus` na
[Especificação de Deploy](deploy.md#cpus).

### `cpuset`

`cpuset` define as CPUs específicas nas quais a execução é permitida.
Pode ser um intervalo, como `0-3`, ou uma lista, como `0,1`.

### `cap_add`

`cap_add` especifica
[capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html)
adicionais do contêiner como strings.

```yaml
cap_add:
  - ALL
```

### `cap_drop`

`cap_drop` especifica as
[capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html) do
contêiner a serem descartadas, na forma de strings.

```yaml
cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```

### `cgroup`

{{< summary-bar feature_name="Compose cgroup" >}}

`cgroup` especifica o namespace de cgroup ao qual se associar.
Quando não definido, cabe ao runtime do contêiner decidir qual namespace de
cgroup usar, caso haja suporte.

- `host`: Executa o contêiner no namespace de cgroup do runtime do contêiner.
- `private`: Executa o contêiner em seu próprio namespace de cgroup privado.

### `cgroup_parent`

`cgroup_parent` especifica um
[cgroup](https://man7.org/linux/man-pages/man7/cgroups.7.html) pai opcional para
o contêiner.

```yaml
cgroup_parent: m-executor-abcd
```

### `command`

`command` substitui o comando padrão declarado pela imagem do contêiner, por
exemplo, aquele definido pelo `CMD` do Dockerfile.

```yaml
command: bundle exec thin -p 3000
```

Se o valor for `null`, o comando padrão da imagem é utilizado.

Se o valor for `[]` (lista vazia) ou `''` (string vazia), o comando padrão
declarado pela imagem é ignorado ou, em outras palavras, substituído por um
comando vazio.

> [!NOTE]
>
> Ao contrário da instrução `CMD` em um Dockerfile, o campo `command` não é
> executado automaticamente no contexto da instrução
> [`SHELL`](/reference/dockerfile.md#shell-form) definida na imagem.
> Se o seu `command` depender de recursos específicos do shell, como a expansão
> de variáveis de ambiente, você precisará executá-lo explicitamente dentro de
> um shell.
> Por exemplo:
>
> ```yaml
> command: /bin/sh -c 'echo "hello $$HOSTNAME"'
> ```

O valor também pode ser uma lista, semelhante à
[sintaxe exec-form](/reference/dockerfile.md#exec-form)
utilizada pelo [Dockerfile](/reference/dockerfile.md#exec-form).

### `configs`

As `configs` permitem que os serviços adaptem seu comportamento sem a
necessidade de reconstruir uma imagem Docker.
Os serviços só podem acessar as `configs` quando isso é explicitamente concedido
por meio do atributo `configs`.
Há suporte para duas variantes de sintaxe.

O Compose gera um erro se a `config` não existir na plataforma ou não estiver
definida no [elemento de nível superior `configs`](configs.md) do arquivo
Compose.

Existem duas sintaxes definidas para `configs`: uma sintaxe curta e uma sintaxe
longa.

Você pode conceder a um serviço acesso a múltiplas `configs` e combinar as
sintaxes longa e curta.

#### Sintaxe curta

A variante de sintaxe curta especifica apenas o nome da configuração.
Isso concede ao container acesso à configuração e a monta como arquivos no
sistema de arquivos do container do serviço.
O local do ponto de montagem dentro do container é, por padrão,
`/<nome_da_config>` em contêineres Linux e `C:\<nome-da-config>` em contêineres
Windows.

O exemplo a seguir usa a sintaxe curta para conceder ao serviço `redis`acesso às
configurações `my_config` e `my_other_config`.
O valor de `my_config` é definido como o conteúdo do arquivo `./my_config.txt`,
e `my_other_config` é definida como um recurso externo, o que significa que ela
já foi definida na plataforma.
Se a configuração externa não existir, a implantação falha.

```yml
services:
  redis:
    image: redis:latest
    configs:
      - my_config
      - my_other_config
configs:
  my_config:
    file: ./my_config.txt
  my_other_config:
    external: true
```

#### Sintaxe longa

A sintaxe longa oferece maior granularidade na forma como a configuração é
criada dentro dos contêineres de tarefa do serviço.

- `source`: O nome da configuração conforme ela existe na plataforma.
- `target`: O caminho e o nome do arquivo a ser montado nos contêineres de
  tarefa do serviço.
  O padrão é `/<source>` se não for especificado.
- `uid` e `gid`: O UID ou GID numérico do proprietário do arquivo de
  configuração montado dentro dos contêineres de tarefa do serviço.
- `mode`: As [permissões](https://wintelguy.com/permissions-calc.pl) do arquivo
  montado dentro dos contêineres de tarefa do serviço, em notação octal.
  O valor padrão é de leitura para todos (`0444`).
  O bit de escrita deve ser ignorado.
  O bit de execução pode ser definido.

O exemplo a seguir define o nome de `my_config` como `redis_config` dentro do
contêiner, define o modo como `0440` (leitura pelo grupo) e define o usuário e o
grupo como `103`.
O serviço `redis` não tem acesso à configuração `my_other_config`.

```yml
services:
  redis:
    image: redis:latest
    configs:
      - source: my_config
        target: /redis_config
        uid: "103"
        gid: "103"
        mode: 0440
configs:
  my_config:
    external: true
  my_other_config:
    external: true
```

### `container_name`

`container_name` é uma string que especifica um nome de contêiner personalizado,
em vez de um nome gerado por padrão.

```yml
container_name: meu-conteiner-web
```

O Compose não escala um serviço além de um único contêiner se o arquivo Compose
especificar um `container_name`.
Tentar fazer isso resulta em um erro.

`container_name` segue o formato de expressão regular
`[a-zA-Z0-9][a-zA-Z0-9_.-]+`.

### `credential_spec`

`credential_spec` configura a especificação de credenciais para uma conta de
serviço gerenciada.

Se você tiver serviços que usam contêineres do Windows, poderá usar os
protocolos `file:` e `registry:` para `credential_spec`.
O Compose também oferece suporte a protocolos adicionais para casos de uso
personalizados.

O `credential_spec` deve estar no formato `file://<nome-do-arquivo>` ou `registry://<nome-do-valor>`.

```yml
credential_spec:
  file: my-credential-spec.json
```

Ao usar `registry:`, a especificação de credenciais é lida a partir do Registro
do Windows no host do daemon.
Um valor de registro com o nome especificado deve estar localizado em:

```bash
HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Containers\CredentialSpecs
```

O exemplo a seguir carrega a especificação de credenciais a partir de um valor
chamado `my-credential-spec` no registro:

```yml
credential_spec:
  registry: my-credential-spec
```

#### Exemplo de configuração de gMSA

Ao configurar uma especificação de credencial gMSA para um serviço, você só
precisa especificar uma especificação de credencial com `config`, conforme
mostrado no exemplo a seguir:

```yml
services:
  myservice:
    image: myimage:latest
    credential_spec:
      config: my_credential_spec

configs:
  my_credentials_spec:
    file: ./my-credential-spec.json
```

### `depends_on`

{{% include "compose/services-depends-on.md" %}}

#### Short syntax

The short syntax variant only specifies service names of the dependencies.
Service dependencies cause the following behaviors:

- Compose creates services in dependency order. In the following
  example, `db` and `redis` are created before `web`.

- Compose removes services in dependency order. In the following
  example, `web` is removed before `db` and `redis`.

Simple example:

```yml
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres:18
```

Compose guarantees dependency services have been started before
starting a dependent service.
With short syntax, Compose does not wait for dependency services to be "healthy" before
starting a dependent service.

#### Long syntax

The long form syntax enables the configuration of additional fields that can't be
expressed in the short form.

- `restart`: When set to `true` Compose restarts this service after it updates the dependency service.
  This applies to an explicit restart controlled by a Compose operation, and excludes automated restart by the container runtime
  after the container dies. Introduced in Docker Compose version [2.17.0](https://github.com/docker/compose/releases/tag/v2.17.0).

- `condition`: Sets the condition under which dependency is considered satisfied
  - `service_started`: An equivalent of the short syntax described previously
  - `service_healthy`: Specifies that a dependency is expected to be "healthy"
    (as indicated by [`healthcheck`](#healthcheck)) before starting a dependent
    service.
  - `service_completed_successfully`: Specifies that a dependency is expected to run
    to successful completion before starting a dependent service.
- `required`: When set to `false` Compose only warns you when the dependency service isn't started or available. If it's not defined
    the default value of `required` is `true`. Introduced in Docker Compose version [2.20.0](https://github.com/docker/compose/releases/tag/v2.20.0).

Service dependencies cause the following behaviors:

- Compose creates services in dependency order. In the following
  example, `db` and `redis` are created before `web`.

- Compose waits for healthchecks to pass on dependencies
  marked with `service_healthy`. In the following example, `db` is expected to
  be "healthy" before `web` is created.

- Compose removes services in dependency order. In the following
  example, `web` is removed before `db` and `redis`.

```yml
services:
  web:
    build: .
    depends_on:
      db:
        condition: service_healthy
        restart: true
      redis:
        condition: service_started
  redis:
    image: redis
  db:
    image: postgres:18
```

Compose guarantees dependency services are started before
starting a dependent service.
Compose guarantees dependency services marked with
`service_healthy` are "healthy" before starting a dependent service.

### `deploy`

`deploy` specifies the configuration for the deployment and lifecycle of services, as defined [in the Compose Deploy Specification](deploy.md).

### `develop`

{{< summary-bar feature_name="Compose develop" >}}

`develop` specifies the development configuration for maintaining a container in sync with source, as defined in the [Development Section](develop.md).

### `device_cgroup_rules`

`device_cgroup_rules` defines a list of device cgroup rules for this container.
The format is the same format the Linux kernel specifies in the [Control Groups
Device Whitelist Controller](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v1/devices.html).

```yml
device_cgroup_rules:
  - 'c 1:3 mr'
  - 'a 7:* rmw'
```

### `devices`

`devices` defines a list of device mappings for created containers in the form of
`HOST_PATH:CONTAINER_PATH[:CGROUP_PERMISSIONS]`.

```yml
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
  - "/dev/sda:/dev/xvda:rwm"
```

`devices` can also rely on the [CDI](https://github.com/cncf-tags/container-device-interface) syntax to let the container runtime select a device:

```yml
devices:
  - "vendor1.com/device=gpu"
```

### `dns`

`dns` defines custom DNS servers to set on the container network interface configuration. It can be a single value or a list.

```yml
dns: 8.8.8.8
```

```yml
dns:
  - 8.8.8.8
  - 9.9.9.9
```

### `dns_opt`

`dns_opt` list custom DNS options to be passed to the container’s DNS resolver (`/etc/resolv.conf` file on Linux).

```yml
dns_opt:
  - use-vc
  - no-tld-query
```

### `dns_search`

`dns_search` defines custom DNS search domains to set on container network interface configuration. It can be a single value or a list.

```yml
dns_search: example.com
```

```yml
dns_search:
  - dc1.example.com
  - dc2.example.com
```

### `domainname`

`domainname` declares a custom domain name to use for the service container. It must be a valid RFC 1123 hostname.

### `driver_opts`

{{< summary-bar feature_name="Compose driver opts" >}}

`driver_opts` specifies a list of options as key-value pairs to pass to the driver. These options are
driver-dependent.

```yml
services:
  app:
    networks:
      app_net:
        driver_opts:
          com.docker.network.bridge.host_binding_ipv4: "127.0.0.1"
```

Consult the [network drivers documentation](/manuals/engine/network/_index.md) for more information.

### `entrypoint`

`entrypoint` declares the default entrypoint for the service container.
This overrides the `ENTRYPOINT` instruction from the service's Dockerfile.

If `entrypoint` is non-null, Compose ignores any default command from the image, for example the `CMD`
instruction in the Dockerfile.

See also [`command`](#command) to set or override the default command to be executed by the entrypoint process.

In its short form, the value can be defined as a string:
```yml
entrypoint: /code/entrypoint.sh
```

Alternatively, the value can also be a list, in a manner similar to the
[Dockerfile](https://docs.docker.com/reference/dockerfile/#cmd):

```yml
entrypoint:
  - php
  - -d
  - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
  - -d
  - memory_limit=-1
  - vendor/bin/phpunit
```

If the value is `null`, the default entrypoint from the image is used.

If the value is `[]` (empty list) or `''` (empty string), the default entrypoint declared by the image is ignored, or in other words, overridden to be empty.

### `env_file`

{{% include "compose/services-env-file.md" %}}

```yml
env_file: .env
```

Relative paths are resolved from the Compose file's parent folder. As absolute paths prevent the Compose
file from being portable, Compose warns you when such a path is used to set `env_file`.

Environment variables declared in the [`environment`](#environment) section override these values. This holds true even if those values are
empty or undefined.

`env_file` can also be a list. The files in the list are processed from the top down. For the same variable
specified in two environment files, the value from the last file in the list stands.

```yml
env_file:
  - ./a.env
  - ./b.env
```

List elements can also be declared as a mapping, which then lets you set additional
attributes.

#### `required`

{{< summary-bar feature_name="Compose required" >}}

The `required` attribute defaults to `true`. When `required` is set to `false` and the `.env` file is missing, Compose silently ignores the entry.

```yml
env_file:
  - path: ./default.env
    required: true # default
  - path: ./override.env
    required: false
```

#### `format`

{{< summary-bar feature_name="Compose format" >}}

The `format` attribute lets you use an alternative file format for the `env_file`. When not set, `env_file` is parsed according to the Compose rules outlined in [`Env_file` format](#env_file-format).

`raw` format lets you use an `env_file` with key=value items, but without any attempt from Compose to parse the value for interpolation.
This let you pass values as-is, including quotes and `$` signs.

```yml
env_file:
  - path: ./default.env
    format: raw
```

#### `Env_file` format

Each line in an `.env` file must be in `VAR[=[VAL]]` format. The following syntax rules apply:

- Lines beginning with `#` are processed as comments and ignored.
- Blank lines are ignored.
- Unquoted and double-quoted (`"`) values have [Interpolation](interpolation.md) applied.
- Each line represents a key-value pair. Values can optionally be quoted.
- Delimiter separating key and value can be either `=` or `:`.
- Spaces before and after value are ignored.
  - `VAR=VAL` -> `VAL`
  - `VAR="VAL"` -> `VAL`
  - `VAR='VAL'` -> `VAL`
  - `VAR: VAL` -> `VAL`
  - `VAR = VAL  ` -> `VAL` <!-- markdownlint-disable-line no-space-in-code -->
- Inline comments for unquoted values must be preceded with a space.
  - `VAR=VAL # comment` -> `VAL`
  - `VAR=VAL# not a comment` -> `VAL# not a comment`
- Inline comments for quoted values must follow the closing quote.
  - `VAR="VAL # not a comment"` -> `VAL # not a comment`
  - `VAR="VAL" # comment` -> `VAL`
- Single-quoted (`'`) values are used literally.
  - `VAR='$OTHER'` -> `$OTHER`
  - `VAR='${OTHER}'` -> `${OTHER}`
- Quotes can be escaped with `\`.
  - `VAR='Let\'s go!'` -> `Let's go!`
  - `VAR="{\"hello\": \"json\"}"` -> `{"hello": "json"}`
- Common shell escape sequences including `\n`, `\r`, `\t`, and `\\` are supported in double-quoted values.
  - `VAR="some\tvalue"` -> `some  value`
  - `VAR='some\tvalue'` -> `some\tvalue`
  - `VAR=some\tvalue` -> `some\tvalue`

`VAL` may be omitted, in such cases the variable value is an empty string.
`=VAL` may be omitted, in such cases the variable is unset.

```bash
# Set Rails/Rack environment
RACK_ENV=development
VAR="quoted"
```

### `environment`

{{% include "compose/services-environment.md" %}}

Environment variables can be declared by a single key (no value to equals sign). In this case Compose
relies on you to resolve the value. If the value is not resolved, the variable
is unset and is removed from the service container environment.

Map syntax:

```yml
environment:
  RACK_ENV: development
  SHOW: "true"
  USER_INPUT:
```

Array syntax:

```yml
environment:
  - RACK_ENV=development
  - SHOW=true
  - USER_INPUT
```

When both `env_file` and `environment` are set for a service, values set by `environment` have precedence.

### `expose`

`expose` defines the (incoming) port or a range of ports that Compose exposes from the container. These ports must be
accessible to linked services and should not be published to the host machine. Only the internal container
ports can be specified.

Syntax is `<portnum>/[<proto>]` or `<startport-endport>/[<proto>]` for a port range.
When not explicitly set, `tcp` protocol is used.

```yml
expose:
  - "3000"
  - "8000"
  - "8080-8085/tcp"
```

> [!NOTE]
>
> If the Dockerfile for the image already exposes ports, it is visible to other containers on the network even if `expose` is not set in your Compose file.

### `extends`

`extends` lets you share common configurations among different files, or even different projects entirely. With `extends` you can define a common set of service options in one place and refer to it from anywhere. You can refer to another Compose file and select a service you want to also use in your own application, with the ability to override some attributes for your own needs.

You can use `extends` on any service together with other configuration keys. The `extends` value must be a mapping
defined with a required `service` and an optional `file` key.

```yaml
extends:
  file: common.yml
  service: webapp
```

- `service`: Defines the name of the service being referenced as a base, for example `web` or `database`.
- `file`: The location of a Compose configuration file defining that service.

`extends` is not supported when deploying with `docker stack deploy`.

#### Restrictions

When a service is referenced using `extends`, it can declare dependencies on other resources. These dependencies may be explicitly defined through attributes like `volumes`, `networks`, `configs`, `secrets`, `links`, `volumes_from`, or `depends_on`. Alternatively, dependencies can reference another service using the `service:{name}` syntax in namespace declarations such as `ipc`, `pid`, or `network_mode`.

Compose does not automatically import these referenced resources into the extended model. It is your responsibility to ensure all required resources are explicitly declared in the model that relies on extends.

Circular references with `extends` are not supported, Compose returns an error when one is detected.

#### Finding referenced service

`file` value can be:

- Not present.
  This indicates that another service within the same Compose file is being referenced.
- File path, which can be either:
  - Relative path. This path is considered as relative to the location of the main Compose
    file.
  - Absolute path.

A service denoted by `service` must be present in the identified referenced Compose file.
Compose returns an error if:

- The service denoted by `service` is not found.
- The Compose file denoted by `file` is not found.

#### Merging service definitions

Two service definitions, the main one in the current Compose file and the referenced one
specified by `extends`, are merged in the following way:

- Mappings: Keys in mappings of the main service definition override keys in mappings
  of the referenced service definition. Keys that aren't overridden are included as is.
- Sequences: Items are combined together into a new sequence. The order of elements is
  preserved with the referenced items coming first and main items after.
- Scalars: Keys in the main service definition take precedence over keys in the
  referenced one.

##### Mappings

The following keys should be treated as mappings: `annotations`, `build.args`, `build.labels`,
`build.extra_hosts`, `deploy.labels`, `deploy.update_config`, `deploy.rollback_config`,
`deploy.restart_policy`, `deploy.resources.limits`, `environment`, `healthcheck`,
`labels`, `logging.options`, `sysctls`, `storage_opt`, `extra_hosts`, `ulimits`.

One exception that applies to `healthcheck` is that the main mapping cannot specify
`disable: true` unless the referenced mapping also specifies `disable: true`. Compose returns an error in this case.
For example, the following input:

```yaml
services:
  common:
    image: busybox
    environment:
      TZ: utc
      PORT: 80
  cli:
    extends:
      service: common
    environment:
      PORT: 8080
```

Produces the following configuration for the `cli` service. The same output is
produced if array syntax is used.

```yaml
environment:
  PORT: 8080
  TZ: utc
image: busybox
```

Items under `blkio_config.device_read_bps`, `blkio_config.device_read_iops`,
`blkio_config.device_write_bps`, `blkio_config.device_write_iops`, `devices` and
`volumes` are also treated as mappings where key is the target path inside the
container.

For example, the following input:

```yaml
services:
  common:
    image: busybox
    volumes:
      - common-volume:/var/lib/backup/data:rw
  cli:
    extends:
      service: common
    volumes:
      - cli-volume:/var/lib/backup/data:ro
```

Produces the following configuration for the `cli` service. Note that the mounted path
now points to the new volume name and `ro` flag was applied.

```yaml
image: busybox
volumes:
- cli-volume:/var/lib/backup/data:ro
```

If the referenced service definition contains `extends` mapping, the items under it
are simply copied into the new merged definition. The merging process is then kicked
off again until no `extends` keys are remaining.

For example, the following input:

```yaml
services:
  base:
    image: busybox
    user: root
  common:
    image: busybox
    extends:
      service: base
  cli:
    extends:
      service: common
```

Produces the following configuration for the `cli` service. Here, `cli` services
gets `user` key from `common` service, which in turn gets this key from `base`
service.

```yaml
image: busybox
user: root
```

##### Sequences

The following keys should be treated as sequences: `cap_add`, `cap_drop`, `configs`,
`deploy.placement.constraints`, `deploy.placement.preferences`,
`deploy.reservations.generic_resources`, `device_cgroup_rules`, `expose`,
`external_links`, `ports`, `secrets`, `security_opt`.
Any duplicates resulting from the merge are removed so that the sequence only
contains unique elements.

For example, the following input:

```yaml
services:
  common:
    image: busybox
    security_opt:
      - label=role:ROLE
  cli:
    extends:
      service: common
    security_opt:
      - label=user:USER
```

Produces the following configuration for the `cli` service.

```yaml
image: busybox
security_opt:
- label=role:ROLE
- label=user:USER
```

In case list syntax is used, the following keys should also be treated as sequences:
`dns`, `dns_search`, `env_file`, `tmpfs`. Unlike sequence fields mentioned previously,
duplicates resulting from the merge are not removed.

##### Scalars

Any other allowed keys in the service definition should be treated as scalars.

### `external_links`

`external_links` link service containers to services managed outside of your Compose application.
`external_links` define the name of an existing service to retrieve using the platform lookup mechanism.
An alias of the form `SERVICE:ALIAS` can be specified.

```yml
external_links:
  - redis
  - database:mysql
  - database:postgresql
```

### `extra_hosts`

`extra_hosts` adds hostname mappings to the container network interface configuration (`/etc/hosts` for Linux).

#### Short syntax

Short syntax uses plain strings in a list. Values must set hostname and IP address for additional hosts in the form of `HOSTNAME=IP`.

```yml
extra_hosts:
  - "somehost=162.242.195.82"
  - "otherhost=50.31.209.229"
  - "myhostv6=::1"
```

IPv6 addresses can be enclosed in square brackets, for example:

```yml
extra_hosts:
  - "myhostv6=[::1]"
```

The separator `=` is preferred, but `:` can also be used. Introduced in Docker Compose version [2.24.1](https://github.com/docker/compose/releases/tag/v2.24.1). For example:

```yml
extra_hosts:
  - "somehost:162.242.195.82"
  - "myhostv6:::1"
```

#### Long syntax

Alternatively, `extra_hosts` can be set as a mapping between hostname(s) and IP(s)

```yml
extra_hosts:
  somehost: "162.242.195.82"
  otherhost: "50.31.209.229"
  myhostv6: "::1"
```

Compose creates a matching entry with the IP address and hostname in the container's network
configuration, which means for Linux `/etc/hosts` get extra lines:

```console
162.242.195.82  somehost
50.31.209.229   otherhost
::1             myhostv6
```

### `gpus`

{{< summary-bar feature_name="Compose gpus" >}}

`gpus` specifies GPU devices to be allocated for container usage. This is equivalent to a [device request](deploy.md#devices) with
an implicit `gpu` capability.

```yaml
services:
  model:
    gpus:
      - driver: 3dfx
        count: 2
```

`gpus` also can be set as string `all` to allocate all available GPU devices to the container.

```yaml
services:
  model:
    gpus: all
```

### `group_add`

`group_add` specifies additional groups, by name or number, which the user inside the container must be a member of.

An example of where this is useful is when multiple containers (running as different users) need to all read or write
the same file on a shared volume. That file can be owned by a group shared by all the containers, and specified in
`group_add`.

```yml
services:
  myservice:
    image: alpine
    group_add:
      - mail
```

Running `id` inside the created container must show that the user belongs to the `mail` group, which would not have
been the case if `group_add` were not declared.

### `healthcheck`

{{% include "compose/services-healthcheck.md" %}}

For more information on `HEALTHCHECK`, see the [Dockerfile reference](/reference/dockerfile.md#healthcheck).

```yml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
  start_interval: 5s
```

`interval`, `timeout`, `start_period`, and `start_interval` are [specified as durations](extension.md#specifying-durations). Introduced in Docker Compose version [2.20.2](https://github.com/docker/compose/releases/tag/v2.20.2)

`test` defines the command Compose runs to check container health. It can be
either a string or a list. If it's a list, the first item must be either `NONE`, `CMD` or `CMD-SHELL`.
If it's a string, it's equivalent to specifying `CMD-SHELL` followed by that string.

```yml
# Hit the local web app
test: ["CMD", "curl", "-f", "http://localhost"]
```

Using `CMD-SHELL` runs the command configured as a string using the container's default shell
(`/bin/sh` for Linux). Both of the following forms are equivalent:

```yml
test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
```

```yml
test: curl -f https://localhost || exit 1
```

`NONE` disables the healthcheck, and is mostly useful to disable the Healthcheck Dockerfile instruction set by the service's Docker image. Alternatively,
the healthcheck set by the image can be disabled by setting `disable: true`:

```yml
healthcheck:
  disable: true
```

### `hostname`

`hostname` declares a custom host name to use for the service container. It must be a valid RFC 1123 hostname.

### `image`

`image` specifies the image to start the container from. `image` must follow the Open Container Specification
[addressable image format](https://github.com/opencontainers/org/blob/master/docs/docs/introduction/digests.md),
as `[<registry>/][<project>/]<image>[:<tag>|@<digest>]`.

```yml
    image: redis
    image: redis:5
    image: redis@sha256:0ed5d5928d4737458944eb604cc8509e245c3e19d02ad83935398bc4b991aac7
    image: library/redis
    image: docker.io/library/redis
    image: my_private.registry:5000/redis
```

If the image does not exist on the platform, Compose attempts to pull it based on the `pull_policy`.
If you are also using the [Compose Build Specification](build.md), there are alternative options for controlling the precedence of
pull over building the image from source, however pulling the image is the default behavior.

`image` may be omitted from a Compose file as long as a `build` section is declared. If you are not using the Compose Build Specification, Compose won't work if `image` is missing from the Compose file.

### `init`

`init` runs an init process (PID 1) inside the container that forwards signals and reaps processes.
Set this option to `true` to enable this feature for the service.

```yml
services:
  web:
    image: alpine:latest
    init: true
```

The init binary that is used is platform specific.

### `ipc`

`ipc` configures the IPC isolation mode set by the service container.

- `shareable`: Gives the container its own private IPC namespace, with a
  possibility to share it with other containers.
- `service:{name}`: Makes the container join another container's
  (`shareable`) IPC namespace.

```yml
    ipc: "shareable"
    ipc: "service:[service name]"
```

### `isolation`

`isolation` specifies a container’s isolation technology. Supported values are platform specific.

### `labels`

`labels` add metadata to containers. You can use either an array or a map.

It's recommended that you use reverse-DNS notation to prevent your labels from conflicting with
those used by other software.

```yml
labels:
  com.example.description: "Accounting webapp"
  com.example.department: "Finance"
  com.example.label-with-empty-value: ""
```

```yml
labels:
  - "com.example.description=Accounting webapp"
  - "com.example.department=Finance"
  - "com.example.label-with-empty-value"
```

Compose creates containers with canonical labels:

- `com.docker.compose.project` set on all resources created by Compose to the user project name
- `com.docker.compose.service` set on service containers with service name as defined in the Compose file

The `com.docker.compose` label prefix is reserved. Specifying labels with this prefix in the Compose file
results in a runtime error.

### `label_file`

{{< summary-bar feature_name="Compose label file" >}}

The `label_file` attribute lets you load labels for a service from an external file or a list of files. This provides a convenient way to manage multiple labels without cluttering the Compose file.

The file uses a key-value format, similar to `env_file`. You can specify multiple files as a list. When using multiple files, they are processed in the order they appear in the list. If the same label is defined in multiple files, the value from the last file in the list overrides earlier ones.

```yaml
services:
  one:
    label_file: ./app.labels

  two:
    label_file:
      - ./app.labels
      - ./additional.labels
```

If a label is defined in both the `label_file` and the `labels` attribute, the value in [labels](#labels) takes precedence.

### `links`

`links` defines a network link to containers in another service. Either specify both the service name and
a link alias (`SERVICE:ALIAS`), or just the service name.

```yml
web:
  links:
    - db
    - db:database
    - redis
```

Containers for the linked service are reachable at a hostname identical to the alias, or the service name
if no alias is specified.

Links are not required to enable services to communicate. When no specific network configuration is set,
any service is able to reach any other service at that service’s name on the `default` network.
If services specify the networks they are attached to, `links` does not override the network configuration. Services that are not connected to a shared network are not be able to communicate with each other. Compose doesn't warn you about a configuration mismatch.

Links also express implicit dependency between services in the same way as
[`depends_on`](#depends_on), so they determine the order of service startup.

### `logging`

`logging` defines the logging configuration for the service.

```yml
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
```

The `driver` name specifies a logging driver for the service's containers. The default and available values
are platform specific. Driver specific options can be set with `options` as key-value pairs.

### `mac_address`

> Available with Docker Compose version 2.24.0 and later.

`mac_address` sets a Mac address for the service container.

> [!NOTE]
> Container runtimes might reject this value, for example Docker Engine >= v25.0. In that case, you should use [networks.mac_address](#mac_address) instead.

### `mem_limit`

`mem_limit` configures a limit on the amount of memory a container can allocate, set as a string expressing a [byte value](extension.md#specifying-byte-values).

When set, `mem_limit` must be consistent with the `limits.memory` attribute in the [Deploy Specification](deploy.md#memory).

### `mem_reservation`

`mem_reservation` configures a reservation on the amount of memory a container can allocate, set as a string expressing a [byte value](extension.md#specifying-byte-values).

When set, `mem_reservation` must be consistent with the `reservations.memory` attribute in the [Deploy Specification](deploy.md#memory).

### `mem_swappiness`

`mem_swappiness` defines as a percentage, a value between 0 and 100, for the host kernel to swap out
anonymous memory pages used by a container.

- `0`: Turns off anonymous page swapping.
- `100`: Sets all anonymous pages as swappable.

The default value is platform specific.

### `memswap_limit`

`memswap_limit` defines the amount of memory the container is allowed to swap to disk. This is a modifier
attribute that only has meaning if [`memory`](deploy.md#memory) is also set. Using swap lets the container write excess
memory requirements to disk when the container has exhausted all the memory that is available to it.
There is a performance penalty for applications that swap memory to disk often.

- If `memswap_limit` is set to a positive integer, then both `memory` and `memswap_limit` must be set. `memswap_limit` represents the total amount of memory and swap that can be used, and `memory` controls the amount used by non-swap memory. So if `memory`="300m" and `memswap_limit`="1g", the container can use 300m of memory and 700m (1g - 300m) swap.
- If `memswap_limit` is set to 0, the setting is ignored, and the value is treated as unset.
- If `memswap_limit` is set to the same value as `memory`, and `memory` is set to a positive integer, the container does not have access to swap.
- If `memswap_limit` is unset, and `memory` is set, the container can use as much swap as the `memory` setting, if the host container has swap memory configured. For instance, if `memory`="300m" and `memswap_limit` is not set, the container can use 600m in total of memory and swap.
- If `memswap_limit` is explicitly set to -1, the container is allowed to use unlimited swap, up to the amount available on the host system.

### `models`

{{< summary-bar feature_name="Compose models" >}}

`models` defines which AI models the service should use at runtime. Each referenced model must be defined under the [`models` top-level element](models.md).

```yaml
services:
  short_syntax:
    image: app
    models:
      - my_model
  long_syntax:
    image: app
    models:
      my_model:
        endpoint_var: MODEL_URL
        model_var: MODEL
```

When a service is linked to a model, Docker Compose injects environment variables to pass connection details and model identifiers to the container. This allows the application to locate and communicate with the model dynamically at runtime, without hard-coding values.

#### Long syntax

The long syntax gives you more control over the environment variable names.

- `endpoint_var` sets the name of the environment variable that holds the model runner’s URL.
- `model_var` sets the name of the environment variable that holds the model identifier.

If either is omitted, Compose automatically generates the environment variable names based on the model key using the following rules:

 - Convert the model key to uppercase
 - Replace any '-' characters with '_'
 - Append `_URL` for the endpoint variable

### `network_mode`

`network_mode` sets a service container's network mode.

- `bridge`: Connects the container to Docker's default bridge network instead of
  a project-specific network. Containers on the default bridge network cannot
  resolve each other by service name . Instead, use a user-defined network for DNS resolution.
- `none`: Turns off all container networking.
- `host`: Gives the container raw access to the host's network interface.
- `service:{name}`: Gives the container access to the specified container by referring to its service name.
- `container:{name}`: Gives the container access to the specified container by referring to its container ID.

For more information container networks, see the [Docker Engine documentation](/manuals/engine/network/_index.md#container-networks).

```yml
    network_mode: "bridge"
    network_mode: "host"
    network_mode: "none"
    network_mode: "service:[service name]"
```

When set, the [`networks`](#networks) attribute is not allowed and Compose rejects any
Compose file containing both attributes.

### `networks`

{{% include "compose/services-networks.md" %}}

```yml
services:
  some-service:
    networks:
      - some-network
      - other-network
```
For more information about the `networks` top-level element, see [Networks](networks.md).

#### Implicit default network

If `networks` is empty or absent from the Compose file, Compose considers an implicit definition for the service to be
connected to the `default` network:

```yml
services:
  some-service:
    image: foo
```
This example is actually equivalent to:

```yml
services:
  some-service:
    image: foo
    networks:
      default: {}
```

If you want the service to not be connected a network, you must set [`network_mode: none`](#network_mode).

#### `aliases`

`aliases` declares alternative hostnames for the service on the network. Other containers on the same
network can use either the service name or an alias to connect to one of the service's containers.

Since `aliases` are network-scoped, the same service can have different aliases on different networks.

> [!NOTE]
> A network-wide alias can be shared by multiple containers, and even by multiple services.
> If it is, then exactly which container the name resolves to is not guaranteed.

```yml
services:
  some-service:
    networks:
      some-network:
        aliases:
          - alias1
          - alias3
      other-network:
        aliases:
          - alias2
```

In the following example, service `frontend` is able to reach the `backend` service at
the hostname `backend` or `database` on the `back-tier` network. The service `monitoring`
is able to reach same `backend` service at `backend` or `mysql` on the `admin` network.

```yml
services:
  frontend:
    image: example/webapp
    networks:
      - front-tier
      - back-tier

  monitoring:
    image: example/monitoring
    networks:
      - admin

  backend:
    image: example/backend
    networks:
      back-tier:
        aliases:
          - database
      admin:
        aliases:
          - mysql

networks:
  front-tier: {}
  back-tier: {}
  admin: {}
```

#### `interface_name`

{{< summary-bar feature_name="Compose interface-name" >}}

`interface_name` lets you specify the name of the network interface used to connect a service to a given network. This ensures consistent and predictable interface naming across services and networks.

```yaml
services:
  backend:
    image: alpine
    command: ip link show
    networks:
      back-tier:
        interface_name: eth0
```

Running the example Compose application shows:

```console
backend-1  | 11: eth0@if64: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
```

#### `ipv4_address`, `ipv6_address`

Specify a static IP address for a service container when joining the network.

The corresponding network configuration in the [top-level networks section](networks.md) must have an
`ipam` attribute with subnet configurations covering each static address.

```yml
services:
  frontend:
    image: example/webapp
    networks:
      front-tier:
        ipv4_address: 172.16.238.10
        ipv6_address: 2001:3984:3989::10

networks:
  front-tier:
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"
        - subnet: "2001:3984:3989::/64"
```

#### `link_local_ips`

`link_local_ips` specifies a list of link-local IPs. Link-local IPs are special IPs which belong to a well
known subnet and are purely managed by the operator, usually dependent on the architecture where they are
deployed.

Example:

```yaml
services:
  app:
    image: busybox
    command: top
    networks:
      app_net:
        link_local_ips:
          - 57.123.22.11
          - 57.123.22.13
networks:
  app_net:
    driver: bridge
```

#### `mac_address`

{{< summary-bar feature_name="Compose mac address" >}}

`mac_address` sets the Mac address used by the service container when connecting to this particular network.

#### `driver_opts`

`driver_opts` specifies a list of options as key-value pairs to pass to the driver. These options are
driver-dependent. Consult the driver's documentation for more information.

```yml
services:
  app:
    networks:
      app_net:
        driver_opts:
          foo: "bar"
          baz: 1
```

#### `gw_priority`

{{< summary-bar feature_name="Compose gw priority" >}}

The network with the highest `gw_priority` is selected as the default gateway for the service container.
If unspecified, the default value is 0.

In the following example, `app_net_2` will be selected as the default gateway.

```yaml
services:
  app:
    image: busybox
    command: top
    networks:
      app_net_1:
      app_net_2:
        gw_priority: 1
      app_net_3:
networks:
  app_net_1:
  app_net_2:
  app_net_3:
```

#### `priority`

`priority` indicates in which order Compose connects the service’s containers to its
networks. If unspecified, the default value is 0.

If the container runtime accepts a `mac_address` attribute at service level, it is
applied to the network with the highest `priority`. In other cases, use attribute
`networks.mac_address`.

`priority` does not affect which network is selected as the default gateway. Use the
[`gw_priority`](#gw_priority) attribute instead.

`priority` does not control the order in which networks connections are added to
the container, it cannot be used to determine the device name (`eth0` etc.) in the
container.

```yaml
services:
  app:
    image: busybox
    command: top
    networks:
      app_net_1:
        priority: 1000
      app_net_2:

      app_net_3:
        priority: 100
networks:
  app_net_1:
  app_net_2:
  app_net_3:
```

### `oom_kill_disable`

If `oom_kill_disable` is set, Compose configures the platform so it won't kill the container in case
of memory starvation.

### `oom_score_adj`

`oom_score_adj` tunes the preference for containers to be killed by platform in case of memory starvation. Value must
be within -1000,1000 range.

### `pid`

`pid` sets the PID mode for container created by Compose.
Supported values are platform specific.

### `pids_limit`

`pids_limit` tunes a container’s PIDs limit. Set to -1 for unlimited PIDs.

```yml
pids_limit: 10
```

When set, `pids_limit` must be consistent with the `pids` attribute in the [Deploy Specification](deploy.md#pids).

### `platform`

`platform` defines the target platform the containers for the service run on. It uses the `os[/arch[/variant]]` syntax.

The values of `os`, `arch`, and `variant` must conform to the convention used by the [OCI Image Spec](https://github.com/opencontainers/image-spec/blob/v1.0.2/image-index.md).

Compose uses this attribute to determine which version of the image is pulled
and/or on which platform the service’s build is performed.

```yml
platform: darwin
platform: windows/amd64
platform: linux/arm64/v8
```

### `ports`

{{% include "compose/services-ports.md" %}}

> [!NOTE]
>
> Port mapping must not be used with `network_mode: host`. Doing so causes a runtime error because `network_mode: host` already exposes container ports directly to the host network, so port mapping isn’t needed.

#### Short syntax

The short syntax is a colon-separated string to set the host IP, host port, and container port
in the form:

`[HOST:]CONTAINER[/PROTOCOL]` where:

- `HOST` is `[IP:](port | range)` (optional). If it is not set, it binds to all network interfaces (`0.0.0.0`).
- `CONTAINER` is `port | range`.
- `PROTOCOL` restricts ports to a specified protocol either `tcp` or `udp`(optional). Default is `tcp`.

> [!WARNING]
>
> If you do not specify a host IP (such as `127.0.0.1`), Docker binds to all interfaces (`0.0.0.0`), bypassing host firewall rules. This can expose the container directly to the internet if the host has a public IP address. For more information, see [Port publishing and mapping](/manuals/engine/network/port-publishing.md).

Ports can be either a single value or a range. `HOST` and `CONTAINER` must use equivalent ranges.

You can either specify both ports (`HOST:CONTAINER`), or just the container port. In the latter case,
the container runtime automatically allocates any unassigned port of the host.

`HOST:CONTAINER` should always be specified as a (quoted) string, to avoid conflicts
with [YAML base-60 float](https://yaml.org/type/float.html).



IPv6 addresses can be enclosed in square brackets.

Examples:

```yml
ports:
  - "3000"
  - "3000-3005"
  - "8000:8000"
  - "9090-9091:8080-8081"
  - "49100:22"
  - "8000-9000:80"
  - "127.0.0.1:8001:8001"
  - "127.0.0.1:5000-5010:5000-5010"
  - "::1:6000:6000"
  - "[::1]:6001:6001"
  - "6060:6060/udp"
```

> [!NOTE]
>
> If host IP mapping is not supported by a container engine, Compose rejects
> the Compose file and ignores the specified host IP.

#### Long syntax

The long form syntax lets you configure additional fields that can't be
expressed in the short form.

- `target`: The container port.
- `published`: The publicly exposed port. It is defined as a string and can be set as a range using syntax `start-end`. It means the actual port is assigned a remaining available port, within the set range.
- `host_ip`: The host IP mapping. If it is not set, it binds to all network interfaces (`0.0.0.0`).
- `protocol`: The port protocol (`tcp` or `udp`). Defaults to `tcp`.
- `app_protocol`: The application protocol (TCP/IP level 4 / OSI level 7) this port is used for. This is optional and can be used as a hint for Compose to offer richer behavior for protocols that it understands. Introduced in Docker Compose version [2.26.0](https://github.com/docker/compose/releases/tag/v2.26.0).
- `mode`: Specifies how the port is published in a Swarm setup. If set to `host`, it publishes the port on every node in Swarm. If set to `ingress`, it allows load balancing across the nodes in Swarm. Defaults to `ingress`.
- `name`: A human-readable name for the port, used to document its usage within the service.

```yml
ports:
  - name: web
    target: 80
    host_ip: 127.0.0.1
    published: "8080"
    protocol: tcp
    app_protocol: http
    mode: host

  - name: web-secured
    target: 443
    host_ip: 127.0.0.1
    published: "8083-9000"
    protocol: tcp
    app_protocol: https
    mode: host
```

### `post_start`

{{< summary-bar feature_name="Compose post start" >}}

`post_start` defines a sequence of lifecycle hooks to run after a container has started. The exact timing of when the command is run is not guaranteed.

- `command`: Specifies the command to run once the container starts. This attribute is required, and you can choose to use either the shell form or the exec form.
- `user`: The user to run the command. If not set, the command is run with the same user as the main service command.
- `privileged`: Lets the `post_start` command run with privileged access.
- `working_dir`: The working directory in which to run the command. If not set, it is run in the same working directory as the main service command.
- `environment`: Sets environment variables specifically for the `post_start` command. While the command inherits the environment variables defined for the service’s main command, this section lets you add new variables or override existing ones.

```yaml
services:
  test:
    post_start:
      - command: ./do_something_on_startup.sh
        user: root
        privileged: true
        environment:
          - FOO=BAR
```

For more information, see [Use lifecycle hooks](/manuals/compose/how-tos/lifecycle.md).

### `pre_stop`

{{< summary-bar feature_name="Compose pre stop" >}}

`pre_stop` defines a sequence of lifecycle hooks to run before the container is stopped. These hooks won't run if the container stops by itself or is terminated suddenly.

Configuration is equivalent to [post_start](#post_start).

### `privileged`

`privileged` configures the service container to run with elevated privileges. Support and actual impacts are platform specific.

### `profiles`

`profiles` defines a list of named profiles for the service to be enabled under. If unassigned, the service is always started but if assigned, it is only started if the profile is activated.

If present, `profiles` follow the regex format of `[a-zA-Z0-9][a-zA-Z0-9_.-]+`.

```yaml
services:
  frontend:
    image: frontend
    profiles: ["frontend"]

  phpmyadmin:
    image: phpmyadmin
    depends_on:
      - db
    profiles:
      - debug
```

### `provider`

{{< summary-bar feature_name="Compose provider services" >}}

`provider` can be used to define a service that Compose won't manage directly. Compose delegated the service lifecycle to a dedicated or third-party component.

```yaml
  database:
    provider:
      type: awesomecloud
      options:
        type: mysql
        foo: bar
  app:
    image: myapp
    depends_on:
       - database
```

As Compose runs the application, the `awesomecloud` binary is used to manage the `database` service setup.
Dependent service `app` receives additional environment variables prefixed by the service name so it can access the resource.

For illustration, assuming `awesomecloud` execution produced variables `URL` and `API_KEY`, the `app` service
runs with environment variables `DATABASE_URL` and `DATABASE_API_KEY`.

As Compose stops the application, the `awesomecloud` binary is used to manage the `database` service tear down.

The mechanism used by Compose to delegate the service lifecycle to an external binary is described in the [Compose extensibility documentation](https://github.com/docker/compose/tree/main/docs/extension.md).

For more information on using the `provider` attribute, see [Use provider services](/manuals/compose/how-tos/provider-services.md).

#### `type`

`type` attribute is required. It defines the external component used by Compose to manage setup and tear down lifecycle
events.

#### `options`

`options` are specific to the selected provider and not validated by the compose specification

### `pull_policy`

`pull_policy` defines the decisions Compose makes when it starts to pull images. Possible values are:

- `always`: Compose always pulls the image from the registry.
- `never`: Compose doesn't pull the image from a registry and relies on the platform cached image.
   If there is no cached image, a failure is reported.
- `missing`: Compose pulls the image only if it's not available in the platform cache.
   This is the default option if you are not also using the [Compose Build Specification](build.md).
  `if_not_present` is considered an alias for this value for backward compatibility. The `latest` tag is always pulled even when the `missing` pull policy is used.
- `build`: Compose builds the image. Compose rebuilds the image if it's already present.
- `daily`: Compose checks the registry for image updates if the last pull took place more than 24 hours ago.
- `weekly`: Compose checks the registry for image updates if the last pull took place more than 7 days ago.
- `every_<duration>`: Compose checks the registry for image updates if the last pull took place before `<duration>`. Duration can be expressed in weeks (`w`), days (`d`), hours (`h`), minutes (`m`), seconds (`s`) or a combination of these.

```yaml
services:
  test:
    image: nginx
    pull_policy: every_12h
```

### `read_only`

`read_only` configures the service container to be created with a read-only filesystem.

### `restart`

`restart` defines the policy that the platform applies on container termination.

- `no`: The default restart policy. It does not restart the container under any circumstances.
- `always`: The policy always restarts the container until its removal.
- `on-failure[:max-retries]`: The policy restarts the container if the exit code indicates an error.
Optionally, limit the number of restart retries the Docker daemon attempts.
- `unless-stopped`: The policy restarts the container irrespective of the exit code but stops
  restarting when the service is stopped or removed.

```yml
    restart: "no"
    restart: always
    restart: on-failure
    restart: on-failure:3
    restart: unless-stopped
```

You can find more detailed information on restart policies in the
[Restart Policies (--restart)](/reference/cli/docker/container/run/#restart)
section of the Docker run reference page.

### `runtime`

`runtime` specifies which runtime to use for the service’s containers.

For example, `runtime` can be the name of [an implementation of OCI Runtime Spec](https://github.com/opencontainers/runtime-spec/blob/master/implementations.md), such as "runc".

```yml
web:
  image: busybox:latest
  command: true
  runtime: runc
```

The default is `runc`. To use a different runtime, see [Alternative runtimes](/manuals/engine/daemon/alternative-runtimes.md).

### `scale`

`scale` specifies the default number of containers to deploy for this service.
When both are set, `scale` must be consistent with the `replicas` attribute in the [Deploy Specification](deploy.md#replicas).

### `secrets`

{{% include "compose/services-secrets.md" %}}

Two different syntax variants are supported; the short syntax and the long syntax. Long and short syntax for secrets may be used in the same Compose file.

Compose reports an error if the secret doesn't exist on the platform or isn't defined in the
[`secrets` top-level section](secrets.md) of the Compose file.

Defining a secret in the top-level `secrets` must not imply granting any service access to it.
Such grant must be explicit within service specification as [secrets](secrets.md) service element.

#### Short syntax

The short syntax variant only specifies the secret name. This grants the
container access to the secret and mounts it as read-only to `/run/secrets/<secret_name>`
within the container. The source name and destination mountpoint are both set
to the secret name.

The following example uses the short syntax to grant the `frontend` service
access to the `server-certificate` secret. The value of `server-certificate` is set
to the contents of the file `./server.cert`.

```yml
services:
  frontend:
    image: example/webapp
    secrets:
      - server-certificate
secrets:
  server-certificate:
    file: ./server.cert
```

#### Long syntax

The long syntax provides more granularity in how the secret is created within
the service's containers.

- `source`: The name of the secret as it exists on the platform.
- `target`: The name of the file to be mounted in `/run/secrets/` in the
  service's task container, or absolute path of the file if an alternate location is required. Defaults to `source` if not specified.
- `uid` and `gid`: The numeric uid or gid that owns the file within
  `/run/secrets/` in the service's task containers.
- `mode`: The [permissions](https://wintelguy.com/permissions-calc.pl) for the file to be mounted in `/run/secrets/`
  in the service's task containers, in octal notation.
  The default value is world-readable permissions (mode `0444`).
  The writable bit must be ignored if set. The executable bit may be set.

Note that support for `uid`, `gid`, and `mode` attributes are only implemented in Docker Compose when the source of the secret is [`environment`](secrets.md). When the source is a [`file`](secrets.md), Compose uses a bind-mount under the hood which doesn't allow `uid` remapping, and these attributes are silently ignored.

The following example sets the name of the `my-token` secret file within the container,
sets the mode to `0440` (group-readable), and sets the user and group to `103`.
The value of `my-token` is read from the `MY_TOKEN` environment variable.

```yml
services:
  frontend:
    image: example/webapp
    secrets:
      - source: my-token
        uid: "103"
        gid: "103"
        mode: 0o440
secrets:
  my-token:
    environment: "MY_TOKEN"
```

### `security_opt`

`security_opt` overrides the default labeling scheme for each container.

Options accept either `option=value` or `option:value` syntax. For boolean options
such as `no-new-privileges`, the value may be omitted entirely, in which case the
option is treated as enabled. The following syntaxes are all equivalent:

```yml
security_opt:
  - no-new-privileges
  - no-new-privileges=true
  - no-new-privileges:true
```

```yml
security_opt:
  - label=user:USER
  - label=role:ROLE
```

For further default labeling schemes you can override, see [Security configuration](/reference/cli/docker/container/run/#security-opt).

### `shm_size`

`shm_size` configures the size of the shared memory (`/dev/shm` partition on Linux) allowed by the service container.
It's specified as a [byte value](extension.md#specifying-byte-values).

### `stdin_open`

`stdin_open` configures a service's container to run with an allocated stdin. This is the same as running a container with the
`-i` flag. For more information, see [Keep stdin open](/reference/cli/docker/container/run/#interactive).

Supported values are `true` or `false`.

### `stop_grace_period`

`stop_grace_period` specifies how long Compose must wait when attempting to stop a container if it doesn't
handle SIGTERM (or whichever stop signal has been specified with
[`stop_signal`](#stop_signal)), before sending SIGKILL. It's specified
as a [duration](extension.md#specifying-durations).

```yml
    stop_grace_period: 1s
    stop_grace_period: 1m30s
```

Default value is 10 seconds for the container to exit before sending SIGKILL.

### `stop_signal`

`stop_signal` defines the signal that Compose uses to stop the service containers.
If unset containers are stopped by Compose by sending `SIGTERM`.

```yml
stop_signal: SIGUSR1
```

### `storage_opt`

`storage_opt` defines storage driver options for a service.

```yml
storage_opt:
  size: '1G'
```

### `sysctls`

`sysctls` defines kernel parameters to set in the container. `sysctls` can use either an array or a map.

```yml
sysctls:
  net.core.somaxconn: 1024
  net.ipv4.tcp_syncookies: 0
```

```yml
sysctls:
  - net.core.somaxconn=1024
  - net.ipv4.tcp_syncookies=0
```

You can only use sysctls that are namespaced in the kernel. Docker does not
support changing sysctls inside a container that also modify the host system.
For an overview of supported sysctls, refer to [configure namespaced kernel
parameters (sysctls) at runtime](/reference/cli/docker/container/run/#sysctl).

### `tmpfs`

`tmpfs` mounts a temporary file system inside the container. It can be a single value or a list.

```yml
tmpfs:
 - <path>
 - <path>:<options>
```

- `path`: The path inside the container where the tmpfs will be mounted.
- `options`: Comma-separated list of options for the tmpfs mount.

Available options:

- `mode`: Sets the file system permissions.
- `uid`: Sets the user ID that owns the mounted tmpfs.
- `gid`: Sets the group ID that owns the mounted tmpfs.

```yml
services:
  app:
    tmpfs:
      - /data:mode=755,uid=1009,gid=1009
      - /run
```

### `tty`

`tty` configures a service's container to run with a TTY. This is the same as running a container with the
`-t` or `--tty` flag. For more information, see [Allocate a pseudo-TTY](/reference/cli/docker/container/run/#tty).

Supported values are `true` or `false`.

### `ulimits`

`ulimits` overrides the default `ulimits` for a container. It's specified either as an integer for a single limit
or as mapping for soft/hard limits.

```yml
ulimits:
  nproc: 65535
  nofile:
    soft: 20000
    hard: 40000
```

### `use_api_socket`

When `use_api_socket` is set, the container is able to interact with the underlying container engine through the API socket.
Your credentials are mounted inside the container so the container acts as a pure delegate for your commands relating to the container engine.
Typically, commands ran by container can `pull` and `push` to your registry.

### `user`

`user` overrides the user used to run the container process. The default is set by the image, for example Dockerfile `USER`. If it's not set, then `root`.

### `userns_mode`

`userns_mode` sets the user namespace for the service. Supported values are platform specific and may depend
on platform configuration.

```yml
userns_mode: "host"
```

### `uts`

{{< summary-bar feature_name="Compose uts" >}}

`uts` configures the UTS namespace mode set for the service container. When unspecified
it is the runtime's decision to assign a UTS namespace, if supported. Available values are:

- `'host'`: Results in the container using the same UTS namespace as the host.

```yml
    uts: "host"
```

### `volumes`

{{% include "compose/services-volumes.md" %}}

The following example shows a named volume (`db-data`) being used by the `backend` service,
and a bind mount defined for a single service.

```yml
services:
  backend:
    image: example/backend
    volumes:
      - type: volume
        source: db-data
        target: /data
        volume:
          nocopy: true
          subpath: sub
      - type: bind
        source: /var/run/postgres/postgres.sock
        target: /var/run/postgres/postgres.sock

volumes:
  db-data:
```

For more information about the `volumes` top-level element, see [Volumes](volumes.md).

#### Short syntax

The short syntax uses a single string with colon-separated values to specify a volume mount
(`VOLUME:CONTAINER_PATH`), or an access mode (`VOLUME:CONTAINER_PATH:ACCESS_MODE`).

- `VOLUME`: Can be either a host path on the platform hosting containers (bind mount) or a volume name.
- `CONTAINER_PATH`: The path in the container where the volume is mounted.
- `ACCESS_MODE`: A comma-separated `,` list of options:
  - `rw`: Read and write access. This is the default if none is specified.
  - `ro`: Read-only access.
  - `z`: SELinux option indicating that the bind mount host content is shared among multiple containers.
  - `Z`: SELinux option indicating that the bind mount host content is private and unshared for other containers.

> [!NOTE]
>
> The SELinux re-labeling bind mount option is ignored on platforms without SELinux.

> [!NOTE]
> Relative host paths are only supported by Compose that deploy to a
> local container runtime. This is because the relative path is resolved from the Compose file’s parent
> directory which is only applicable in the local case. When Compose deploys to a non-local
> platform it rejects Compose files which use relative host paths with an error. To avoid ambiguities
> with named volumes, relative paths should always begin with `.` or `..`.

> [!NOTE]
>
> For bind mounts, the short syntax creates a directory at the source path on the host if it doesn't exist. This is for backward compatibility with `docker-compose` legacy.
> It can be prevented by using long syntax and setting `create_host_path` to `false`.

#### Long syntax

The long form syntax lets you configure additional fields that can't be
expressed in the short form.

- `type`: The mount type. Either `volume`, `bind`, `tmpfs`, `image`, `npipe`, or `cluster`
- `source`: The source of the mount, a path on the host for a bind mount, a Docker image reference for an image mount, or the
  name of a volume defined in the
  [top-level `volumes` key](volumes.md). Not applicable for a tmpfs mount.
- `target`: The path in the container where the volume is mounted.
- `read_only`: Flag to set the volume as read-only.
- `bind`: Used to configure additional bind options:
  - `propagation`: The propagation mode used for the bind.
  - `create_host_path`: Creates a directory at the source path on host if there is nothing present. Defaults to `true`.
  - `selinux`: The SELinux re-labeling option `z` (shared) or `Z` (private)
- `volume`: Configures additional volume options:
  - `nocopy`: Flag to disable copying of data from a container when a volume is created.
  - `subpath`: Path inside a volume to mount instead of the volume root.
- `tmpfs`: Configures additional tmpfs options:
  - `size`: The size for the tmpfs mount in bytes (either numeric or as bytes unit).
  - `mode`: The file mode for the tmpfs mount as Unix permission bits as an octal number. Introduced in Docker Compose version [2.14.0](https://github.com/docker/compose/releases/tag/v2.14.0).
- `image`: Configures additional image options:
  - `subpath`: Path inside the source image to mount instead of the image root. Available in [Docker Compose version 2.35.0](https://github.com/docker/compose/releases/tag/v2.35.0)
- `consistency`: The consistency requirements of the mount. Available values are platform specific.

> [!TIP]
>
> Working with large repositories or monorepos, or with virtual file systems that are no longer scaling with your codebase?
> Compose now takes advantage of [Synchronized file shares](/manuals/desktop/features/synchronized-file-sharing.md) and automatically creates file shares for bind mounts.
> Ensure you're signed in to Docker with a paid subscription and have enabled both **Access experimental features** and **Manage Synchronized file shares with Compose** in Docker Desktop's settings.

### `volumes_from`

`volumes_from` mounts all of the volumes from another service or container. You can optionally specify
read-only access `ro` or read-write `rw`. If no access level is specified, then read-write access is used.

You can also mount volumes from a container that is not managed by Compose by using the `container:` prefix.

```yaml
volumes_from:
  - service_name
  - service_name:ro
  - container:container_name
  - container:container_name:rw
```

### `working_dir`

`working_dir` overrides the container's working directory which is specified by the image, for example Dockerfile's `WORKDIR`.
