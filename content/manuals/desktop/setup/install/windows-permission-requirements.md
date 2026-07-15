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

source_url: https://github.com/docker/docs/blob/main/content/manuals/desktop/setup/install/windows-permission-requirements.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

description: >-
  Entenda os requisitos de permissão para o Docker Desktop no Windows.
keywords: docker desktop, windows, segurança, instalação
title: Entenda os requisitos de permissão para o Windows
linkTitle: Requisitos de permissão do Windows
weight: 40
---

Esta página contém informações sobre os requisitos de permissão para executar e
instalar o Docker Desktop no Windows, a funcionalidade do processo auxiliar
privilegiado `com.docker.service` e a justificativa para essa abordagem.

Também esclarece a execução de contêineres como `root` em vez de ter acesso de
`Administrador` no host e os privilégios da Docker Engine e dos contêineres do
Windows.

O Docker Desktop no Windows foi projetado com foco em segurança.
Direitos administrativos são necessários apenas quando absolutamente necessário.

## Requisitos de permissão

As permissões necessárias para instalar e executar o Docker Desktop dependem do
[modo de instalação](/manuals/desktop/setup/install/windows-install.md#modos-de-instalação)
usado.

### Instalação por usuário (Beta)

No modo por usuário, o Docker Desktop é instalado em
`%LOCALAPPDATA%\Programs\DockerDesktop` e grava apenas nas chaves de registro do
usuário atual (`HKCU`).
Isso significa:

- Não são necessários privilégios de administrador para instalar ou atualizar o
  Docker Desktop.
- Após a instalação, o Docker Desktop pode ser executado sem privilégios de
  administrador.
- Algumas configurações marcadas como **Requires password** em **Settings**
  ainda exigem elevação de privilégios.
  Ao alterar uma dessas configurações e selecionar **Apply**, o Docker Desktop
  abrirá uma solicitação de UAC para acesso de administrador.

A instalação por usuário não instala automaticamente o serviço auxiliar
privilegiado `com.docker.service`.
Como resultado, recursos que dependem dele, como o backend Hyper-V e os
contêineres do Windows, não estão disponíveis.
Para a maioria dos usuários, isso não representa uma limitação, pois o backend
WSL 2 atende à maioria dos casos de uso.

### Instalação para todos os usuários

No modo de instalação para todos os usuários, o Docker Desktop é instalado em
`C:\Program Files\Docker\Docker` e grava nas chaves do registro do computador
local (`HKLM`).
Ambos os locais exigem privilégios de administrador para serem modificados,
portanto:

- Privilégios de administrador são necessários para instalar e atualizar o
  Docker Desktop.
- Durante a instalação, você receberá uma solicitação do UAC que permite a
  instalação do serviço auxiliar privilegiado `com.docker.service`.
- Após a instalação, o Docker Desktop pode ser executado sem privilégios de
  administrador.

Executar o Docker Desktop sem o serviço auxiliar privilegiado não exige que os
usuários sejam membros do grupo `docker-users`.
No entanto, alguns recursos que exigem operações privilegiadas terão esse
requisito.

Se você realizou a instalação, será adicionado automaticamente ao grupo
`docker-users`, mas outros usuários deverão ser adicionados manualmente.
Isso permite que a pessoa administradora controle quem tem acesso a recursos que
exigem privilégios mais elevados, como criar e gerenciar a VM do Hyper-V ou usar
contêineres do Windows.

Quando o Docker Desktop é iniciado, todos os pipes nomeados não privilegiados
são criados para que apenas os seguintes usuários possam acessá-los:
- O usuário que iniciou o Docker Desktop.
- Membros do grupo local `Administradores`.
- A conta `LOCALSYSTEM`.

### Operações que sempre exigem privilégios elevados

As seguintes operações exigem privilégios de administrador, independentemente do
modo de instalação.

- Habilitar o WSL 2 pela primeira vez: O WSL 2 deve ser habilitado na máquina
  antes que o Docker Desktop possa ser executado.
  Esta é uma operação única, realizada por máquina.
  Depois que o WSL 2 for habilitado, não será necessário habilitá-lo novamente
  para instalações ou atualizações subsequentes do Docker Desktop.
- Configurações marcadas como **Requires password**: Certas configurações do
  Docker Desktop afetam a configuração em nível de sistema e exigem credenciais
  de administrador para serem aplicadas.
  Elas são claramente marcadas como **Requires password**.
  Ao alterar uma dessas configurações e selecionar **Apply**, o Docker Desktop
  solicitará as credenciais de administrador.

## Auxiliar privilegiado

O Docker Desktop precisa executar um conjunto limitado de operações
privilegiadas, realizadas pelo processo auxiliar privilegiado
`com.docker.service`.
Essa abordagem permite, seguindo o princípio do menor privilégio, que o acesso
de `Administrador` seja usado apenas para as operações absolutamente
necessárias, mantendo a possibilidade de usar o Docker Desktop como um usuário
sem privilégios.

> [!NOTE]
>
> `com.docker.service` é instalado apenas no modo de instalação para todos os
> usuários.
> Ele não é usado na instalação por usuário, que, em vez disso, depende
> exclusivamente do backend WSL 2 e não oferece suporte a contêineres Hyper-V ou
> Windows.

O auxiliar privilegiado `com.docker.service` é um serviço do Windows executado
em segundo plano com privilégios de `SYSTEM`.
Ele escuta no pipe nomeado `//./pipe/dockerBackendV2`.
A pessoa desenvolvedora executa a aplicação Docker Desktop, que se conecta ao
pipe nomeado e envia comandos para o serviço.
Este pipe nomeado é protegido e somente usuários que fazem parte do grupo
`docker-users` podem acessá-lo.

O serviço executa as seguintes funcionalidades:
- Garantir que `kubernetes.docker.internal` esteja definido no arquivo hosts do
  Win32.
  A definição do nome DNS `kubernetes.docker.internal` permite que o Docker
  compartilhe contextos do Kubernetes com contêineres.
- Garantir que `host.docker.internal` e `gateway.docker.internal` estejam
  definidos no arquivo hosts do Win32.
  Eles apontam para o endereço IP local do host e permitem que uma aplicação
  resolva o IP do host usando o mesmo nome, seja a partir do próprio host ou de
  um contêiner.
- Armazenar em cache, de forma segura, a política de Gerenciamento de Acesso ao
  Registro, que é somente leitura para a pessoa desenvolvedora.
- Criar a VM do Hyper-V "DockerDesktopVM" e gerenciar seu ciclo de vida —
  iniciar, parar e destruí-la.
  O nome da VM está codificado no código do serviço, portanto, o serviço não
  pode ser usado para criar ou manipular outras VMs.
- Mover o arquivo ou pasta VHDX.
- Iniciar e parar a Docker Engine do Windows e verificar se ela está em
  execução.
- Excluir todos os arquivos de dados dos contêineres do Windows.
- Verificar se o Hyper-V está habilitado.
- Verificar se o carregador de inicialização ativa o Hyper-V.
- Verificar se os recursos necessários do Windows estão instalados e
  habilitados.
- Realizar verificações de integridade e recuperar a versão do próprio serviço.

O modo de inicialização do serviço depende da engine de contêiner selecionada e,
no caso do WSL, da necessidade de manter `host.docker.internal` e
`gateway.docker.internal` no arquivo hosts do Win32.
Isso é controlado por uma configuração em `Use the WSL 2 based engine` na página
de configurações.
Quando essa opção está habilitada, o mecanismo do WSL se comporta da mesma forma
que o Hyper-V.
Portanto:
- Com contêineres do Windows ou contêineres Linux do Hyper-V, o serviço é
  iniciado quando o sistema é inicializado e permanece em execução o tempo todo,
  mesmo quando o Docker Desktop não está em execução.
  Isso é necessário para que você possa executar o Docker Desktop sem
  privilégios de administrador.
- Com contêineres Linux do WSL2, o serviço não é necessário e, portanto, não é
  executado automaticamente quando o sistema é inicializado.
  Ao alternar para contêineres do Windows ou contêineres Linux do Hyper-V, ou ao
  optar por manter `host.docker.internal` e `gateway.docker.internal` no arquivo
  hosts do Win32, uma solicitação do UAC será exibida, pedindo que você aceite a
  operação privilegiada para iniciar o serviço.
  Se aceito, o serviço é iniciado e configurado para iniciar automaticamente na
  próxima inicialização do Windows.

## Contêineres executados como root dentro da VM Linux

O daemon Docker Linux e os contêineres são executados em uma VM Linux mínima e
de propósito específico gerenciada pelo Docker.
Ela é imutável, portanto, você não pode estendê-la ou alterar o software
instalado.
Isso significa que, embora os contêineres sejam executados por padrão como
`root`, isso não permite alterar a VM e não concede acesso de `Administrador`
à máquina host Windows.
A VM Linux serve como um limite de segurança e limita quais recursos do host
podem ser acessados.
O compartilhamento de arquivos usa um servidor de arquivos criado no espaço do
usuário e quaisquer diretórios do host montados em contêineres Docker ainda
mantêm suas permissões originais.
Os contêineres não têm acesso a nenhum arquivo do host além daqueles
explicitamente compartilhados.

## Isolamento Aprimorado de Contêiner

Além disso, o Docker Desktop oferece suporte ao modo de
[Isolamento Aprimorado de Contêiner](/manuals/enterprise/security/hardened-desktop/enhanced-container-isolation/_index.md)
(Enhanced Container Isolation, ou ECI), disponível apenas para clientes
Business, que aumenta ainda mais a segurança dos contêineres sem impactar os
fluxos de trabalho das pessoas desenvolvedoras.

O ECI executa automaticamente todos os contêineres em um namespace de usuário
Linux, de forma que o usuário root do contêiner seja mapeado para um usuário sem
privilégios dentro da VM do Docker Desktop.
O ECI usa essa e outras técnicas avançadas para proteger ainda mais os
contêineres dentro da VM Linux do Docker Desktop, de forma que eles fiquem ainda
mais isolados do daemon do Docker e de outros serviços em execução dentro da VM.

## Contêineres do Windows

> [!WARNING]
>
> Habilitar contêineres do Windows tem implicações de segurança importantes.

> [!NOTE]
>
> Os contêineres do Windows são suportados apenas no modo de instalação para
> todos os usuários.
> Eles não estão disponíveis quando o Docker Desktop é instalado por usuário.
> Consulte
> [Modos de instalação](/manuals/desktop/setup/install/windows-install.md#modos-de-instalação).

Ao contrário da Docker Engine para Linux e dos contêineres executados em uma
máquina virtual, os contêineres do Windows são implementados usando recursos do
sistema operacional e executados diretamente no host Windows.
Se você habilitar os contêineres do Windows durante a instalação, o usuário
`ContainerAdministrator` usado para administração dentro do contêiner será um
administrador local na máquina host.
Habilitar os contêineres do Windows durante a instalação permite que os membros
do grupo `docker-users` sejam elevados a administradores no host.
Para organizações que não desejam que suas pessoas desenvolvedoras executem
contêineres do Windows, um parâmetro de instalação `--no-windows-containers`
está disponível para desabilitar seu uso.

## Rede

Para conectividade de rede, o Docker Desktop usa um processo em espaço de
usuário (`vpnkit`), que herda restrições como regras de firewall, VPN,
propriedades de proxy HTTP, etc., do usuário que o iniciou.
