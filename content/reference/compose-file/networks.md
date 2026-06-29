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

source_url: https://github.com/docker/docs/blob/main/content/reference/compose-file/networks.md
source_revision: 0220ef67450476a5e2c18f481f29a3ee1b352f56
translation_status: ready

linkTitle: Redes
title: Defina e gerencie redes no Docker Compose
description: >-
  Aprenda como configurar e controlar redes usando o elemento de nível superior
  networks no Docker Compose.
keywords: >-
  compose, especificação do compose, redes, referência de arquivo compose
aliases:
 - /compose/compose-file/06-networks/
weight: 30
---

{{% include "compose/networks.md" %}}

Para usar uma rede em vários serviços, você deve conceder acesso explicitamente
a cada serviço usando o atributo [networks](services.md) dentro do elemento de
nível superior `services`.
O elemento de nível superior `networks` possui uma sintaxe adicional que fornece
um controle mais granular.

## Exemplos

### Exemplo básico

No exemplo a seguir, em tempo de execução, as redes `front-tier` e `back-tier`
são criadas e o serviço `frontend` é conectado às redes `front-tier` e
`back-tier`.

```yml
services:
  frontend:
    image: example/webapp
    networks:
      - front-tier
      - back-tier

networks:
  front-tier:
  back-tier:
```

### Exemplo avançado

```yml
services:
  proxy:
    build: ./proxy
    networks:
      - frontend
  app:
    build: ./app
    networks:
      - frontend
      - backend
  db:
    image: postgres:18
    networks:
      - backend

networks:
  frontend:
    # Especifique as opções do driver
    driver: bridge
    driver_opts:
      com.docker.network.bridge.host_binding_ipv4: "127.0.0.1"
  backend:
    # Use um driver personalizado
    driver: custom-driver
```

Este exemplo mostra um arquivo Compose que define duas redes personalizadas.
O serviço `proxy` está isolado do serviço `db`, pois eles não compartilham uma
rede em comum.
Somente o serviço `app` pode se comunicar com ambos.

## A rede padrão

Quando um arquivo Compose não declara redes explicitamente, o Compose usa uma
rede `default` implícita.
Serviços sem uma declaração explícita de [`networks`](services.md#networks) são
conectados pelo Compose a esta rede `default`:

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
networks:
  default: {}
```

Você pode personalizar a rede `default` com uma declaração explícita:

```yml
networks:
  default:
    name: uma_rede  # Use um nome personalizado
    driver_opts:    # Passe opções para o driver para criação de rede
      com.docker.network.bridge.host_binding_ipv4: 127.0.0.1
```

Para opções, consulte a
[documentação da Docker Engine](https://docs.docker.com/engine/network/drivers/bridge/#options).

## Atributos

### `attachable`

Se `attachable` estiver definido como `true`, os contêineres independentes
poderão se conectar a esta rede, além dos serviços.
Se um contêiner independente se conectar à rede, ele poderá se comunicar com
serviços e outros contêineres independentes que também estejam conectados à
rede.

```yml
networks:
  mynet1:
    driver: overlay
    attachable: true
```

### `driver`

`driver` especifica qual driver deve ser usado para esta rede.
O Compose retorna um erro se o driver não estiver disponível na plataforma.

```yml
networks:
  db-data:
    driver: bridge
```

Para obter mais informações sobre drivers e opções disponíveis, consulte
[Drivers de rede](/manuals/engine/network/drivers/_index.md).

### `driver_opts`

`driver_opts` especifica uma lista de opções como pares chave-valor para passar
para o driver.
Essas opções são dependentes do driver.

```yml
networks:
  frontend:
    driver: bridge
    driver_opts:
      com.docker.network.bridge.host_binding_ipv4: "127.0.0.1"
```

Consulte a [documentação dos drivers de rede](/manuals/engine/network/_index.md)
para obter mais informações.

### `enable_ipv4`

{{< summary-bar feature_name="Compose enable ipv4" >}}

`enable_ipv4` pode ser usado para desativar a atribuição de endereços IPv4.

```yml
  networks:
    ip6net:
      enable_ipv4: false
      enable_ipv6: true
```

### `enable_ipv6`

`enable_ipv6` habilita a atribuição de endereços IPv6.

```yml
  networks:
    ip6net:
      enable_ipv6: true
```

### `external`

Se definido como `true`:
  - `external` especifica que o ciclo de vida desta rede é mantido fora do ciclo
    de vida da aplicação.
    O Compose não tenta criar essas redes e retorna um erro se uma não existir.
  - Todos os outros atributos, exceto o nome, são irrelevantes.
    Se o Compose detectar qualquer outro atributo, ele rejeitará o arquivo
    Compose como inválido.

No exemplo a seguir, `proxy` é o gateway para o mundo externo.
Em vez de tentar criar uma rede, o Compose consulta a plataforma em busca de uma
rede existente chamada `outside` e conecta os contêineres do serviço `proxy` a
ela.

```yml
services:
  proxy:
    image: example/proxy
    networks:
      - outside
      - default
  app:
    image: example/app
    networks:
      - default

networks:
  outside:
    external: true
```

### `ipam`

`ipam` especifica uma configuração IPAM personalizada.
Trata-se de um objeto com diversas propriedades, cada uma opcional:

- `driver`: driver IPAM personalizado, em vez do padrão.
- `config`: uma lista com zero ou mais elementos de configuração, cada um
  contendo:
  - `subnet`: sub-rede no formato CIDR que representa um segmento de rede.
  - `ip_range`: intervalo de IPs a partir do qual alocar IPs de contêineres.
  - `gateway`: gateway IPv4 ou IPv6 para a sub-rede principal.
  - `aux_addresses`: endereços IPv4 ou IPv6 auxiliares usados pelo driver de
     rede, como um mapeamento de hostname para IP.
- `options`: opções específicas do driver como um mapeamento de chave-valor.

```yml
networks:
  mynet1:
    ipam:
      driver: default
      config:
        - subnet: 172.28.0.0/16
          ip_range: 172.28.5.0/24
          gateway: 172.28.5.254
          aux_addresses:
            host1: 172.28.1.5
            host2: 172.28.1.6
            host3: 172.28.1.7
      options:
        foo: bar
        baz: "0"
```

### `internal`

Por padrão, o Compose fornece conectividade externa às redes.
`internal`, quando definido como `true`, permite que você crie uma rede isolada
externamente.

### `labels`

Adicione metadados às redes usando `labels`.
Você pode usar um array ou um dicionário.

Recomenda-se o uso da notação DNS reversa para evitar conflitos entre suas
labels e as usadas por outros softwares.

```yml
networks:
  mynet1:
    labels:
      com.example.description: "Financial transaction network"
      com.example.department: "Finance"
      com.example.label-with-empty-value: ""
```

```yml
networks:
  mynet1:
    labels:
      - "com.example.description=Financial transaction network"
      - "com.example.department=Finance"
      - "com.example.label-with-empty-value"
```

O Compose define as labels `com.docker.compose.project` e
`com.docker.compose.network`.

### `name`

`name` define um nome personalizado para a rede.
O campo `name` pode ser usado para referenciar redes que contenham caracteres
especiais.
O nome é usado como está e não é vinculado ao nome do projeto.

```yml
networks:
  network1:
    name: my-app-net
```

Também pode ser usado em conjunto com a propriedade `external` para definir a
rede da plataforma que o Compose deve recuperar, normalmente usando um parâmetro
para que o arquivo Compose não precise codificar valores específicos de tempo de
execução:

```yml
networks:
  network1:
    external: true
    name: "${NETWORK_ID}"
```

## Recursos adicionais

Para mais exemplos, consulte
[Redes no Compose](/manuals/compose/how-tos/networking.md).
