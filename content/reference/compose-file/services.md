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
translation_status: ready

linkTitle: Serviços
title: Defina serviços no Docker Compose
description: >-
  Explore todos os atributos que o elemento de nível superior services pode ter.
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
O Compose oferece suporte à construção de imagens Docker usando essa definição
de serviço.
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
> de variáveis de ambiente, você precisará executá-lo explicitamente em um
> shell.
> Por exemplo:
>
> ```yaml
> command: /bin/sh -c 'echo "hello $$HOSTNAME"'
> ```

O valor também pode ser uma lista, semelhante à
[sintaxe exec-form](/reference/dockerfile.md#exec-form)
usada pelo [Dockerfile](/reference/dockerfile.md#exec-form).

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
criados, no formato `CAMINHO_NO_HOST:CAMINHO_NO_CONTÊINER[:PERMISSÕES_DO_CGROUP]`.

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

Caso a sintaxe de lista seja usada, as seguintes chaves também devem ser
tratadas como sequências: `dns`, `dns_search`, `env_file`, `tmpfs`.
Ao contrário dos campos de sequência mencionados anteriormente, duplicatas
resultantes da mesclagem não são removidas.

##### Escalares

Quaisquer outras chaves permitidas na definição do serviço devem ser tratadas
como escalares.

### `external_links`

`external_links` vincula contêineres de serviço a serviços gerenciados fora da
sua aplicação Compose.
`external_links` define o nome de um serviço existente a ser recuperado usando o
mecanismo de busca da plataforma.
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

A sintaxe curta usa strings simples em uma lista.
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

Usar `CMD-SHELL` executa o comando configurado como uma string usando o shell
padrão do contêiner (`/bin/sh` no Linux).
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
Se você também estiver usando a [Especificação de Build do Compose](build.md),
existem opções alternativas para controlar a precedência entre baixar a imagem e
construí-la a partir do código-fonte; no entanto, baixar a imagem é o
comportamento padrão.

`image` pode ser omitido de um arquivo Compose, desde que uma seção `build` seja
declarada.
Se você não estiver usando a Especificação de Build do Compose, o Compose não
funcionará caso o campo `image` esteja ausente do arquivo Compose.

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
rótulos e aqueles usados por outros softwares.

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

O arquivo usa um formato de chave-valor, semelhante ao `env_file`.
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

Não é necessário usar links para permitir a comunicação entre serviços.
Quando nenhuma configuração de rede específica é definida, qualquer serviço
consegue acessar outro serviço usando o nome deste na rede `default`.
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
contêiner pode alocar, definida como uma string que expressa um
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

`models` define quais modelos de IA o serviço deve usar em tempo de execução.
Cada modelo referenciado deve ser definido no
[elemento de nível superior `models`](models.md).

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

Quando um serviço está vinculado a um modelo, o Docker Compose injeta variáveis
de ambiente para passar detalhes de conexão e identificadores do modelo para o
contêiner.
Isso permite que a aplicação localize e se comunique com o modelo dinamicamente
em tempo de execução, sem a necessidade de definir valores fixos no código.

#### Sintaxe longa

A sintaxe longa oferece maior controle sobre os nomes das variáveis de ambiente.

- `endpoint_var` define o nome da variável de ambiente que armazena a URL do
  executor do modelo.
- `model_var` define o nome da variável de ambiente que armazena o identificador
  do modelo.

Se qualquer um deles for omitido, o Compose gera automaticamente os nomes das
variáveis de ambiente com base na chave do modelo, seguindo estas regras:

- Converte a chave do modelo para letras maiúsculas.
- Substitui quaisquer caracteres '-' por '_'.
- Acrescenta `_URL` para a variável de endpoint.

### `network_mode`

`network_mode` define o modo de rede de um contêiner de serviço.

- `bridge`: conecta o contêiner à rede bridge padrão do Docker em vez de a uma
  rede específica do projeto.
  Contêineres na rede bridge padrão não conseguem resolver reciprocamente pelo
  nome do serviço.
  Em vez disso, use uma rede definida pela pessoa usuária para resolução DNS.
- `none`: desativa toda a rede do contêiner.
- `host`: concede ao contêiner acesso direto à interface de rede do host.
- `service:{nome}`: concede ao contêiner acesso ao contêiner especificado
  referenciando seu nome de serviço.
- `container:{nome}`: concede ao contêiner acesso ao contêiner especificado
  referenciando seu ID de contêiner.

Para mais informações sobre redes de contêineres, consulte a
[documentação da Docker Engine](/manuals/engine/network/_index.md#container-networks).

```yml
    network_mode: "bridge"
    network_mode: "host"
    network_mode: "none"
    network_mode: "service:[service name]"
```

Quando definido, o atributo [`networks`](#networks) não é permitido, e o Compose
rejeita qualquer arquivo Compose que contenha ambos os atributos.

### `networks`

{{% include "compose/services-networks.md" %}}

```yml
services:
  some-service:
    networks:
      - some-network
      - other-network
```

Para mais informações sobre o elemento de nível superior `networks`, consulte
[Networks](networks.md).

#### Rede padrão implícita

Se `networks` estiver vazio ou ausente do arquivo Compose, o Compose considera
uma definição implícita para que o serviço seja conectado à rede `default`:

```yml
services:
  some-service:
    image: foo
```

Este exemplo é, na verdade, equivalente a:

```yml
services:
  some-service:
    image: foo
    networks:
      default: {}
```

Se você não quiser que o serviço esteja conectado a uma rede, deve definir
[`network_mode: none`](#network_mode).

#### `aliases`

`aliases` declara nomes de host alternativos para o serviço na rede.
Outros contêineres na mesma rede podem usar o nome do serviço ou um alias para
se conectar a um dos contêineres do serviço.

Como os `aliases` têm escopo de rede, o mesmo serviço pode ter aliases
diferentes em redes diferentes.

> [!NOTE]
>
> Um alias de rede pode ser compartilhado por vários contêineres e até mesmo por
> vários serviços.
> Nesse caso, não há garantia de qual contêiner será resolvido pelo nome.

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

No exemplo a seguir, o serviço `frontend` consegue acessar o serviço `backend`
pelos nomes de host `backend` ou `database` na rede `back-tier`.
O serviço `monitoring` consegue acessar esse mesmo serviço `backend` pelos nomes
`backend` ou `mysql` na rede `admin`.

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

`interface_name` permite especificar o nome da interface de rede usada para
conectar um serviço a uma determinada rede.
Isso garante uma nomenclatura de interface consistente e previsível entre
serviços e redes.

```yaml
services:
  backend:
    image: alpine
    command: ip link show
    networks:
      back-tier:
        interface_name: eth0
```

Executar a aplicação de exemplo do Compose mostra:

```console
backend-1  | 11: eth0@if64: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP
```

#### `ipv4_address`, `ipv6_address`

Especifica um endereço IP estático para um contêiner de serviço ao ingressar na
rede.

A configuração de rede correspondente na
[seção de nível superior networks](networks.md) deve ter um atributo `ipam` com
configurações de sub-rede que abranjam cada endereço estático.

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

`link_local_ips` especifica uma lista de endereços IP de link-local.
Endereços IP de link-local são endereços especiais que pertencem a uma sub-rede
conhecida e são gerenciados exclusivamente pela pessoa operadora, geralmente
dependendo da arquitetura onde são implantados.

Exemplo:

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

`mac_address` define o endereço MAC usado pelo contêiner do serviço ao se
conectar a esta rede específica.

#### `driver_opts`

`driver_opts` especifica uma lista de opções, no formato chave-valor, a serem
passadas para o driver.
Essas opções dependem do driver.
Consulte a documentação do driver para obter mais informações.

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

A rede com a maior `gw_priority` é selecionada como o gateway padrão para o
contêiner de serviço.
Se não for especificada, o valor padrão é 0.

No exemplo a seguir, `app_net_2` será selecionada como o gateway padrão.

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

`priority` indica a ordem em que o Compose conecta os contêineres do serviço às
suas redes.
Se não for especificado, o valor padrão é 0.

Se o runtime do contêiner aceitar um atributo `mac_address` no nível do serviço,
ele será aplicado à rede com a maior `priority`.
Em outros casos, use o atributo `networks.mac_address`.

`priority` não afeta qual rede é selecionada como gateway padrão.
Use o atributo [`gw_priority`](#gw_priority) para isso.

`priority` não controla a ordem em que as conexões de rede são adicionadas ao
contêiner, nem pode ser usado para determinar o nome do dispositivo (`eth0`,
etc.) dentro do contêiner.

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

Se `oom_kill_disable` estiver definido, o Compose configura a plataforma para
não encerrar o contêiner em caso de escassez de memória.

### `oom_score_adj`

O `oom_score_adj` ajusta a preferência quanto a quais contêineres serão
encerrados pela plataforma em caso de escassez de memória.
O valor deve estar na faixa de -1000 a 1000.

### `pid`

`pid` define o modo PID para o contêiner criado pelo Compose.
Os valores suportados dependem da plataforma.

### `pids_limit`

`pids_limit` ajusta o limite de PIDs de um contêiner.
Defina como -1 para PIDs ilimitados.

```yml
pids_limit: 10
```

Quando definido, `pids_limit` deve ser consistente com o atributo `pids` na
[Especificação de Deploy](deploy.md#pids).

### `platform`

`platform` define a plataforma de destino na qual os contêineres do serviço são
executados.
Usa a sintaxe `os[/arch[/variant]]`.

Os valores de `os`, `arch` e `variant` devem estar conforme a convenção
usada pela
[Especificação de Imagem OCI](https://github.com/opencontainers/image-spec/blob/v1.0.2/image-index.md).

O Compose usa esse atributo para determinar qual versão da imagem é baixada e/ou
em qual plataforma a construção do serviço é realizada.

```yml
platform: darwin
platform: windows/amd64
platform: linux/arm64/v8
```

### `ports`

{{% include "compose/services-ports.md" %}}

> [!NOTE]
>
> O mapeamento de portas não deve ser usado com `network_mode: host`.
> Fazer isso causa um erro de tempo de execução, pois `network_mode: host` já
> expõe as portas do contêiner diretamente à rede do host, tornando o mapeamento
> de portas desnecessário.

#### Sintaxe curta

A sintaxe curta é uma string separada por dois-pontos para definir o IP do host,
a porta do host e a porta do contêiner, no formato:

`[HOST:]CONTAINER[/PROTOCOL]` onde:

- `HOST` é `[IP:](porta | intervalo)` (opcional).
  Se não for definido, a vinculação ocorre em todas as interfaces de rede
  (`0.0.0.0`).
- `CONTAINER` é `porta | intervalo`.
- `PROTOCOL` restringe as portas a um protocolo específico, `tcp` ou `udp`
  (opcional).
  O padrão é `tcp`.

> [!WARNING]
>
> Se você não especificar um IP do host (como `127.0.0.1`), o Docker fará a
> vinculação em todas as interfaces (`0.0.0.0`), contornando as regras de
> firewall do host.
> Isso pode expor o contêiner diretamente à internet se o host tiver um endereço
> IP público.
> Para mais informações, consulte
> [Publicação e mapeamento de portas](/manuals/engine/network/port-publishing.md).

As portas podem ser um valor único ou um intervalo.
`HOST` e `CONTAINER` devem usar intervalos equivalentes.

Você pode especificar ambas as portas (`HOST:CONTAINER`) ou apenas a porta do
contêiner.
Neste último caso, o ambiente de execução do contêiner aloca automaticamente
qualquer porta não atribuída do host.

`HOST:CONTAINER` deve sempre ser especificado como uma string (entre aspas) para
evitar conflitos com
[valores de ponto flutuante em base 60 do YAML](https://yaml.org/type/float.html).

Endereços IPv6 podem ser colocados entre colchetes.

Exemplos:

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
> Se o mapeamento de IP do host não for suportado por um mecanismo de contêiner,
> o Compose rejeita o arquivo Compose e ignora o IP do host especificado.

#### Sintaxe longa

A sintaxe longa permite configurar campos adicionais que não podem ser expressos
na sintaxe curta.

- `target`: a porta do contêiner.
- `published`: a porta exposta publicamente.
  É definida como uma string e pode ser configurada como um intervalo usando a
  sintaxe `início-fim`.
  Isso significa que a porta efetiva será uma porta disponível dentro do
  intervalo definido.
- `host_ip`: o mapeamento de IP do host.
  Se não for definido, a vinculação ocorre em todas as interfaces de rede
  (`0.0.0.0`).
- `protocol`: o protocolo da porta (`tcp` ou `udp`).
  O padrão é `tcp`.
- `app_protocol`: o protocolo de aplicação (TCP/IP camada 4 / OSI camada 7)
  usado por esta porta.
  É opcional e pode servir como uma dica para o Compose oferecer funcionalidades
  mais ricas para protocolos que ele reconhece.
  Introduzido na versão
  [2.26.0](https://github.com/docker/compose/releases/tag/v2.26.0) do Docker
  Compose.
- `mode`: especifica como a porta é publicada em uma configuração Swarm.
  Se definido como `host`, publica a porta em todos os nós do Swarm.
  Se definido como `ingress`, permite o balanceamento de carga entre os nós do
  Swarm.
  O padrão é `ingress`.
- `name`: um nome legível para a porta, usado para documentar seu uso dentro do
  serviço.

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

`post_start` define uma sequência de hooks de ciclo de vida a serem executados
após a inicialização de um contêiner.
O momento exato da execução do comando não é garantido.

- `command`: especifica o comando a ser executado assim que o contêiner iniciar.
  Este atributo é obrigatório, e você pode optar por usar o formato shell ou o
  formato exec.
- `user`: o usuário que executará o comando.
  Se não for definido, o comando será executado com o mesmo usuário do comando
  principal do serviço.
- `privileged`: permite que o comando `post_start` seja executado com
  privilégios elevados.
- `working_dir`: o diretório de trabalho no qual o comando será executado.
  Se não for definido, a execução ocorrerá no mesmo diretório de trabalho do
  comando principal do serviço.
- `environment`: Define variáveis de ambiente especificamente para o comando
  `post_start`.
  Embora o comando herde as variáveis de ambiente definidas para o comando
  principal do serviço, esta seção permite adicionar novas variáveis ou
  substituir as existentes.

```yaml
services:
  test:
    post_start:
      - command: ./executar_algo_na_inicializacao.sh
        user: root
        privileged: true
        environment:
          - FOO=BAR
```

Para mais informações, consulte
[Use hooks de ciclo de vida](/manuals/compose/how-tos/lifecycle.md).

### `pre_stop`

{{< summary-bar feature_name="Compose pre stop" >}}

`pre_stop` define uma sequência de hooks de ciclo de vida para execução antes
que o contêiner seja parado.
Esses hooks não serão executados se o contêiner parar por conta própria ou for
encerrado abruptamente.

A configuração é equivalente à de `post_start` (#post_start).

### `privileged`

`privileged` configura o contêiner do serviço para ser executado com privilégios
elevados.
O suporte e os impactos reais dependem da plataforma.

### `profiles`

`profiles` define uma lista de perfis nomeados sob os quais o serviço será
habilitado.
Se não for atribuído nenhum perfil, o serviço é sempre iniciado; se for
atribuído, ele só será iniciado se o perfil estiver habilitado.

Quando presentes, os valores de `profiles` devem seguir o formato de expressão
regular `[a-zA-Z0-9][a-zA-Z0-9_.-]+`.

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

`provider` pode ser usado para definir um serviço que o Compose não gerenciará
diretamente.
O Compose delegou o ciclo de vida do serviço a um componente dedicado ou de
terceiros.

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

Quando o Compose executa a aplicação, o binário `awesomecloud` é usado para
gerenciar a configuração do serviço `database`.
O serviço dependente `app` recebe variáveis de ambiente adicionais prefixadas
com o nome do serviço, permitindo que ele acesse o recurso.

A título de exemplo, supondo que a execução do `awesomecloud` tenha gerado as
variáveis `URL` e `API_KEY`, o serviço `app` é executado com as variáveis de
ambiente `DATABASE_URL` e `DATABASE_API_KEY`.

Quando o Compose interrompe a aplicação, o binário `awesomecloud` é usado para
gerenciar o encerramento do serviço `database`.

O mecanismo usado pelo Compose para delegar o ciclo de vida do serviço a um
binário externo está descrito na
[documentação de extensibilidade do Compose](https://github.com/docker/compose/tree/main/docs/extension.md).

Para mais informações sobre o uso do atributo `provider`, consulte
[Use serviços de provedor](/manuals/compose/how-tos/provider-services.md).

#### `type`

O atributo `type` é obrigatório.
Ele define o componente externo usado pelo Compose para gerenciar os eventos de
ciclo de vida de configuração e encerramento.

#### `options`

As `options` são específicas do provedor selecionado e não são validadas pela
especificação do Compose.

### `pull_policy`

`pull_policy` define as decisões que o Compose toma ao iniciar a obtenção de
imagens.
Os valores possíveis são:

- `always`: o Compose sempre baixa a imagem do registro.
- `never`: o Compose não baixa a imagem de um registro e usa a imagem em cache
  na plataforma.
  Se não houver imagem em cache, uma falha é relatada.
- `missing`: o Compose baixa a imagem apenas se ela não estiver disponível no
  cache da plataforma.
  Esta é a opção padrão caso você não esteja usando também a
  [Especificação de Build do Compose](build.md).
  `if_not_present` é considerado um alias para este valor, visando a
  compatibilidade com versões anteriores.
  A tag `latest` é sempre baixada, mesmo quando a política de pull `missing` é
  usada.
- `build`: o Compose constrói a imagem.
  O Compose reconstrói a imagem caso ela já exista.
- `daily`: o Compose verifica se há atualizações da imagem no registro caso o
  último download tenha ocorrido há mais de 24 horas.
- `weekly`: o Compose verifica se há atualizações da imagem no registro caso o
  último download tenha ocorrido há mais de 7 dias.
- `every_<duração>`: o Compose verifica se há atualizações da imagem no registro
  caso o último download tenha ocorrido antes do período definido em
  `<duração>`.
  A duração pode ser expressa em semanas (`w`), dias (`d`), horas (`h`), minutos
  (`m`), segundos (`s`) ou uma combinação deles.

```yaml
services:
  test:
    image: nginx
    pull_policy: every_12h
```

### `read_only`

`read_only` configura o contêiner de serviço para ser criado com um sistema de
arquivos somente leitura.

### `restart`

`restart` define a política que a plataforma aplica quando o contêiner é
encerrado.

- `no`: a política de reinicialização padrão.
  O contêiner não é reiniciado em nenhuma circunstância.
- `always`: a política reinicia o contêiner sempre, até que ele seja removido.
- `on-failure[:max-retries]`: a política reinicia o contêiner se o código de
  saída indicar um erro.
  Opcionalmente, é possível limitar o número de tentativas de reinicialização
  realizadas pelo daemon do Docker.
- `unless-stopped`: a política reinicia o contêiner independentemente do código
  de saída, mas para de reiniciá-lo quando o serviço é interrompido ou removido.

```yml
    restart: "no"
    restart: always
    restart: on-failure
    restart: on-failure:3
    restart: unless-stopped
```

Você pode encontrar informações mais detalhadas sobre políticas de
reinicialização na seção
[Políticas de reinicialização (--restart)](/reference/cli/docker/container/run/#restart)
da página de referência do comando `docker run`.

### `runtime`

`runtime` especifica qual runtime usar para os contêineres do serviço.

Por exemplo, `runtime` pode ser o nome de
[uma implementação da OCI Runtime Spec](https://github.com/opencontainers/runtime-spec/blob/master/implementations.md),
como "runc".

```yml
web:
  image: busybox:latest
  command: true
  runtime: runc
```

O padrão é `runc`.
Para usar um runtime diferente, consulte
[Runtimes alternativos](/manuals/engine/daemon/alternative-runtimes.md).

### `scale`

`scale` especifica o número padrão de contêineres a serem implantados para este
serviço.
Quando ambos são definidos, `scale` deve ser consistente com o atributo
`replicas` na [Especificação de Deploy](deploy.md#replicas).

### `secrets`

{{% include "compose/services-secrets.md" %}}

Há suporte para duas variantes de sintaxe: a sintaxe curta e a sintaxe longa.
Sintaxes longa e curta para secrets podem ser usadas no mesmo arquivo Compose.

O Compose relata um erro se o segredo não existir na plataforma ou não estiver
definido na [seção de nível superior `secrets`](secrets.md) do arquivo Compose.

Definir um segredo na seção de nível superior `secrets` não implica conceder
acesso a ele a nenhum serviço.
Essa concessão deve ser explícita na especificação do serviço, por meio do
elemento de serviço [secrets](secrets.md).

#### Sintaxe curta

A variante de sintaxe curta especifica apenas o nome do segredo.
Isso concede ao contêiner acesso ao segredo e o monta como somente leitura em
`/run/secrets/<nome_do_segredo>` dentro do contêiner.
Tanto o nome de origem quanto o ponto de montagem de destino são definidos como
o nome do segredo.

O exemplo a seguir usa a sintaxe curta para conceder ao serviço `frontend`
acesso ao segredo `server-certificate`.
O valor de `server-certificate` é definido como o conteúdo do arquivo
`./server.cert`.

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

#### Sintaxe longa

A sintaxe longa oferece maior granularidade na forma como o segredo é criado
dentro dos contêineres do serviço.

- `source`: o nome do segredo conforme existe na plataforma.
- `target`: o nome do arquivo a ser montado em `/run/secrets/` no contêiner da
  tarefa do serviço, ou o caminho absoluto do arquivo caso seja necessária uma
  localização alternativa.
  O padrão é `source` se não for especificado.
- `uid` e `gid`: o uid ou gid numérico do proprietário do arquivo dentro de
  `/run/secrets/` nos contêineres da tarefa do serviço.
- `mode`: as [permissões](https://wintelguy.com/permissions-calc.pl) para o
  arquivo a ser montado em `/run/secrets/` nos contêineres da tarefa do serviço,
  em notação octal.
  O valor padrão são permissões de leitura para todos (modo `0444`).
  O bit de escrita deve ser ignorado, caso esteja definido.
  O bit de execução pode ser definido.

Observe que o suporte para os atributos `uid`, `gid` e `mode` no Docker Compose
só é implementado quando a origem do segredo é [`environment`](secrets.md).
Quando a origem é um [`file`](secrets.md), o Compose usa internamente um bind
mount, que não permite o remapeamento de `uid`, e esses atributos são ignorados
silenciosamente.

O exemplo a seguir define o nome do arquivo de segredo `meu-token` dentro do
contêiner, define o modo como `0440` (leitura permitida para o grupo) e define o
usuário e o grupo como `103`.
O valor de `meu-token` é lido a partir da variável de ambiente `MEU_TOKEN`.

```yml
services:
  frontend:
    image: example/webapp
    secrets:
      - source: meu-token
        uid: "103"
        gid: "103"
        mode: 0o440
secrets:
  meu-token:
    environment: "MEU_TOKEN"
```

### `security_opt`

`security_opt` sobrescreve o esquema de rotulagem padrão para cada contêiner.

As opções aceitam a sintaxe `option=value` ou `option:value`.
Para opções booleanas, como `no-new-privileges`, o valor pode ser omitido
completamente, caso em que a opção é tratada como habilitada.
As seguintes sintaxes são todas equivalentes:

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

Para outros esquemas de rotulagem padrão que você pode substituir, consulte
[Configuração de segurança](/reference/cli/docker/container/run/#security-opt).

### `shm_size`

`shm_size` configura o tamanho da memória compartilhada (partição `/dev/shm` no
Linux) permitida para o contêiner do serviço.
É especificado como um [valor em bytes](extension.md#specifying-byte-values).

### `stdin_open`

`stdin_open` configura o contêiner de um serviço para ser executado com um
`stdin` alocado.
Isso equivale a executar um contêiner com a flag `-i`.
Para mais informações, consulte
[Mantenha o stdin aberto](/reference/cli/docker/container/run/#interactive).

Os valores suportados são `true` ou `false`.

### `stop_grace_period`

`stop_grace_period` especifica quanto tempo o Compose deve aguardar ao tentar
parar um contêiner, caso este não processe o sinal `SIGTERM` (ou qualquer sinal
de parada especificado com [`stop_signal`](#stop_signal)), antes de enviar o
sinal `SIGKILL`.
É especificado como uma [duração](extension.md#specifying-durations).

```yml
    stop_grace_period: 1s
    stop_grace_period: 1m30s
```

O valor padrão é de 10 segundos para o encerramento do contêiner antes do envio
do sinal `SIGKILL`.

### `stop_signal`

`stop_signal` define o sinal que o Compose usa para interromper os contêineres
do serviço.
Se não for definido, os contêineres são interrompidos pelo Compose através do
envio de `SIGTERM`.

```yml
stop_signal: SIGUSR1
```

### `storage_opt`

`storage_opt` define opções do driver de armazenamento para um serviço.

```yml
storage_opt:
  size: '1G'
```

### `sysctls`

`sysctls` define parâmetros do kernel a serem configurados no contêiner.
`sysctls` pode usar um array ou um mapa.

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

Você só pode usar sysctls que possuam namespace no kernel.
O Docker não oferece suporte à alteração de sysctls em um contêiner que também
modifiquem o sistema host.
Para uma visão geral dos sysctls suportados, consulte
[configurar parâmetros de kernel com namespace (sysctls) em tempo de execução](/reference/cli/docker/container/run/#sysctl).

### `tmpfs`

`tmpfs` monta um sistema de arquivos temporário dentro do contêiner.
Pode ser um valor único ou uma lista.

```yml
tmpfs:
 - <path>
 - <path>:<options>
```

- `path`: o caminho dentro do contêiner onde o tmpfs será montado.
- `options`: lista de opções separadas por vírgula para a montagem do tmpfs.

Opções disponíveis:

- `mode`: define as permissões do sistema de arquivos.
- `uid`: define o ID do usuário proprietário do tmpfs montado.
- `gid`: define o ID do grupo proprietário do tmpfs montado.

```yml
services:
  app:
    tmpfs:
      - /data:mode=755,uid=1009,gid=1009
      - /run
```

### `tty`

`tty` configura o contêiner de um serviço para ser executado com um TTY.
Isso equivale a executar um contêiner com a flag `-t` ou `--tty`.
Para mais informações, consulte
[Alocar um pseudo-TTY](/reference/cli/docker/container/run/#tty).

Os valores suportados são `true` ou `false`.

### `ulimits`

`ulimits` substitui os `ulimits` padrão de um contêiner.
Ele é especificado como um número inteiro para um limite único ou como um
mapeamento para limites soft/hard.

```yml
ulimits:
  nproc: 65535
  nofile:
    soft: 20000
    hard: 40000
```

### `use_api_socket`

Quando `use_api_socket` está definido, o contêiner consegue interagir com o
mecanismo de contêineres subjacente por meio do socket da API.
Suas credenciais são montadas dentro do contêiner, permitindo que ele atue como
um delegado direto para seus comandos relacionados à engine de contêineres.
Normalmente, comandos executados pelo contêiner podem realizar operações de
`pull` e `push` no seu registro.

### `user`

`user` substitui o usuário usado para executar o processo do contêiner.
O padrão é definido pela imagem, por exemplo, pela instrução `USER` do
Dockerfile.
Se não for definido, o padrão é `root`.

### `userns_mode`

`userns_mode` define o namespace de usuário para o serviço.
Os valores suportados são específicos da plataforma e podem depender da
configuração da plataforma.

```yml
userns_mode: "host"
```

### `uts`

{{< summary-bar feature_name="Compose uts" >}}

`uts` configura o modo de namespace UTS definido para o contêiner de serviço.
Quando não especificado, a atribuição de um namespace UTS fica a critério do
runtime, caso haja suporte.
Os valores disponíveis são:

- `'host'`: faz com que o contêiner use o mesmo namespace UTS do host.

```yml
    uts: "host"
```

### `volumes`

{{% include "compose/services-volumes.md" %}}

O exemplo a seguir mostra um volume nomeado (`db-data`) sendo usado pelo serviço
`backend`, e um bind mount definido para um único serviço.

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

Para mais informações sobre o elemento de nível superior `volumes`, consulte
[Volumes](volumes.md).

#### Sintaxe curta

A sintaxe curta usa uma única string com valores separados por dois-pontos para
especificar uma montagem de volume (`VOLUME:CAMINHO_NO_CONTÊINER`) ou um modo de
acesso (`VOLUME:CAMINHO_NO_CONTÊINER:MODO_DE_ACESSO`).

- `VOLUME`: pode ser um caminho no host, na plataforma que hospeda os
  contêineres (bind mount), ou um nome de volume.
- `CAMINHO_NO_CONTÊINER`: o caminho no contêiner onde o volume é montado.
- `MODO_DE_ACESSO`: uma lista de opções separadas por vírgula (`,`):
  - `rw`: acesso de leitura e escrita.
    Este é o padrão caso nenhum seja especificado.
  - `ro`: acesso somente leitura.
  - `z`: opção do SELinux indicando que o conteúdo do bind mount no host é
    compartilhado entre vários contêineres.
  - `Z`: opção do SELinux indicando que o conteúdo do bind mount no host é
    privado e não compartilhado com outros contêineres.

> [!NOTE]
>
> A opção de re-rotulagem do SELinux para bind mounts é ignorada em plataformas
> sem SELinux.

> [!NOTE]
>
> Caminhos relativos no host são suportados apenas pelo Compose ao realizar a
> implantação em um runtime de contêiner local.
> Isso ocorre porque o caminho relativo é resolvido a partir do diretório pai
> do arquivo Compose, o que só é aplicável no caso local.
> Quando o Compose realiza a implantação em uma plataforma não local, ele
> rejeita arquivos Compose que usam caminhos relativos no host, retornando um
> erro.
> Para evitar ambiguidades com volumes nomeados, caminhos relativos devem sempre
> começar com `.` ou `..`.

> [!NOTE]
>
> Para bind mounts, a sintaxe curta cria um diretório no caminho de origem no
> host, caso ele não exista.
> Isso visa manter a compatibilidade com o `docker-compose` legado.
> Esse comportamento pode ser evitado usando a sintaxe longa e definindo
> `create_host_path` como `false`.

#### Sintaxe longa

A sintaxe longa permite configurar campos adicionais que não podem ser expressos
na forma curta.

- `type`: o tipo de montagem.
  Pode ser `volume`, `bind`, `tmpfs`, `image`, `npipe` ou `cluster`.
- `source`: a origem da montagem, um caminho no host para um bind mount, uma
  referência de imagem Docker para uma montagem de imagem ou o nome de um volume
  definido na [chave de nível superior `volumes`](volumes.md).
  Não se aplica a montagens tmpfs.
- `target`: o caminho no container onde o volume é montado.
- `read_only`: flag para definir o volume como somente leitura.
- `bind`: usado para configurar opções adicionais de bind:
  - `propagation`: o modo de propagação usado para o bind.
  - `create_host_path`: cria um diretório no caminho de origem no host caso não
    exista nada lá.
    O padrão é `true`.
  - `selinux`: a opção de re-rotulagem do SELinux: `z` (compartilhado) ou `Z`
    (privado).
- `volume`: configura opções adicionais de volume:
  - `nocopy`: flag para desabilitar a cópia de dados de um container quando um
    volume é criado.
  - `subpath`: caminho em um volume a ser montado em vez da raiz do volume.
- `tmpfs`: configura opções adicionais de tmpfs:
  - `size`: o tamanho da montagem tmpfs em bytes (numérico ou com unidade de
    medida de bytes).
  - `mode`: o modo de arquivo para a montagem tmpfs, definido como bits de
    permissão Unix em formato octal.
    Introduzido na versão
    [2.14.0](https://github.com/docker/compose/releases/tag/v2.14.0) do Docker
    Compose.
- `image`: configura opções adicionais de imagem:
  - `subpath`: caminho dentro da imagem de origem a ser montado em vez da raiz
    da imagem.
    Disponível na
    [versão 2.35.0 do Docker Compose](https://github.com/docker/compose/releases/tag/v2.35.0).
- `consistency`: os requisitos de consistência da montagem.
  Os valores disponíveis dependem da plataforma.

> [!TIP]
>
> Trabalhando com grandes repositórios ou monorepos, ou com sistemas de arquivos
> virtuais que não acompanham mais o crescimento da sua base de código?
> O Compose agora tira proveito do recurso de
> [compartilhamento de arquivos sincronizado](/manuals/desktop/features/synchronized-file-sharing.md)
> e cria automaticamente compartilhamentos de arquivos para bind mounts.
> Certifique-se de ter acessado o Docker com uma assinatura paga e de ter
> habilitado as opções **Access experimental features** e **Manage Synchronized
> file shares with Compose** nas configurações do Docker Desktop.

### `volumes_from`

`volumes_from` monta todos os volumes de outro serviço ou contêiner.
Você pode especificar, opcionalmente, acesso somente leitura (`ro`) ou leitura e
escrita (`rw`).
Se nenhum nível de acesso for especificado, o acesso de leitura e escrita será
usado.

Você também pode montar volumes de um contêiner que não é gerenciado pelo
Compose usando o prefixo `container:`.

```yaml
volumes_from:
  - nome_do_serviço
  - nome_do_serviço:ro
  - container:nome_do_contêiner
  - container:nome_do_contêiner:rw
```

### `working_dir`

`working_dir` substitui o diretório de trabalho do contêiner definido pela
imagem, por exemplo, o `WORKDIR` do Dockerfile.
