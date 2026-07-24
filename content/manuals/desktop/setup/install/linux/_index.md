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

source_url: https://github.com/docker/docs/blob/main/content/manuals/desktop/setup/install/linux/_index.md
source_revision: e16806830ae126a03c354e0752b31f0c17328b8a
translation_status: ready

description: >-
  Instale o Docker no Linux com facilidade usando nosso guia passo a passo, que
  aborda requisitos do sistema, plataformas suportadas e os próximos passos.
keywords: >-
  linux, instalação do docker no linux, docker linux, docker desktop para linux,
  baixar docker para linux, como instalar docker no linux, alternar contextos do
  docker
title: Instale o Docker Desktop no Linux
linkTitle: Linux
weight: 60
aliases:
  - /desktop/linux/install/
  - /desktop/install/linux-install/
  - /desktop/install/linux/
---

> **Termos do Docker Desktop**
>
> O uso comercial do Docker Desktop em empresas maiores (mais de 250 pessoas
> funcionárias OU mais de US$ 10 milhões em receita anual) requer uma
> [assinatura paga](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopLinuxInstall).

Esta página contém informações sobre requisitos gerais do sistema, plataformas
suportadas e instruções para instalar o Docker Desktop para Linux.

> [!IMPORTANT]
>
> O Docker Desktop no Linux executa uma Máquina Virtual (VM) que cria e usa um
> contexto Docker personalizado, `desktop-linux`, durante a inicialização.
>
> Isso significa que imagens e contêineres implantados no Docker Engine para
> Linux (antes da instalação) não ficam disponíveis no Docker Desktop para
> Linux.
>
> {{< accordion title="Docker Desktop vs. Docker Engine: qual é a diferença?" >}}

> [!IMPORTANT]
>
> Para o uso comercial da Docker Engine obtida por meio do Docker Desktop em
> grandes empresas (com mais de 250 pessoas funcionárias ou receita anual
> superior a US$ 10 milhões), é necessária uma
> [assinatura paga](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopLinuxInstall).

O Docker Desktop para Linux oferece uma interface gráfica intuitiva que
simplifica o gerenciamento de contêineres e serviços.
Ele inclui a Docker Engine, visto que essa é a tecnologia central que sustenta
os contêineres Docker.
O Docker Desktop para Linux também conta com recursos adicionais, como o Docker
Scout e as Docker Extensions.

### Instalação do Docker Desktop e da Docker Engine

O Docker Desktop para Linux e a Docker Engine podem ser instalados lado a lado
na mesma máquina.
O Docker Desktop para Linux armazena contêineres e imagens em um local de
armazenamento isolado em uma VM e oferece controles para restringir
[seus recursos](/manuals/desktop/settings-and-maintenance/settings.md#resources).
O uso de um local de armazenamento dedicado para o Docker Desktop evita que ele
interfira em uma instalação da Docker Engine na mesma máquina.

Embora seja possível executar o Docker Desktop e a Docker Engine
simultaneamente, podem ocorrer situações em que a execução de ambos ao mesmo
tempo cause problemas.
Por exemplo, ao mapear portas de rede (`-p` / `--publish`) para contêineres,
tanto o Docker Desktop quanto a Docker Engine podem tentar reservar a mesma
porta na sua máquina, o que pode levar a conflitos ("port already in use").

Geralmente, recomendamos parar a Docker Engine enquanto você usa o Docker
Desktop, para evitar que a Docker Engine consuma recursos e para prevenir
conflitos como os descritos acima.

Use o seguinte comando para parar o serviço da Docker Engine:

```console
$ sudo systemctl stop docker docker.socket containerd
```

Dependendo da sua instalação, a Docker Engine pode estar configurada para
iniciar automaticamente como um serviço do sistema quando sua máquina for
iniciada.
Use o comando a seguir para desativar o serviço da Docker Engine e impedir que
ele inicie automaticamente:

```console
$ sudo systemctl disable docker docker.socket containerd
```

### Alternando entre o Docker Desktop e a Docker Engine

A CLI do Docker pode ser usada para interagir com múltiplas instâncias da Docker
Engine.
Por exemplo, você pode usar a mesma CLI do Docker para controlar uma Docker
Engine local e para controlar uma instância remota da Docker Engine em execução
na nuvem.
Os [contextos do Docker](/manuals/engine/manage-resources/contexts.md) permitem
alternar entre instâncias da Docker Engine.

Ao instalar o Docker Desktop, um contexto dedicado chamado "desktop-linux" é
criado para interagir com o Docker Desktop.
Na inicialização, o Docker Desktop define automaticamente seu próprio contexto
(`desktop-linux`) como o contexto atual.
Isso significa que os comandos subsequentes da CLI do Docker são direcionados ao
Docker Desktop.
Ao ser encerrado, o Docker Desktop redefine o contexto atual para o contexto
`default`.

Use o comando `docker context ls` para visualizar quais contextos estão
disponíveis em sua máquina.
O contexto atual é indicado por um asterisco (`*`).

```console
$ docker context ls
NAME            DESCRIPTION                               DOCKER ENDPOINT                                  ...
default *       Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                      ...
desktop-linux                                             unix:///home/<usuário>/.docker/desktop/docker.sock  ...
```

Se você tiver o Docker Desktop e a Docker Engine instalados na mesma máquina,
pode executar o comando `docker context use` para alternar entre os contextos
do Docker Desktop e da Docker Engine.
Por exemplo, use o contexto "default" para interagir com a Docker Engine:

```console
$ docker context use default
default
Current context is now "default"
```

E use o contexto `desktop-linux` para interagir com o Docker Desktop:

```console
$ docker context use desktop-linux
desktop-linux
Current context is now "desktop-linux"
```

Consulte a
[documentação do contexto do Docker](/manuals/engine/manage-resources/contexts.md)
para obter mais detalhes.
{{< /accordion >}}

## Plataformas suportadas

O Docker fornece pacotes `.deb` e `.rpm` para as seguintes distribuições Linux
e arquiteturas:

| Plataforma                                  | x86_64 / amd64 |
|:--------------------------------------------|:--------------:|
| [Ubuntu](ubuntu.md)                         |       ✅        |
| [Debian](debian.md)                         |       ✅        |
| [Red Hat Enterprise Linux (RHEL)](rhel.md)  |       ✅        |
| [Fedora](fedora.md)                         |       ✅        |

Um pacote experimental está disponível para distribuições baseadas em
[Arch](archlinux.md).
O Docker não testou nem verificou a instalação.

O Docker oferece suporte ao Docker Desktop nas versões LTS atuais e anteriores
das distribuições mencionadas, bem como na versão mais recente.

## Requisitos gerais do sistema

Para instalar o Docker Desktop com sucesso, seu host Linux deve atender aos
seguintes requisitos gerais:

- Kernel de 64 bits e suporte da CPU para virtualização.
- Suporte à virtualização KVM.
  Siga as
  [instruções de suporte à virtualização KVM](#kvm-virtualization-support) para
  verificar se os módulos do kernel KVM estão habilitados e como conceder acesso
  ao dispositivo KVM.
- O QEMU deve estar na versão 5.2 ou superior.
  Recomendamos atualizar para a versão mais recente.
- Sistema de inicialização `systemd`.
- Ambientes de desktop GNOME, KDE ou MATE são suportados, mas outros podem
  funcionar.
  - Em muitas distribuições Linux, o ambiente GNOME não oferece suporte nativo a
    ícones na bandeja do sistema.
    Para adicionar suporte a esses ícones, é necessário instalar uma extensão do
    GNOME.
    Por exemplo,
    [AppIndicator](https://extensions.gnome.org/extension/615/appindicator-support/).
- Pelo menos 4 GB de RAM.
- Habilitar a configuração de mapeamento de ID em namespaces de usuário;
  consulte
  [Compartilhamento de arquivos](/manuals/desktop/troubleshoot-and-support/faqs/linuxfaqs.md#how-do-i-enable-file-sharing).
  Observe que, para o Docker Desktop versão 4.35 e posteriores, isso não é mais
  necessário.
- Recomendado:
  [Inicializar o `pass`](/manuals/desktop/setup/sign-in.md#credentials-management-for-linux-users)
  para o gerenciamento de credenciais.

O Docker Desktop para Linux executa uma Máquina Virtual (VM).
Para mais informações sobre o motivo, consulte
[Por que o Docker Desktop para Linux executa uma VM](/manuals/desktop/troubleshoot-and-support/faqs/linuxfaqs.md#why-does-docker-desktop-for-linux-run-a-vm).

> [!NOTE]
>
> O Docker não oferece suporte à execução do Docker Desktop para Linux em
> cenários de virtualização aninhada.
> Recomendamos executar o Docker Desktop para Linux nativamente em distribuições
> suportadas.

### Suporte à virtualização KVM

O Docker Desktop executa uma VM que requer
[suporte ao KVM](https://www.linux-kvm.org).

O módulo `kvm` deve ser carregado automaticamente se o host possuir suporte para
virtualização.
Para carregar o módulo manualmente, execute:

```console
$ modprobe kvm
```

Dependendo do processador da máquina host, o módulo correspondente deve ser
carregado:

```console
$ modprobe kvm_intel  # Processadores Intel

$ modprobe kvm_amd    # Processadores AMD
```

Se os comandos acima falharem, você pode visualizar os diagnósticos executando:

```console
$ kvm-ok
```

Para verificar se os módulos KVM estão habilitados, execute:

```console
$ lsmod | grep kvm
kvm_amd               167936  0
ccp                   126976  1 kvm_amd
kvm                  1089536  1 kvm_amd
irqbypass              16384  1 kvm
```

#### Configure permissões de usuário para o dispositivo KVM

Para verificar a propriedade de `/dev/kvm`, execute:

```console
$ ls -al /dev/kvm
```

Adicione seu usuário ao grupo `kvm` para acessar o dispositivo kvm:

```console
$ sudo usermod -aG kvm $USER
```

Faça logout e login novamente para que sua associação ao grupo seja reavaliada.

## Usando SDKs do Docker com o Docker Desktop

O Docker Desktop para Linux usa um socket específico do usuário em vez do socket
de todo o sistema `/var/run/docker.sock`.
SDKs e ferramentas do Docker que se conectam diretamente ao daemon do Docker
precisam que a variável de ambiente `DOCKER_HOST` esteja definida para se
conectarem ao Docker Desktop.
Para detalhes sobre a configuração, consulte
[Como usar SDKs do Docker com o Docker Desktop para Linux?](/manuals/desktop/troubleshoot-and-support/faqs/linuxfaqs.md#how-do-i-use-docker-sdks-with-docker-desktop-for-linux).

## Próximos passos

- Instale o Docker Desktop para Linux na sua distribuição Linux específica:
  - [Instale no Ubuntu](ubuntu.md)
  - [Instale no Debian](debian.md)
  - [Instale no Red Hat Enterprise Linux (RHEL)](rhel.md)
  - [Instale no Fedora](fedora.md)
  - [Instale no Arch](archlinux.md)
