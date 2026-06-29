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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/deploy.md
source_revision: 70d0c07c698bfc18bbe08b44c85f9dc859bd7718
translation_status: ready

title: Especificação de Deploy do Compose
description: Saiba mais sobre a Especificação de Deploy do Compose
keywords: >-
  compose, especificação do compose, referência de arquivo do compose,
  especificação de deploy do Compose
aliases:
  - /compose/compose-file/deploy/
weight: 140
---

{{% include "compose/deploy.md" %}}

## Atributos

### `endpoint_mode`

`endpoint_mode` especifica um método de descoberta de serviço para clientes
externos que se conectam a um serviço.
A Especificação de Deploy do Compose define dois valores canônicos:

* `endpoint_mode: vip`: atribui ao serviço um IP virtual (VIP) que atua como a
  interface para os clientes acessarem o serviço em uma rede.
  A plataforma roteia as requisições entre o cliente e os nós que executam o
  serviço, sem que o cliente saiba quantos nós estão participando do serviço,
  seus endereços IP ou portas.

* `endpoint_mode: dnsrr`: a plataforma configura entradas DNS para o serviço de
  forma que uma consulta DNS pelo nome do serviço retorne uma lista de endereços
  IP (round-robin DNS), e o cliente se conecta diretamente a um deles.

```yml
services:
  frontend:
    image: example/webapp
    ports:
      - "8080:80"
    deploy:
      mode: replicated
      replicas: 2
      endpoint_mode: vip
```

### `labels`

`labels` especifica metadados para o serviço.
Esses rótulos são definidos apenas no serviço e não em nenhum contêiner do
serviço.
Isso pressupõe que a plataforma tenha algum conceito nativo de "serviço" que
possa corresponder ao modelo de aplicação Compose.

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      labels:
        com.example.description: "This label will appear on the web service"
```

### `mode`

`mode` define o modelo de replicação usado para executar um serviço ou job
(tarefa).
As opções incluem:

- `global`: garante que exatamente uma tarefa seja executada continuamente por
  nó físico até ser interrompida.
- `replicated`: executa continuamente um número especificado de tarefas em todos
  os nós até ser interrompida (padrão).
- `replicated-job`: executa um número definido de tarefas até atingir um estado
  de conclusão (termina com código 0).
  - O número total de tarefas é determinado por `replicas`.
  - A concorrência pode ser limitada usando a opção `max-concurrent` (somente
    via CLI).
- `global-job`: executa uma tarefa por nó físico com um estado de conclusão
  (termina com código 0).
  - Executa automaticamente em novos nós à medida que são adicionados.

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      mode: global

  batch-job:
    image: example/processor
    deploy:
      mode: replicated-job
      replicas: 5

  maintenance:
    image: example/updater
    deploy:
      mode: global-job
```

> [!NOTE]
>
> - Os modos de trabalho (`replicated-job` e `global-job`) são projetados para
>   tarefas que são concluídas e encerradas com o código 0.
> - As tarefas concluídas permanecem até serem removidas explicitamente.
> - Opções como `max-concurrent` para controlar a concorrência são suportadas
>   apenas via CLI e não estão disponíveis no Compose.

Para obter informações mais detalhadas sobre opções e comportamento de tarefas,
consulte a
[documentação da CLI do Docker](/reference/cli/docker/service/create/#running-as-a-job).

### `placement`

`placement` especifica restrições e preferências para a plataforma selecionar um
nó físico para executar contêineres de serviço.

#### `constraints`

`constraints` define uma propriedade obrigatória que o nó da plataforma deve
atender para executar o contêiner de serviço.
Para mais exemplos, consulte a
[documentação de referência da CLI](/reference/cli/docker/service/create/#constraint).

```yml
deploy:
  placement:
    constraints:
      - node.labels.disktype==ssd
```

#### `preferences`

`preferences` define uma estratégia (atualmente `spread` é a única estratégia
suportada) para distribuir as tarefas uniformemente entre os valores do rótulo
do nó do datacenter.
Para mais exemplos, consulte a
[documentação de referência da CLI](/reference/cli/docker/service/create/#placement-pref).

```yml
deploy:
  placement:
    preferences:
      - spread: node.labels.zone
```

### `replicas`

Se o serviço for `replicated` (que é o padrão), `replicas` especifica o número
de contêineres que devem estar em execução a qualquer momento.

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      mode: replicated
      replicas: 6
```

### `resources`

`resources` configura as restrições de recursos físicos para que o contêiner
seja executado na plataforma.
Essas restrições podem ser configuradas da seguinte forma:

- `limits`: a plataforma deve impedir que o contêiner aloque mais recursos.
- `reservations`: a plataforma deve garantir que o contêiner possa alocar pelo
  menos a quantidade configurada.

```yml
services:
  frontend:
    image: example/webapp
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 50M
          pids: 1
        reservations:
          cpus: '0.25'
          memory: 20M
```

#### `cpus`

`cpus` configura um limite ou reserva para a quantidade de recursos de CPU
disponíveis, em número de núcleos, que um contêiner pode usar.

#### `memory`

`memory` configura um limite ou reserva para a quantidade de memória que um
contêiner pode alocar, definida como uma string que expressa um
[valor em bytes](extension.md#specifying-byte-values).

#### `pids`

`pids` ajusta o limite de PIDs de um contêiner, definido como um número inteiro.

#### `devices`

`devices` configura reservas dos dispositivos que um contêiner pode usar.
Contém uma lista de reservas, cada uma definida como um objeto com os seguintes
parâmetros: `capabilities`, `driver`, `count`, `device_ids` e `options`.

Os dispositivos são reservados usando uma lista de capacidades, tornando
`capabilities` o único campo obrigatório.
Um dispositivo deve atender a todas as capacidades solicitadas para que a
reserva seja bem-sucedida.

##### `capabilities`

As `capabilities` são definidas como uma lista de strings, expressando
capacidades genéricas e específicas do driver.
As seguintes capacidades genéricas são reconhecidas atualmente:

- `gpu`: acelerador gráfico
- `tpu`: acelerador de IA

Para evitar conflitos de nomes, as capacidades específicas do driver devem ser
prefixadas com o nome do driver.
Por exemplo, reservar um acelerador NVIDIA com suporte a CUDA pode ser feito da
seguinte forma:

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["nvidia-compute"]
```

##### `driver`

É possível solicitar um driver diferente para o(s) dispositivo(s) reservado(s)
usando o campo `driver`.
O valor é especificado como uma string.

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["nvidia-compute"]
          driver: nvidia
```

##### `count`

Se `count` estiver definido como `all` ou não for especificado, o Compose
reserva todos os dispositivos que atendem aos recursos solicitados.
Caso contrário, o Compose reserva pelo menos o número de dispositivos
especificado.
O valor é especificado como um número inteiro.

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["tpu"]
          count: 2
```

Os campos `count` e `device_ids` são exclusivos.
O Compose retorna um erro se ambos forem especificados.

##### `device_ids`

Se `device_ids` for definido, o Compose reserva os dispositivos com os IDs
especificados, desde que atendam aos recursos solicitados.
O valor é especificado como uma lista de strings.

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["gpu"]
          device_ids: ["GPU-f123d1c9-26bb-df9b-1c23-4a731f61d8c7"]
```

Os campos `count` e `device_ids` são exclusivos.
O Compose retorna um erro se ambos forem especificados.

##### `options`

Opções específicas do driver podem ser definidas com `options` como pares de
chave-valor.

```yml
deploy:
  resources:
    reservations:
      devices:
        - capabilities: ["gpu"]
          driver: gpuvendor
          options:
            virtualization: false
```

### `restart_policy`

`restart_policy` configura se e como os contêineres devem ser reiniciados quando
forem encerrados.
Se `restart_policy` não estiver definido, o Compose considera o campo `restart`
definido pela configuração do serviço.

- `condition`. Quando definido como:
  - `none`, os contêineres não são reiniciados automaticamente,
    independentemente do status de saída.
  - `on-failure`, o contêiner é reiniciado se for encerrado devido a um erro,
    que se manifesta como um código de saída diferente de zero.
  - `any` (padrão), os contêineres são reiniciados independentemente do status
    de saída.
- `delay`: quanto tempo esperar entre as tentativas de reinicialização,
  especificado como uma [duração](extension.md#specifying-durations).
  O padrão é 0, o que significa que as tentativas de reinicialização podem
  ocorrer imediatamente.
- `max_attempts`: o número máximo de tentativas de reinicialização com falha
  permitidas antes de desistir.
  (Padrão: tentativas ilimitadas.)
  Uma tentativa falha só conta para o limite de `max_attempts` se o contêiner
  não reiniciar com sucesso dentro do tempo definido por `window`.
  Por exemplo, se `max_attempts` estiver definido como `2` e o contêiner não
  reiniciar dentro do período definido na primeira tentativa, o Compose
  continuará tentando até que ocorram duas tentativas falhas, mesmo que isso
  signifique tentar mais de duas vezes.
- `window`: o tempo de espera após uma reinicialização para determinar se ela
  foi bem-sucedida, especificado como uma
  [duração](extension.md#specifying-durations) (padrão: o resultado é avaliado
  imediatamente após a reinicialização).

```yml
deploy:
  restart_policy:
    condition: on-failure
    delay: 5s
    max_attempts: 3
    window: 120s
```

### `rollback_config`

`rollback_config` configura como o serviço deve ser revertido em caso de falha
na atualização.

- `parallelism`: o número de contêineres a serem revertidos por vez.
  Se definido como `0`, todos os contêineres serão revertidos simultaneamente.
- `delay`: o tempo de espera entre a reversão de cada grupo de contêineres
  (padrão: `0s`).
- `failure_action`: o que fazer se uma reversão falhar.
  Uma das opções `continue` ou `pause` (padrão: `pause`).
- `monitor`: duração após cada atualização de tarefa para monitorar falhas
  `(ns|us|ms|s|m|h)` (padrão: `0s`).
- `max_failure_ratio`: taxa de falhas tolerada durante uma reversão (padrão:
  `0`).
- `order`: ordem das operações durante as reversões.
  Uma das opções `stop-first` (a tarefa antiga é interrompida antes de iniciar a
  nova), ou `start-first` (a nova tarefa é iniciada primeiro e as tarefas em
  execução se sobrepõem brevemente) (o padrão é `stop-first`).

### `update_config`

`update_config` configura como o serviço deve ser atualizado.
Útil para configurar atualizações contínuas.

- `parallelism`: o número de contêineres a serem atualizados por vez.
- `delay`: o tempo de espera entre as atualizações de um grupo de contêineres.
- `failure_action`: o que fazer se uma atualização falhar.
  Uma das opções `continue`, `rollback` ou `pause` (padrão: `pause`).
- `monitor`: duração após cada atualização de tarefa para monitorar falhas
  `(ns|us|ms|s|m|h)` (padrão: `0s`).
- `max_failure_ratio`: taxa de falhas tolerada durante uma atualização.
- `order`: ordem das operações durante as atualizações.
  Uma das opções `stop-first` (a tarefa antiga é interrompida antes de iniciar a
  nova), ou `start-first` (a nova tarefa é iniciada primeiro e as tarefas em
  execução se sobrepõem brevemente) (padrão: `stop-first`).

```yml
deploy:
  update_config:
    parallelism: 2
    delay: 10s
    order: stop-first
```
