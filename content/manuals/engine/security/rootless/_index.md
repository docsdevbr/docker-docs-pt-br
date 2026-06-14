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

source_url: https://github.com/docker/docs/blob/main/content/manuals/engine/security/rootless/_index.md
source_revision: 2d7809c7a74ba1e99609a44f421a15cbf8a4a4bc
translation_status: ready

description: >-
  Execute o daemon do Docker como um usuário não root (modo rootless).
keywords: segurança, namespaces, rootless
title: Modo rootless
weight: 10
---

O modo rootless permite executar o daemon e os contêineres do Docker como um
usuário não root para mitigar possíveis vulnerabilidades no daemon e no ambiente
de execução do contêiner.

O modo rootless não requer privilégios de root, mesmo durante a instalação do
daemon do Docker, desde que os [pré-requisitos](#pré-requisitos) sejam
atendidos.

## Como funciona

O modo rootless executa o daemon e os contêineres do Docker em um namespace de
usuário.
Isso é semelhante ao [modo `userns-remap`](../userns-remap.md), exceto que no
modo `userns-remap`, o próprio daemon é executado com privilégios de root,
enquanto no modo rootless, tanto o daemon quanto o contêiner são executados sem
privilégios de root.

Os dois modos também diferem na forma como mapeiam os UIDs e GIDs do contêiner
para o host: consulte [mapeamento de UID/GID](uid-gid-mapping/) para obter
detalhes.

O modo rootless não usa binários com bits `SETUID` ou capacidades de arquivo,
exceto `newuidmap` e `newgidmap`, que são necessários para permitir que vários
UIDs/GIDs sejam usados no namespace do usuário.

## Pré-requisitos

- Você deve instalar `newuidmap` e `newgidmap` no host.
  Esses comandos são fornecidos pelo pacote `uidmap` na maioria das
  distribuições.
- `/etc/subuid` e `/etc/subgid` devem conter pelo menos 65.536 UIDs/GIDs
  subordinados para o usuário.
  No exemplo a seguir, o usuário `testuser` possui 65.536 UIDs/GIDs subordinados
  (231072-296607).

```console
$ id -u
1001
$ whoami
testuser
$ grep ^$(whoami): /etc/subuid
testuser:231072:65536
$ grep ^$(whoami): /etc/subgid
testuser:231072:65536
```

O script `dockerd-rootless-setuptool.sh install` (veja a seguir) exibe
automaticamente a ajuda quando os pré-requisitos não forem atendidos.

## Instalação

> [!NOTE]
>
> Se o daemon Docker do sistema já estiver em execução, considere desativá-lo:
>
>```console
>$ sudo systemctl disable --now docker.service docker.socket
>$ sudo rm /var/run/docker.sock
>```
>
> Caso opte por não desligar o serviço e o socket `docker`, você precisará usar
> o parâmetro `--force` na próxima seção.
> Não há problemas conhecidos, mas até que você desligue e desative o serviço,
> você ainda estará executando o Docker com privilégios de root.

{{< tabs >}}
{{< tab name="Com pacotes (RPM/DEB)" >}}

Se você instalou o Docker 20.10 ou posterior com
[pacotes RPM/DEB](/engine/install), você deve ter o arquivo
`dockerd-rootless-setuptool.sh` em `/usr/bin`.

Execute `dockerd-rootless-setuptool.sh install` como um usuário sem privilégios
de root para configurar o daemon:

```console
$ dockerd-rootless-setuptool.sh install
[INFO] Creating /home/testuser/.config/systemd/user/docker.service
...
[INFO] Installed docker.service successfully.
[INFO] To control docker.service, run: `systemctl --user (start|stop|restart) docker.service`
[INFO] To run docker.service on system startup, run: `sudo loginctl enable-linger testuser`

[INFO] Creating CLI context "rootless"
Successfully created context "rootless"
[INFO] Using CLI context "rootless"
Current context is now "rootless"

[INFO] Make sure the following environment variable(s) are set (or add them to ~/.bashrc):
export PATH=/usr/bin:$PATH

[INFO] Some applications may require the following environment variable too:
export DOCKER_HOST=unix:///run/user/1000/docker.sock
```

Se o arquivo `dockerd-rootless-setuptool.sh` não estiver presente, talvez seja
necessário instalar manualmente o pacote `docker-ce-rootless-extras`, por
exemplo:

```console
$ sudo apt-get install -y docker-ce-rootless-extras
```

{{< /tab >}}
{{< tab name="Sem pacotes" >}}

Se você não tiver permissão para executar gerenciadores de pacotes como
`apt-get` e `dnf`, considere usar o script de instalação disponível em
[https://get.docker.com/rootless](https://get.docker.com/rootless).
Como pacotes estáticos não estão disponíveis para `s390x`, ele não é compatível
com `s390x`.

```console
$ curl -fsSL https://get.docker.com/rootless | sh
...
[INFO] Creating /home/testuser/.config/systemd/user/docker.service
...
[INFO] Installed docker.service successfully.
[INFO] To control docker.service, run: `systemctl --user (start|stop|restart) docker.service`
[INFO] To run docker.service on system startup, run: `sudo loginctl enable-linger testuser`

[INFO] Creating CLI context "rootless"
Successfully created context "rootless"
[INFO] Using CLI context "rootless"
Current context is now "rootless"

[INFO] Make sure the following environment variable(s) are set (or add them to ~/.bashrc):
export PATH=/home/testuser/bin:$PATH

[INFO] Some applications may require the following environment variable too:
export DOCKER_HOST=unix:///run/user/1000/docker.sock
```

Os arquivos binários serão instalados em `~/bin`.

{{< /tab >}}
{{< /tabs >}}

Execute o comando `docker info` para confirmar que o cliente `docker` está se
conectando ao daemon rootless:

```console
$ docker info
Client: Docker Engine - Community
 Version:    28.3.3
 Context:    rootless
...
Server:
...
 Security Options:
  seccomp
   Profile: builtin
  rootless
  cgroupns
...
```

Consulte a seção [Solução de problemas](./troubleshoot.md) se você encontrou um
erro.
