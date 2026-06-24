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

- `path`: define o caminho simbólico para o dispositivo afetado.
- `rate`: pode ser um valor inteiro representando o número de bytes ou uma
  string expressando um valor em bytes.

#### `device_read_iops`, `device_write_iops`

Define um limite em operações por segundo para operações de leitura/escrita em
um dispositivo específico.
Cada item da lista deve conter duas chaves:

- `path`: define o caminho simbólico para o dispositivo afetado.
- `rate`: um valor inteiro representando o número permitido de operações por
  segundo.

#### `weight`

Modifica a proporção de largura de banda alocada a um serviço em relação a
outros serviços.
Assume um valor inteiro entre 10 e 1000, sendo 500 o valor padrão.

#### `weight_device`

Ajusta a alocação de largura de banda por dispositivo.
Cada item da lista deve ter duas chaves:

- `path`: define o caminho simbólico para o dispositivo afetado.
- `weight`: um valor inteiro entre 10 e 1000.

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

- `host`: executa o contêiner no namespace de cgroup do runtime do contêiner.
- `private`: executa o contêiner em seu próprio namespace de cgroup privado.

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

Se o valor for `null`, o comando padrão da imagem é usado.

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
Isso concede ao contêiner acesso à configuração e a monta como arquivos no
sistema de arquivos do contêiner do serviço.
O local do ponto de montagem dentro do contêiner é, por padrão,
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

- `source`: o nome da configuração conforme ela existe na plataforma.
- `target`: o caminho e o nome do arquivo a ser montado nos contêineres de
  tarefa do serviço.
  O padrão é `/<source>` se não for especificado.
- `uid` e `gid`: o UID ou GID numérico do proprietário do arquivo de
  configuração montado dentro dos contêineres de tarefa do serviço.
- `mode`: as [permissões](https://wintelguy.com/permissions-calc.pl) do arquivo
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

#### Sintaxe curta

A variante de sintaxe curta especifica apenas os nomes dos serviços das
dependências.
As dependências de serviço resultam nos seguintes comportamentos:

- O Compose cria os serviços na ordem de dependência.
  No exemplo a seguir, `db` e `redis` são criados antes de `web`.

- O Compose remove os serviços na ordem de dependência.
  No exemplo a seguir, `web` é removido antes de `db` e `redis`.

Exemplo simples:

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

O Compose garante que os serviços de dependência tenham sido iniciados antes de
iniciar um serviço dependente.
Com a sintaxe curta, o Compose não aguarda que os serviços de dependência
estejam "saudáveis" antes de iniciar um serviço dependente.

#### Sintaxe longa

A sintaxe de formato longo permite a configuração de campos adicionais que não
podem ser expressos no formato curto.

- `restart`: quando definido como `true`, o Compose reinicia este serviço após
  atualizar o serviço de dependência.
  Isso se aplica a uma reinicialização explícita controlada por uma operação do
  Compose e exclui a reinicialização automatizada pelo runtime do contêiner após
  a parada do contêiner.
  Introduzido na versão
  [2.17.0](https://github.com/docker/compose/releases/tag/v2.17.0) do Docker
  Compose.

- `condition`: define a condição sob a qual a dependência é considerada
  satisfeita.
  - `service_started`: equivalente à sintaxe curta descrita anteriormente.
  - `service_healthy`: especifica que se espera que uma dependência esteja
    "saudável" (conforme indicado por [`healthcheck`](#healthcheck)) antes de
    iniciar um serviço dependente.
  - `service_completed_successfully`: especifica que se espera que uma
    dependência seja executada até a conclusão bem-sucedida antes de iniciar um
    serviço dependente.
- `required`: quando definido como `false`, o Compose apenas emite um aviso caso
  o serviço de dependência não tenha sido iniciado ou não esteja disponível.
  Se não for definido, o valor padrão de `required` é `true`.
  Introduzido na versão
  [2.20.0](https://github.com/docker/compose/releases/tag/v2.20.0) do Docker
  Compose.

As dependências de serviço geram os seguintes comportamentos:

- O Compose cria serviços na ordem de dependência.
  No exemplo a seguir, `db` e `redis` são criados antes de `web`.

- O Compose aguarda a conclusão bem-sucedida das verificações de saúde das
  dependências marcadas com `service_healthy`.
  No exemplo a seguir, espera-se que `db` esteja "saudável" antes de `web` ser
  criado.

- O Compose remove serviços na ordem de dependência.
  No exemplo a seguir, `web` é removido antes de `db` e `redis`.

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

O Compose garante que os serviços de dependência sejam iniciados antes de
iniciar um serviço dependente.
O Compose garante que os serviços de dependência marcados com `service_healthy`
estejam "saudáveis" antes de iniciar um serviço dependente.

### `deploy`

`deploy` especifica a configuração para a implantação e o ciclo de vida dos
serviços, conforme definido [na Especificação de Deploy do Compose](deploy.md).

### `develop`

{{< summary-bar feature_name="Compose develop" >}}

`develop` especifica a configuração de desenvolvimento para manter um contêiner
sincronizado com o código-fonte, conforme definido na
[Seção de desenvolvimento](develop.md).

### `device_cgroup_rules`

`device_cgroup_rules` define uma lista de regras de cgroup de dispositivos para
este contêiner.
O formato é o mesmo especificado pelo kernel do Linux no
[Control Groups Device Whitelist Controller](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v1/devices.html).

```yml
device_cgroup_rules:
  - 'c 1:3 mr'
  - 'a 7:* rmw'
```

### `devices`

`devices` define uma lista de mapeamentos de dispositivos para contêineres
criados, no formato `HOST_PATH:CONTAINER_PATH[:CGROUP_PERMISSIONS]`.

```yml
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
  - "/dev/sda:/dev/xvda:rwm"
```

`devices` também pode usar a sintaxe da
[CDI](https://github.com/cncf-tags/container-device-interface) para permitir que
o runtime de contêiner selecione um dispositivo:

```yml
devices:
  - "vendor1.com/device=gpu"
```

### `dns`

`dns` define servidores DNS personalizados a serem configurados na interface de
rede do contêiner.
Pode ser um valor único ou uma lista.

```yml
dns: 8.8.8.8
```

```yml
dns:
  - 8.8.8.8
  - 9.9.9.9
```

### `dns_opt`

`dns_opt` lista opções de DNS personalizadas a serem passadas para o resolvedor
de DNS do contêiner (arquivo `/etc/resolv.conf` no Linux).

```yml
dns_opt:
  - use-vc
  - no-tld-query
```

### `dns_search`

`dns_search` define domínios de busca DNS personalizados para configurar na
interface de rede do contêiner.
Pode ser um valor único ou uma lista.

```yml
dns_search: example.com
```

```yml
dns_search:
  - dc1.example.com
  - dc2.example.com
```

### `domainname`

`domainname` declara um nome de domínio personalizado a ser usado para o
contêiner de serviço.
Ele deve ser um nome de host válido segundo a RFC 1123.

### `driver_opts`

{{< summary-bar feature_name="Compose driver opts" >}}

`driver_opts` especifica uma lista de opções, na forma de pares chave-valor, a
serem passadas para o driver.
Essas opções dependem do driver.

```yml
services:
  app:
    networks:
      app_net:
        driver_opts:
          com.docker.network.bridge.host_binding_ipv4: "127.0.0.1"
```

Consulte a [documentação dos drivers de rede](/manuals/engine/network/_index.md)
para obter mais informações.

### `entrypoint`

O `entrypoint` declara o ponto de entrada padrão para o contêiner do serviço.
Isso substitui a instrução `ENTRYPOINT` do Dockerfile do serviço.

Se o `entrypoint` não for nulo, o Compose ignora qualquer comando padrão da
imagem, como, por exemplo, a instrução `CMD` no Dockerfile.

Veja também [`command`](#command) para definir ou substituir o comando padrão a
ser executado pelo processo de ponto de entrada.

Em sua forma abreviada, o valor pode ser definido como uma string:

```yml
entrypoint: /code/entrypoint.sh
```

Alternativamente, o valor também pode ser uma lista, de maneira semelhante ao
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

Se o valor for `null`, o ponto de entrada padrão da imagem será usado.

Se o valor for `[]` (lista vazia) ou `''` (string vazia), o ponto de entrada
padrão declarado pela imagem será ignorado, ou seja, substituído por um valor
vazio.

### `env_file`

{{% include "compose/services-env-file.md" %}}

```yml
env_file: .env
```

Caminhos relativos são resolvidos a partir da pasta que contém o arquivo
Compose.
Como caminhos absolutos impedem a portabilidade do arquivo Compose, o Compose
emite um aviso quando tal caminho é usado para definir `env_file`.

Variáveis de ambiente declaradas na seção [`environment`](#environment)
substituem esses valores.
Isso se aplica mesmo que tais valores estejam vazios ou indefinidos.

O `env_file` também pode ser uma lista.
Os arquivos da lista são processados de cima para baixo.
Caso uma mesma variável seja especificada em dois arquivos de ambiente,
prevalece o valor definido no último arquivo da lista.

```yml
env_file:
  - ./a.env
  - ./b.env
```

Elementos de lista também podem ser declarados como um mapeamento, o que permite
definir atributos adicionais.

#### `required`

{{< summary-bar feature_name="Compose required" >}}

O atributo `required` tem `true` como valor padrão.
Quando `required` é definido como `false` e o arquivo `.env` está ausente, o
Compose ignora a entrada silenciosamente.

```yml
env_file:
  - path: ./default.env
    required: true # default
  - path: ./override.env
    required: false
```

#### `format`

{{< summary-bar feature_name="Compose format" >}}

O atributo `format` permite usar um formato de arquivo alternativo para o
`env_file`.
Quando não definido, o `env_file` é analisado conforme as regras do Compose
descritas em [Formato do `env_file`](#env_file-format).

O formato `raw` permite usar um `env_file` com itens chave=valor, mas sem que o
Compose tente analisar o valor para interpolação.
Isso permite passar valores como estão, incluindo aspas e cifrão (`$`).

```yml
env_file:
  - path: ./default.env
    format: raw
```

#### Formato do `env_file`

Cada linha em um arquivo `.env` deve estar no formato `VAR[=[VAL]]`.
As seguintes regras de sintaxe são aplicadas:

- Linhas que começam com `#` são processadas como comentários e ignoradas.
- Linhas em branco são ignoradas.
- Valores sem aspas ou entre aspas duplas (`"`) passam por
  [Interpolação](interpolation.md).
- Cada linha representa um par chave-valor.
  Os valores podem, opcionalmente, estar entre aspas.
- O delimitador que separa a chave do valor pode ser `=` ou `:`.
- Espaços antes e depois do valor são ignorados.
  - `VAR=VAL` -> `VAL`
  - `VAR="VAL"` -> `VAL`
  - `VAR='VAL'` -> `VAL`
  - `VAR: VAL` -> `VAL`
  - `VAR = VAL  ` -> `VAL` <!-- markdownlint-disable-line no-space-in-code -->
- Comentários na mesma linha para valores sem aspas devem ser precedidos por um
  espaço.
  - `VAR=VAL # comentário` -> `VAL`
  - `VAR=VAL# não é um comentário` -> `VAL# não é um comentário`
- Comentários na mesma linha para valores entre aspas devem vir após as aspas de
  fechamento.
  - `VAR="VAL # não é um comentário"` -> `VAL # não é um comentário`
  - `VAR="VAL" # comentário` -> `VAL`
- Valores entre aspas simples (`'`) são tratados literalmente.
  - `VAR='$OTHER'` -> `$OTHER`
  - `VAR='${OTHER}'` -> `${OTHER}`
- Aspas podem ser escapadas com `\`.
  - `VAR='Let\'s go!'` -> `Let's go!`
  - `VAR="{\"hello\": \"json\"}"` -> `{"hello": "json"}`
- Sequências de escape comuns de shell, incluindo `\n`, `\r`, `\t` e `\\`, são
  suportadas em valores entre aspas duplas.
  - `VAR="algum\tvalor"` -> `algum  valor`
  - `VAR='algum\tvalor'` -> `algum\tvalor`
  - `VAR=algum\tvalor` -> `algum\tvalor`

`VAL` pode ser omitido; nesses casos, o valor da variável é uma string vazia.
`=VAL` pode ser omitido; nesses casos, a variável não é definida (unset).

```bash
# Define o ambiente Rails/Rack
RACK_ENV=development
VAR="quoted"
```

### `environment`

{{% include "compose/services-environment.md" %}}

As variáveis de ambiente podem ser declaradas por uma única chave (sem valor
para o sinal de igual).
Nesse caso, o Compose depende de você para resolver o valor.
Se o valor não for resolvido, a variável é removida e excluída do ambiente do
contêiner de serviço.

Sintaxe do mapa:

```yml
environment:
  RACK_ENV: development
  SHOW: "true"
  USER_INPUT:
```

Sintaxe de array:

```yml
environment:
  - RACK_ENV=development
  - SHOW=true
  - USER_INPUT
```

Quando tanto `env_file` quanto `environment` são definidos para um serviço, os
valores definidos em `environment` têm precedência.

### `expose`

O `expose` define a porta (de entrada) ou o intervalo de portas que o Compose
expõe a partir do contêiner.
Essas portas devem ser acessíveis a serviços vinculados e não devem ser
publicadas na máquina host.
Apenas as portas internas do contêiner podem ser especificadas.

A sintaxe é `<número_da_porta>/[<proto>]` ou
`<porta_inicial-porta_final>/[<proto>]` para um intervalo de portas.
Quando não definido explicitamente, o protocolo `tcp` é usado.

```yml
expose:
  - "3000"
  - "8000"
  - "8080-8085/tcp"
```

> [!NOTE]
>
> Se o Dockerfile da imagem já expuser portas, elas ficarão visíveis para outros
> contêineres na rede, mesmo que `expose` não esteja definido no seu arquivo
> Compose.

### `extends`

O `extends` permite compartilhar configurações comuns entre arquivos diferentes
ou até mesmo entre projetos totalmente distintos.
Com o `extends`, você pode definir um conjunto comum de opções de serviço em um
único local e referenciá-lo de qualquer lugar.
É possível referenciar outro arquivo Compose e selecionar um serviço que você
deseja usar também em sua própria aplicação, com a possibilidade de substituir
alguns atributos para atender às suas necessidades específicas.

Você pode usar o `extends` em qualquer serviço, juntamente com outras chaves de
configuração.
O valor de `extends` deve ser um mapeamento definido com uma chave obrigatória
`service` e uma chave opcional `file`.

```yaml
extends:
  file: common.yml
  service: webapp
```

- `service`: define o nome do serviço que está sendo referenciado como base; por
  exemplo, `web` ou `database`.
- `file`: o local de um arquivo de configuração do Compose que define esse
  serviço.

`extends` não é suportado ao realizar o deploy com `docker stack deploy`.

#### Restrições

Quando um serviço é referenciado usando `extends`, ele pode declarar
dependências de outros recursos.
Essas dependências podem ser definidas explicitamente por meio de atributos como
`volumes`, `networks`, `configs`, `secrets`, `links`, `volumes_from` ou
`depends_on`.
Alternativamente, as dependências podem referenciar outro serviço usando a
sintaxe `service:{name}` em declarações de namespace, como `ipc`, `pid` ou
`network_mode`.

O Compose não importa automaticamente esses recursos referenciados para o modelo
estendido.
É sua responsabilidade garantir que todos os recursos necessários sejam
declarados explicitamente no modelo que usa o `extends`.

Referências circulares com `extends` não são suportadas; o Compose retorna um
erro quando uma delas é detectada.

#### Localizando o serviço referenciado

O valor de `file` pode ser:

- Ausente.
  Isso indica que está sendo referenciado outro serviço dentro do mesmo arquivo
  Compose.
- Um caminho de arquivo, que pode ser:
  - Caminho relativo.
    Esse caminho é considerado relativo à localização do arquivo Compose
    principal.
  - Caminho absoluto.

O serviço indicado por `service` deve estar presente no arquivo Compose
referenciado e identificado.
O Compose retorna um erro se:

- O serviço indicado por `service` não for encontrado.
- O arquivo Compose indicado por `file` não for encontrado.

#### Mesclando definições de serviço

Duas definições de serviço, a principal, no arquivo Compose atual, e a
referenciada, especificada por `extends`, são mescladas da seguinte forma:

- Mapeamentos: chaves nos mapeamentos da definição de serviço principal
  substituem chaves nos mapeamentos da definição de serviço referenciada.
  Chaves que não são substituídas são incluídas como estão.
- Sequências: os itens são combinados em uma nova sequência.
  A ordem dos elementos é preservada, com os itens referenciados vindo primeiro
  e os itens principais depois.
- Escalares: chaves na definição de serviço principal têm precedência sobre
  chaves na definição referenciada.

##### Mapeamentos

As seguintes chaves devem ser tratadas como mapeamentos: `annotations`,
`build.args`, `build.labels`, `build.extra_hosts`, `deploy.labels`,
`deploy.update_config`, `deploy.rollback_config`, `deploy.restart_policy`,
`deploy.resources.limits`, `environment`, `healthcheck`, `labels`,
`logging.options`, `sysctls`, `storage_opt`, `extra_hosts`, `ulimits`.

Uma exceção que se aplica a `healthcheck` é que o mapeamento principal não pode
especificar `disable: true` a menos que o mapeamento referenciado também
especifique `disable: true`.
O Compose retorna um erro nesse caso.

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

Gera a seguinte configuração para o serviço `cli`.
O mesmo resultado é obtido se a sintaxe de array for usada.

```yaml
environment:
  PORT: 8080
  TZ: utc
image: busybox
```

Os itens em `blkio_config.device_read_bps`, `blkio_config.device_read_iops`,
`blkio_config.device_write_bps`, `blkio_config.device_write_iops`, `devices` e
`volumes` também são tratados como mapeamentos onde a chave é o caminho de
destino dentro do contêiner.

Por exemplo, a seguinte entrada:

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

Gera a seguinte configuração para o serviço `cli`.
Observe que o caminho montado agora aponta para o novo nome de volume e a flag
`ro` foi aplicada.

```yaml
image: busybox
volumes:
- cli-volume:/var/lib/backup/data:ro
```

Se a definição de serviço referenciada contiver um mapeamento `extends`, os
itens sob ele serão simplesmente copiados para a nova definição mesclada.
O processo de mesclagem é então iniciado novamente até que não haja mais chaves
`extends`.

Por exemplo, a seguinte entrada:

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

Gera a seguinte configuração para o serviço `cli`.
Aqui, o serviço `cli` obtém a chave `user` do serviço `common`, que, por sua
vez, obtém essa chave do serviço `base`.

```yaml
image: busybox
user: root
```

##### Sequências

As seguintes chaves devem ser tratadas como sequências: `cap_add`, `cap_drop`,
`configs`, `deploy.placement.constraints`, `deploy.placement.preferences`,
`deploy.reservations.generic_resources`, `device_cgroup_rules`, `expose`,
`external_links`, `ports`, `secrets`, `security_opt`.
Quaisquer duplicatas resultantes da mesclagem são removidas para que a sequência
contenha apenas elementos únicos.

Por exemplo, a seguinte entrada:

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

Gera a seguinte configuração para o serviço `cli`.

```yaml
image: busybox
security_opt:
- label=role:ROLE
- label=user:USER
```

Caso a sintaxe de lista seja utilizada, as seguintes chaves também devem ser
tratadas como sequências: `dns`, `dns_search`, `env_file`, `tmpfs`.
Ao contrário dos campos de sequência mencionados anteriormente, duplicatas
resultantes da mesclagem não são removidas.

##### Escalares

Quaisquer outras chaves permitidas na definição do serviço devem ser tratadas
como escalares.

### `external_links`

`external_links` vincula contêineres de serviço a serviços gerenciados fora da
sua aplicação Compose.
`external_links` define o nome de um serviço existente a ser recuperado
utilizando o mecanismo de busca da plataforma.
Um apelido no formato `SERVICE:ALIAS` pode ser especificado.

```yml
external_links:
  - redis
  - database:mysql
  - database:postgresql
```

### `extra_hosts`

O `extra_hosts` adiciona mapeamentos de nomes de host à configuração da
interface de rede do contêiner (`/etc/hosts` no Linux).

#### Sintaxe curta

A sintaxe curta utiliza strings simples em uma lista.
Os valores devem definir o nome do host e o endereço IP para hosts adicionais no
formato `HOSTNAME=IP`.

```yml
extra_hosts:
  - "somehost=162.242.195.82"
  - "otherhost=50.31.209.229"
  - "myhostv6=::1"
```

Endereços IPv6 podem ser colocados entre colchetes, por exemplo:

```yml
extra_hosts:
  - "myhostv6=[::1]"
```

O separador `=` é o preferido, mas `:` também pode ser usado.
Introduzido na versão
[2.24.1](https://github.com/docker/compose/releases/tag/v2.24.1) do Docker
Compose.
Por exemplo:

```yml
extra_hosts:
  - "somehost:162.242.195.82"
  - "myhostv6:::1"
```

#### Sintaxe longa

Alternativamente, `extra_hosts` pode ser definido como um mapeamento entre
hostname(s) e IP(s).

```yml
extra_hosts:
  somehost: "162.242.195.82"
  otherhost: "50.31.209.229"
  myhostv6: "::1"
```

O Compose cria uma entrada correspondente com o endereço IP e o nome do host na
configuração de rede do contêiner; isso significa que, no Linux, o arquivo
`/etc/hosts` recebe linhas adicionais:

```console
162.242.195.82  somehost
50.31.209.229   otherhost
::1             myhostv6
```

### `gpus`

{{< summary-bar feature_name="Compose gpus" >}}

`gpus` especifica os dispositivos de GPU a serem alocados para uso pelo
contêiner.
Isso equivale a uma [solicitação de dispositivo](deploy.md#devices) com uma
capacidade `gpu` implícita.

```yaml
services:
  model:
    gpus:
      - driver: 3dfx
        count: 2
```

`gpus` também pode ser definido como a string `all` para alocar todos os
dispositivos de GPU disponíveis ao contêiner.

```yaml
services:
  model:
    gpus: all
```

### `group_add`

`group_add` especifica grupos adicionais, por nome ou número, dos quais o
usuário dentro do contêiner deve fazer parte.

Um exemplo de onde isso é útil é quando vários contêineres (executados com
usuários diferentes) precisam ler ou gravar o mesmo arquivo em um volume
compartilhado.
Esse arquivo pode pertencer a um grupo compartilhado por todos os contêineres
e especificado em `group_add`.

```yml
services:
  myservice:
    image: alpine
    group_add:
      - mail
```

Executar `id` dentro do contêiner criado deve mostrar que o usuário pertence ao
grupo `mail`, o que não aconteceria se `group_add` não tivesse sido declarado.

### `healthcheck`

{{% include "compose/services-healthcheck.md" %}}

Para mais informações sobre `HEALTHCHECK`, consulte a
[referência do Dockerfile](/reference/dockerfile.md#healthcheck).

```yml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
  start_interval: 5s
```

`interval`, `timeout`, `start_period` e `start_interval` são
[especificados como durações](extension.md#specifying-durations).
Introduzidos na versão
[2.20.2](https://github.com/docker/compose/releases/tag/v2.20.2) do Docker
Compose.

`test` define o comando que o Compose executa para verificar a saúde do
contêiner.
Ele pode ser uma string ou uma lista.
Se for uma lista, o primeiro item deve ser `NONE`, `CMD` ou `CMD-SHELL`.
Se for uma string, é equivalente a especificar `CMD-SHELL` seguido por essa
string.

```yml
# Acessa a aplicação web local
test: ["CMD", "curl", "-f", "http://localhost"]
```

Usar `CMD-SHELL` executa o comando configurado como uma string utilizando o
shell padrão do contêiner (`/bin/sh` no Linux).
As duas formas a seguir são equivalentes:

```yml
test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
```

```yml
test: curl -f https://localhost || exit 1
```

`NONE` desativa a verificação de saúde e é útil principalmente para desativar a
instrução `HEALTHCHECK` do Dockerfile definida pela imagem Docker do serviço.
Alternativamente, a verificação de saúde definida pela imagem pode ser
desativada definindo `disable: true`:

```yml
healthcheck:
  disable: true
```

### `hostname`

`hostname` declara um nome de host personalizado a ser usado para o contêiner de
serviço.
Ele deve ser um nome de host válido segundo a RFC 1123.

### `image`

`image` especifica a imagem a partir da qual o contêiner será iniciado.
`image` deve seguir o formato de imagem endereçável da
[Open Container Specification](https://github.com/opencontainers/org/blob/master/docs/docs/introduction/digests.md),
como `[<registry>/][<project>/]<image>[:<tag>|@<digest>]`.

```yml
    image: redis
    image: redis:5
    image: redis@sha256:0ed5d5928d4737458944eb604cc8509e245c3e19d02ad83935398bc4b991aac7
    image: library/redis
    image: docker.io/library/redis
    image: my_private.registry:5000/redis
```

Se a imagem não existir na plataforma, o Compose tenta baixá-la com base na
`pull_policy`.
Se você também estiver utilizando a
[Especificação de Build do Compose](build.md), existem opções alternativas para
controlar a precedência entre baixar a imagem e construí-la a partir do
código-fonte; no entanto, baixar a imagem é o comportamento padrão.

`image` pode ser omitido de um arquivo Compose, desde que uma seção `build` seja
declarada.
Se você não estiver utilizando a Especificação de Build do Compose, o Compose
não funcionará caso o campo `image` esteja ausente do arquivo Compose.

### `init`

`init` executa um processo de inicialização (PID 1) dentro do contêiner,
responsável por encaminhar sinais e coletar processos encerrados.
Defina esta opção como `true` para habilitar esse recurso para o serviço.

```yml
services:
  web:
    image: alpine:latest
    init: true
```

O binário `init` usado é específico para a plataforma.

### `ipc`

`ipc` configura o modo de isolamento de IPC definido pelo contêiner de serviço.

- `shareable`: atribui ao contêiner seu próprio namespace de IPC privado, com a
  possibilidade de compartilhá-lo com outros contêineres.
- `service:{nome}`: Faz com que o contêiner ingresse no namespace de IPC (do
  tipo `shareable`) de outro contêiner.

```yml
    ipc: "shareable"
    ipc: "service:[nome do serviço]"
```

### `isolation`

`isolation` especifica a tecnologia de isolamento de um contêiner.
Os valores suportados dependem da plataforma.

### `labels`

`labels` adicionam metadados aos contêineres.
Você pode usar um array ou um mapa.

Recomenda-se usar a notação de DNS reverso para evitar conflitos entre seus
rótulos e aqueles utilizados por outros softwares.

```yml
labels:
  com.example.description: "Aplicação web de contabilidade"
  com.example.department: "Finanças"
  com.example.rotulo-com-valor-vazio: ""
```

```yml
labels:
  - "com.example.description=Aplicação web de contabilidade"
  - "com.example.department=Finanças"
  - "com.example.rotulo-com-valor-vazio"
```

O Compose cria contêineres com rótulos canônicos:

- `com.docker.compose.project`: definido em todos os recursos criados pelo
  Compose com o nome do projeto da pessoa usuária.
- `com.docker.compose.service`: definido nos contêineres de serviço com o nome do
  serviço, conforme definido no arquivo do Compose.

O prefixo de rótulo `com.docker.compose` é reservado.
Especificar rótulos com esse prefixo no arquivo do Compose resulta em um erro de
tempo de execução.

### `label_file`

{{< summary-bar feature_name="Compose label file" >}}

O atributo `label_file` permite carregar rótulos para um serviço a partir de um
arquivo externo ou de uma lista de arquivos.
Isso oferece uma maneira prática de gerenciar múltiplos rótulos sem
sobrecarregar o arquivo do Compose.

O arquivo utiliza um formato de chave-valor, semelhante ao `env_file`.
É possível especificar vários arquivos como uma lista.
Ao usar múltiplos arquivos, eles são processados na ordem em que aparecem na
lista.
Se o mesmo rótulo for definido em vários arquivos, o valor do último arquivo da
lista substituirá os anteriores.

```yaml
services:
  one:
    label_file: ./app.labels

  two:
    label_file:
      - ./app.labels
      - ./additional.labels
```

Se um rótulo for definido tanto no `label_file` quanto no atributo `labels`, o
valor em [labels](#labels) terá precedência.

### `links`

O `links` define uma conexão de rede com contêineres em outro serviço.
Especifique o nome do serviço e um alias para o link (`SERVICE:ALIAS`) ou apenas
o nome do serviço.

```yml
web:
  links:
    - db
    - db:database
    - redis
```

Os contêineres do serviço vinculado podem ser acessados por meio de um hostname
idêntico ao alias — ou ao nome do serviço, caso nenhum alias seja especificado.

Não é necessário utilizar links para permitir a comunicação entre serviços.
Quando nenhuma configuração de rede específica é definida, qualquer serviço
consegue acessar outro serviço utilizando o nome deste na rede `default`.
Se os serviços especificarem as redes às quais estão conectados, os `links` não
substituem essa configuração de rede.
Serviços que não estão conectados a uma rede compartilhada não conseguem se
comunicar entre si.
O Compose não emite avisos sobre inconsistências na configuração.

Os links também estabelecem uma dependência implícita entre serviços, da mesma
forma que o [`depends_on`](#depends_on), determinando assim a ordem de
inicialização dos serviços.

### `logging`

`logging` define a configuração de registro de logs para o serviço.

```yml
logging:
  driver: syslog
  options:
    syslog-address: "tcp://192.168.0.42:123"
```

O nome `driver` especifica um driver de log para os contêineres do serviço.
Os valores padrão e disponíveis dependem da plataforma.
Opções específicas do driver podem ser definidas em `options` como pares
chave-valor.

### `mac_address`

> Disponível a partir da versão 2.24.0 do Docker Compose.

`mac_address` define um endereço MAC para o contêiner do serviço.

> [!NOTE]
>
> Runtimes de contêiner podem rejeitar esse valor; por exemplo, o Docker Engine
> >= v25.0.
> Nesse caso, você deve usar [networks.mac_address](#mac_address) em vez disso.

### `mem_limit`

`mem_limit` configura um limite para a quantidade de memória que um contêiner
pode alocar, definido como uma string que expressa um
[valor em bytes](extension.md#specifying-byte-values).

Quando definido, `mem_limit` deve ser consistente com o atributo `limits.memory`
na [Especificação de Deploy](deploy.md#memory).

### `mem_reservation`

`mem_reservation` configura uma reserva na quantidade de memória que um
container pode alocar, definida como uma string que expressa um
[valor em bytes](extension.md#specifying-byte-values).

Quando definido, `mem_reservation` deve ser consistente com o atributo
`reservations.memory` na [Especificação de Deploy](deploy.md#memory).

### `mem_swappiness`

`mem_swappiness` define, como uma porcentagem (um valor entre 0 e 100), a
propensão do kernel do host para mover para o swap páginas de memória anônima
usadas por um contêiner.

- `0`: desativa o swap de páginas anônimas.
- `100`: define todas as páginas anônimas como passíveis de swap.

O valor padrão depende da plataforma.

### `memswap_limit`

`memswap_limit` define a quantidade de memória que o contêiner tem permissão
para mover para o disco.
Este é um atributo modificador que só faz sentido se
[`memory`](deploy.md#memory) também estiver definido.
O uso de swap permite que o contêiner grave o excedente de memória no disco
quando esgota toda a memória disponível para ele.
Há uma penalidade de desempenho para aplicações que frequentemente movem memória
para o disco.

- Se `memswap_limit` for definido como um número inteiro positivo, então tanto
  `memory` quanto `memswap_limit` devem ser definidos.
  `memswap_limit` representa a quantidade total de memória e swap que pode ser
  usada, e `memory` controla a quantidade de memória que não é swap.
  Portanto, se `memory`="300m" e `memswap_limit`="1g", o contêiner pode usar
  300m de memória e 700m (1g - 300m) de swap.
- Se `memswap_limit` for definido como 0, a configuração é ignorada e o valor é
  tratado como não definido.
- Se `memswap_limit` for definido com o mesmo valor de `memory`, e `memory` for
  um número inteiro positivo, o contêiner não terá acesso a swap.
- Se `memswap_limit` não estiver definido e `memory` estiver definido, o
  contêiner poderá usar uma quantidade de swap igual ao valor de `memory`, caso
  o host tenha memória swap configurada.
  Por exemplo, se `memory`="300m" e `memswap_limit` não estiver definido, o
  contêiner poderá usar um total de 600m entre memória e swap.
- Se `memswap_limit` for explicitamente definido como -1, o contêiner terá
  permissão para usar swap ilimitado, até o limite disponível no sistema host.

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
