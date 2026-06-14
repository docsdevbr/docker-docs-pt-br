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

source_url: https://github.com/docker/docs/blob/main/content/manuals/engine/install/fedora.md
source_revision: 39adf04805cce61a88737beead94ac2a7549b475
translation_status: ready

description: >-
  Aprenda como instalar a Docker Engine no Fedora.
  Estas instruções abrangem os diferentes métodos de instalação, como
  desinstalar e os próximos passos.
keywords: >-
  requisitos, dnf, instalação, fedora, instalar fedora, instalar docker engine,
  rpm, instalar, desinstalar, atualizar
title: Instale a Docker Engine no Fedora
linkTitle: Fedora
weight: 40
toc_max: 4
aliases:
  - /engine/installation/fedora/
  - /engine/installation/linux/fedora/
  - /engine/installation/linux/docker-ce/fedora/
  - /install/linux/docker-ce/fedora/
download-url-base: https://download.docker.com/linux/fedora
---

Para começar a usar a Docker Engine no Fedora, certifique-se de que você
[atende aos pré-requisitos](#pré-requisitos) e, em seguida, siga os
[passos da instalação](#métodos-de-instalação).

## Pré-requisitos

### Requisitos do sistema operacional

Para instalar a Docker Engine, você precisa de uma versão com suporte de uma das
seguintes versões do Fedora:

- Fedora 44
- Fedora 43

### Desinstale versões antigas

Antes de instalar a Docker Engine, você precisa desinstalar quaisquer pacotes
conflitantes.

Sua distribuição Linux pode fornecer pacotes Docker não oficiais, que podem
entrar em conflito com os pacotes oficiais fornecidos pelo Docker.
Você deve desinstalar esses pacotes antes de instalar a versão oficial da Docker
Engine.

```console
$ sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

O `dnf` pode informar que você não tem nenhum desses pacotes instalados.

Imagens, contêineres, volumes e redes armazenados em `/var/lib/docker/` não são
removidos automaticamente ao desinstalar o Docker.

## Métodos de instalação

Você pode instalar a Docker Engine de diferentes maneiras, dependendo das suas
necessidades:

- Você pode
  [configurar os repositórios do Docker](#instale-usando-o-repositório) e
  instalar a partir deles, para facilitar as tarefas de instalação e
  atualização.
  Esta é a abordagem recomendada.

- Você pode baixar o pacote RPM,
  [instalá-lo manualmente](#instale-a-partir-de-um-pacote) e gerenciar as
  atualizações de forma completamente manual.
  Isso é útil em situações como a instalação do Docker em sistemas isolados da
  internet.

- Em ambientes de teste e desenvolvimento, você pode usar um
  [script de conveniência](#instale-usando-o-script-de-conveniência) para
  instalar o Docker.

{{% include "engine-license.md" %}}

### Instale usando o repositório rpm {#instale-usando-o-repositório}

Antes de instalar a Docker Engine pela primeira vez em uma nova máquina host,
você precisa configurar o repositório do Docker.
Depois disso, você poderá instalar e atualizar o Docker a partir do repositório.

#### Configure o repositório

```console
$ sudo dnf config-manager addrepo --from-repofile {{% param "download-url-base" %}}/docker-ce.repo
```

#### Instale a Docker Engine

1. Instale os pacotes do Docker.

   {{< tabs >}}
   {{< tab name="Versão mais recente" >}}

   Para instalar a versão mais recente, execute:

   ```console
   $ sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

   Se a aceitação da chave GPG for solicitada, verifique se a impressão
   digital corresponde a `060A 61C5 1B55 8A7F 742B 77AA C52F EB6B 621E 9F35`, e,
   em caso afirmativo, aceite-a.

   Este comando instala o Docker, mas não o inicia.
   Ele também cria um grupo `docker`, porém, não adiciona nenhum usuário ao
   grupo por padrão.

   {{< /tab >}}
   {{< tab name="Versão específica" >}}

   Para instalar uma versão específica, comece listando as versões disponíveis
   no repositório:

   ```console
   $ dnf list docker-ce --showduplicates | sort -r

   docker-ce.x86_64    3:{{% param "docker_ce_version" %}}-1.fc41    docker-ce-stable
   docker-ce.x86_64    3:{{% param "docker_ce_version_prev" %}}-1.fc41    docker-ce-stable
   <...>
   ```

   A lista retornada depende dos repositórios habilitados e é específica para a
   sua versão do Fedora (indicada pelo sufixo `.fc41` neste exemplo).

   Instale uma versão específica pelo seu nome de pacote totalmente qualificado,
   que é o nome do pacote (`docker-ce`) mais a string da versão (segunda
   coluna), separados por um hífen (`-`).
   Por exemplo, `docker-ce-3:{{% param "docker_ce_version" %}}-1.fc41`.

   Substitua `<VERSION_STRING>` pela versão desejada e execute o seguinte
   comando para instalar os pacotes:

   ```console
   $ sudo dnf install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io docker-buildx-plugin docker-compose-plugin
   ```

   Este comando instala o Docker, mas não o inicia.
   Ele também cria um grupo `docker`, porém, não adiciona nenhum usuário ao
   grupo por padrão.

   {{< /tab >}}
   {{< /tabs >}}

2. Inicie a Docker Engine.

   ```console
   $ sudo systemctl enable --now docker
   ```

   Isso configura o serviço systemd do Docker para iniciar automaticamente
   quando você inicializar o sistema.
   Se você não quiser que o Docker inicie automaticamente, use
   `sudo systemctl start docker` em vez disso.

   > [!NOTE]
   >
   > Se o serviço Docker não iniciar e `journalctl -u docker` mostrar
   > `failed to find iptables`, aponte o comando `iptables` para `iptables-nft`
   > usando `alternatives` e reinicie o serviço:
   >
   > ```console
   > $ sudo alternatives --set iptables /usr/bin/iptables-nft
   > $ sudo systemctl restart docker
   > ```

3. Verifique se a instalação foi bem-sucedida executando a imagem `hello-world`:

   ```console
   $ sudo docker run hello-world
   ```

   Este comando baixa uma imagem de teste e a executa em um contêiner.
   Quando o contêiner é executado, ele imprime uma mensagem e é encerrado.

Você instalou e iniciou a Docker Engine com sucesso.

{{% include "root-errors.md" %}}

#### Atualize a Docker Engine

Para atualizar a Docker Engine, siga as
[instruções de instalação](#instale-usando-o-repositório), escolhendo a nova
versão que deseja instalar.

### Instale a partir de um pacote

Se você não puder usar o repositório `rpm` do Docker para instalar a Docker
Engine, você pode baixar o arquivo `.rpm` da sua versão e instalá-lo
manualmente.
Você precisará baixar um novo arquivo sempre que quiser atualizar a Docker
Engine.

<!-- markdownlint-disable-next-line -->
1. Acesse
   [{{% param "download-url-base" %}}/]({{% param "download-url-base" %}}/) e
   escolha sua versão do Fedora.
   Em seguida, navegue até `x86_64/stable/Packages/` e baixe o arquivo `.rpm` da
   versão do Docker que deseja instalar.

2. Instale a Docker Engine, alterando o seguinte caminho para o caminho onde
   você baixou o pacote do Docker.

   ```console
   $ sudo dnf install /caminho/para/o/pacote.rpm
   ```

   O Docker está instalado, mas não foi iniciado.
   O grupo `docker` foi criado, mas nenhum usuário foi adicionado ao grupo.

3. Inicie a Docker Engine.

   ```console
   $ sudo systemctl enable --now docker
   ```

   Isso configura o serviço systemd do Docker para iniciar automaticamente
   quando você inicializar o sistema.
   Se você não quiser que o Docker inicie automaticamente, use
   `sudo systemctl start docker` em vez disso.

   > [!NOTE]
   >
   > Se o serviço Docker não iniciar e `journalctl -u docker` mostrar
   > `failed to find iptables`, aponte o comando `iptables` para `iptables-nft`
   > usando `alternatives` e reinicie o serviço:
   >
   > ```console
   > $ sudo alternatives --set iptables /usr/bin/iptables-nft
   > $ sudo systemctl restart docker
   > ```

4. Verifique se a instalação foi bem-sucedida executando a imagem `hello-world`:

   ```console
   $ sudo docker run hello-world
   ```

   Este comando baixa uma imagem de teste e a executa em um contêiner.
   Quando o contêiner é executado, ele imprime uma mensagem e é encerrado.

Você instalou e iniciou a Docker Engine com sucesso.

{{% include "root-errors.md" %}}

#### Atualize a Docker Engine

Para atualizar a Docker Engine, baixe os arquivos de pacote mais recentes e
repita o [procedimento de instalação](#instale-a-partir-de-um-pacote), usando
`dnf upgrade` em vez de `dnf install`, e aponte para os novos arquivos.

{{% include "install-script.md" %}}

## Desinstale a Docker Engine

1. Desinstale os pacotes Docker Engine, CLI, containerd e Docker Compose:

   ```console
   $ sudo dnf remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
   ```

2. Imagens, contêineres, volumes ou arquivos de configuração personalizados no
   seu host não são removidos automaticamente.
   Para excluir todas as imagens, contêineres e volumes:

   ```console
   $ sudo rm -rf /var/lib/docker
   $ sudo rm -rf /var/lib/containerd
   ```

Você precisa excluir manualmente todos os arquivos de configuração editados.

## Próximos passos

- Continue para [Passos da pós-instalação no Linux](linux-postinstall.md).
