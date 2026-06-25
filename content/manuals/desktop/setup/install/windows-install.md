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

source_url: https://github.com/docker/docs/blob/main/content/manuals/desktop/setup/install/windows-install.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

description: >-
  Comece a usar o Docker para Windows.
  Este guia aborda os requisitos do sistema, onde baixar e instruções sobre como
  instalar e atualizar.
keywords: >-
  docker para windows, docker windows, docker desktop para windows, docker no
  windows, instalar docker windows, instalar docker no windows, docker windows
  10, executar docker no windows, instalando docker para windows, contêineres do
  windows, wsl, hyper-v
title: Instale o Docker Desktop no Windows
linkTitle: Windows
weight: 30
aliases:
  - /desktop/windows/install/
  - /docker-for-windows/install/
  - /engine/installation/windows/
  - /engine/installation/windows/docker-ee/
  - /desktop/win/configuring-wsl/
  - /desktop/install/windows-install/
---

> **Termos do Docker Desktop**
>
> O uso comercial do Docker Desktop em empresas maiores (mais de 250 pessoas
> funcionárias OU mais de US$ 10 milhões em receita anual) requer uma
> [assinatura paga](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopWindowsInstall).

Esta página fornece links para download, requisitos de sistema e instruções de
instalação passo a passo para o Docker Desktop no Windows.

{{< button text="Docker Desktop para Windows - x86_64" url="https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-win-amd64" >}}
{{< button text="Docker Desktop para Windows - x86_64 na Microsoft Store" url="https://apps.microsoft.com/detail/xp8cbj40xlbwkx?hl=en-GB&gl=GB" >}}
{{< button text="Docker Desktop para Windows - Arm (Acesso Antecipado)" url="https://desktop.docker.com/win/main/arm64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-win-arm64" >}}

_Para checksums, consulte as [Notas de lançamento](/manuals/desktop/release-notes.md)_

## Modos de instalação

O Docker Desktop oferece dois modos de instalação.
A instalação por usuário (Beta) é recomendada para a maioria das pessoas
usuárias.
Ela não requer privilégios de administrador para instalar ou atualizar, e o
backend WSL 2 usado atende às necessidades da grande maioria das pessoas
usuárias do Docker Desktop.

| | Por usuário (recomendado) | Todos os usuários |
|---|---|---|
| Local de instalação | `%LOCALAPPDATA%\Programs\DockerDesktop` | `C:\Program Files\Docker\Docker` |
| Chaves de registro | Usuário atual (HKCU) | Máquina local (HKLM) |
| Direitos de administrador para instalar | Não necessário | Necessário |
| Direitos de administrador para atualizar | Não necessário | Necessário |
| Backend de contêineres Linux | Somente WSL 2 | WSL 2 ou Hyper-V |
| Contêineres do Windows | Não suportado | Suportado |
| Segurança | Superfície de ataque menor; nenhum serviço de sistema privilegiado instalado | Requer serviço de sistema privilegiado; acesso mais amplo aos recursos do host |

Para obter mais informações, consulte [Entenda os requisitos de permissão para o Windows](windows-permission-requirements.md).

## System requirements

> [!TIP]
>
> **Devo usar Hyper-V ou WSL?**
>
> A funcionalidade do Docker Desktop permanece consistente tanto no WSL quanto
> no Hyper-V, sem preferência por uma arquitetura em detrimento da outra.
> Hyper-V e WSL têm suas próprias vantagens e desvantagens, dependendo da sua
> configuração específica e do seu caso de uso planejado.
> Observe que o Hyper-V está disponível apenas com a instalação para todos os
> usuários.
> Se você instalar o Docker Desktop no modo por usuário, o WSL 2 é o único
> backend compatível.

{{< tabs >}}
{{< tab name="Backend WSL 2, x86_64" >}}

* WSL versão 2.1.5 ou posterior.
  Para verificar sua versão, consulte
  [WSL: Verificação e instalação](#wsl-verificação-e-instalação).
* Se você pretende usar o Isolamento de Contêiner Aprimorado (Enhanced Container
  Isolation, ou ECI), certifique-se de estar usando o WSL versão 2.6 ou
  posterior.
  Isso é necessário porque o ECI depende de uma versão do kernel Linux de pelo
  menos 6.3.0, e o WSL 2.6+ inclui a versão 6.6 do kernel Linux.
* Windows 10 64 bits: Enterprise, Pro ou Education versão 22H2 (compilação
  19045).
* Windows 11 64 bits: Enterprise, Pro ou Education versão 23H2 (compilação
  \22631) ou superior.
* O serviço do Windows Server (LanmanServer) deve estar habilitado e seu modo de
  inicialização definido como **Automático**.
* Ative o recurso WSL 2 no Windows.
  Para obter instruções detalhadas, consulte a
  [documentação da Microsoft](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
* Os seguintes pré-requisitos de hardware são necessários para executar o WSL 2
  com sucesso no Windows 10 ou Windows 11:
  * Processador de 64 bits com
    [Second Level Address Translation (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation).
  * 8 GB de RAM do sistema.
  * Ative a virtualização de hardware na BIOS/UEFI.
    Para obter mais informações, consulte
    [Virtualização](/manuals/desktop/troubleshoot-and-support/troubleshoot/topics.md#docker-desktop-fails-due-to-virtualization-not-working).

Para obter mais informações sobre como configurar o WSL 2 com o Docker Desktop,
consulte [WSL](/manuals/desktop/features/wsl/_index.md).

> [!NOTE]
>
> O Docker só oferece suporte ao Docker Desktop no Windows para as versões do
> Windows que ainda estão dentro do
> [ciclo de manutenção da Microsoft](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet).
> O Docker Desktop não é compatível com versões de servidor do Windows, como o
> Windows Server 2019 ou o Windows Server 2022.
> Para obter mais informações sobre como executar contêineres no Windows Server,
> consulte a
> [documentação oficial da Microsoft](https://learn.microsoft.com/virtualization/windowscontainers/quick-start/set-up-environment).

> [!IMPORTANT]
>
> Para executar [contêineres do Windows](#contêineres-do-windows), você precisa do
> Windows 10 ou do Windows 11 Professional ou Enterprise.
> As edições Home ou Education do Windows permitem apenas a execução de
> contêineres Linux.

{{< /tab >}}
{{< tab name="Backend Hyper-V, x86_64" >}}

* Windows 10 64 bits: Enterprise, Pro ou Education versão 22H2 (compilação
  19045).
* Windows 11 64 bits: Enterprise, Pro ou Education versão 23H2 (compilação
  \22631) ou superior.
* O serviço do Windows Server (LanmanServer) deve estar habilitado e seu modo de
  inicialização definido como **Automático**.
* Ative os recursos Hyper-V e contêineres do Windows.
* Os seguintes pré-requisitos de hardware são necessários para executar o
  Cliente Hyper-V com sucesso no Windows 10:
  * Processador de 64 bits com
    [Second Level Address Translation (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation).
  * 8 GB de RAM do sistema.
  * Ative o suporte à virtualização de hardware em nível de BIOS/UEFI nas
    configurações da BIOS/UEFI.
    Para obter mais informações, consulte
    [Virtualização](/manuals/desktop/troubleshoot-and-support/troubleshoot/topics.md#virtualization).

> [!NOTE]
>
> O Docker só oferece suporte ao Docker Desktop no Windows para as versões do
> Windows que ainda estão dentro do
> [ciclo de manutenção da Microsoft](https://support.microsoft.com/en-us/help/13853/windows-lifecycle-fact-sheet).
> O Docker Desktop não é compatível com versões de servidor do Windows, como o
> Windows Server 2019 ou o Windows Server 2022.
> Para obter mais informações sobre como executar contêineres no Windows Server,
> consulte a
> [documentação oficial da Microsoft](https://learn.microsoft.com/virtualization/windowscontainers/quick-start/set-up-environment).

> [!IMPORTANT]
>
> Para executar [contêineres do Windows](#contêineres-do-windows), você precisa do
> Windows 10 ou do Windows 11 Professional ou Enterprise.
> As edições Windows Home ou Education permitem apenas a execução de contêineres
> Linux.

{{< /tab >}}
{{< tab name="Backend WSL 2, Arm (Acesso Antecipado)" >}}

* WSL versão 2.1.5 ou posterior.
  Para verificar sua versão, consulte
  [WSL: Verificação e instalação](#wsl-verificação-e-instalação).
* Windows 10 64 bits: Enterprise, Pro ou Education versão 22H2 (compilação
  19045).
* Windows 11 64 bits: Enterprise, Pro ou Education versão 23H2 (compilação
  \22631) ou superior.
* O serviço do Windows Server (LanmanServer) deve estar habilitado e seu modo de
  inicialização definido como **Automático**.
* Ative o recurso WSL 2 no Windows.
  Para obter instruções detalhadas, consulte a
  [Documentação da Microsoft](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
* Os seguintes pré-requisitos de hardware são necessários para executar o WSL 2
  com sucesso no Windows 10 ou Windows 11:
  * Processador de 64 bits com
    [Second Level Address Translation (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation).
  * 8 GB de RAM do sistema.
  * Habilite a virtualização de hardware na BIOS/UEFI.
    Para obter mais informações, consulte
    [Virtualização](/manuals/desktop/troubleshoot-and-support/troubleshoot/topics.md#virtualization).

> [!IMPORTANT]
>
> Contêineres do Windows não são suportados.

{{< /tab >}}
{{< /tabs >}}

Contêineres e imagens criados com o Docker Desktop são compartilhados entre
todas as contas de usuário nas máquinas onde ele está instalado.
Isso ocorre porque todas as contas do Windows usam a mesma VM para criar e
executar contêineres.
Observe que não é possível compartilhar contêineres e imagens entre contas de
usuário ao usar o backend WSL 2 do Docker Desktop.

A execução do Docker Desktop em uma VM VMware ESXi ou Azure é compatível com
clientes do Docker Business.
Isso requer a ativação da virtualização aninhada no hipervisor.
Para obter mais informações, consulte
[Executando o Docker Desktop em um ambiente de VM ou VDI](/manuals/desktop/setup/vm-vdi.md).

## Instale o Docker Desktop no Windows

### Instale interativamente

1. Faça o download do instalador usando o botão de download na parte superior da
   página ou nas [Notas de lançamento](/manuals/desktop/release-notes.md).

2. Clique duas vezes em `Docker Desktop Installer.exe` para executar o
   instalador.
   O instalador perguntará qual modo de instalação você prefere.
   A opção "per-user" instala em `%LOCALAPPDATA%\Programs\DockerDesktop` e não
   requer privilégios de administrador.
   A opção "all users" solicitará a elevação de privilégios.

   > [!NOTE]
   >
   > Se você quiser alterar o modo de instalação posteriormente, será necessário
   > desinstalar e reinstalar o Docker Desktop.

3. Quando for solicitado, certifique-se de que a opção **Use WSL 2 instead of
   Hyper-V** na página Configuration esteja selecionada ou não, dependendo da
   sua escolha de backend.

   Em sistemas que suportam apenas um backend, o Docker Desktop seleciona
   automaticamente a opção disponível.

4. Siga as instruções do assistente de instalação para autorizar o instalador e
   prosseguir com a instalação.

5. Quando a instalação for concluída com sucesso, selecione **Close** para
   finalizar o processo.

6. [Inicie o Docker Desktop](#inicie-o-docker-desktop).

### Instale pela linha de comando

Após baixar o arquivo `Docker Desktop Installer.exe`, execute o seguinte comando
em um terminal para instalar o Docker Desktop em
`%LOCALAPPDATA%\Programs\DockerDesktop`.

Para instalação por usuário, execute:

```console
$ "Docker Desktop Installer.exe" install --user
```

Para instalar para todos os usuários da máquina (requer privilégios de
administrador):

```console
$ "Docker Desktop Installer.exe" install
```

Se você estiver usando o PowerShell, deverá executá-lo da seguinte forma:

```powershell
# Instalação por usuário (sem necessidade de privilégios de administrador)
Start-Process 'Docker Desktop Installer.exe' -Wait -ArgumentList 'install', '--user'

# Instalação para todos os usuários (executar como administrador)
Start-Process 'Docker Desktop Installer.exe' -Wait install
```

Se estiver usando o Prompt de Comando do Windows:

```sh
# Instalação por usuário (sem necessidade de privilégios de administrador)
start /w "" "Docker Desktop Installer.exe" install --user

# Instalação para todos os usuários (executar como administrador)
start /w "" "Docker Desktop Installer.exe" install
```

Se estiver usando a instalação para todos os usuários e sua conta de
administrador for diferente da sua conta de usuário, você deve adicionar o
usuário ao grupo **docker-users** para acessar recursos que exigem privilégios
mais elevados, como criar e gerenciar a VM do Hyper-V ou usar contêineres do
Windows:

```console
$ net localgroup docker-users <user> /add
```

Consulte a seção [Flags do instalador](#flags-do-instalador) para ver quais flags o
comando `install` aceita.

> [!NOTE]
>
> Se você quiser alterar o modo de instalação posteriormente, será necessário
> desinstalar e reinstalar o Docker Desktop.

## Inicie o Docker Desktop

O Docker Desktop não inicia automaticamente após a instalação.
Para iniciar o Docker Desktop:

1. Pesquise por Docker e selecione **Docker Desktop** nos resultados da
   pesquisa.

2. O menu do Docker
   ({{< inline-image src="images/whale-x.svg" alt="menu da baleia" >}}) exibe o
   Contrato de Serviço de Assinatura do Docker.

   {{% include "desktop-license-update.md" %}}

3. Selecione **Accept** para continuar.
   O Docker Desktop será iniciado após a aceitação dos termos.

   Observe que o Docker Desktop não será executado se você não concordar com os
   termos.
   Você pode optar por aceitar os termos posteriormente, abrindo o Docker
   Desktop.

   Para obter mais informações, consulte o
   [Contrato de Serviço de Assinatura do Docker Desktop](https://www.docker.com/legal/docker-subscription-service-agreement/).
   Recomenda-se a leitura das
   [Perguntas Frequentes](https://www.docker.com/pricing/faq).

> [!TIP]
>
> Como pessoa administradora de TI, você pode usar um software de gerenciamento
> de dispositivos móveis (MDM) para identificar o número de instâncias do Docker
> Desktop e suas versões em seu ambiente.
> Isso pode fornecer relatórios de licença precisos, ajudar a garantir que suas
> máquinas usem a versão mais recente do Docker Desktop e permitir que você
> [imponha o login](/manuals/enterprise/security/enforce-sign-in/_index.md).
> - [Intune](https://learn.microsoft.com/en-us/mem/intune/apps/app-discovered-apps)
> - [Jamf](https://docs.jamf.com/10.25.0/jamf-pro/administrator-guide/Application_Usage.html)
> - [Kandji](https://support.kandji.io/support/solutions/articles/72000559793-view-a-device-application-list)
> - [Kolide](https://www.kolide.com/features/device-inventory/properties/mac-apps)
> - [Workspace One](https://blogs.vmware.com/euc/2022/11/how-to-use-workspace-one-intelligence-to-manage-app-licenses-and-reduce-costs.html)

## Opções avançadas de configuração e instalação do sistema

### WSL: Verificação e instalação

Se você optou por usar o WSL, primeiro verifique se a versão instalada atende
aos requisitos do sistema executando o seguinte comando no terminal:

```console
wsl --version
```

Se os detalhes da versão não aparecerem, provavelmente você está usando a versão
padrão do WSL.
Essa versão não oferece suporte a recursos modernos e precisa ser atualizada.

Você pode atualizar ou instalar o WSL usando um dos seguintes métodos:

#### Opção 1: Instale ou atualize o WSL pelo terminal

1. Abra o PowerShell ou o Prompt de Comando do Windows no modo administrador.

2. Execute o comando de instalação ou atualização.
   A reinicialização do computador poderá ser solicitada.
   Para obter mais informações, consulte
   [Instale o WSL](https://learn.microsoft.com/en-us/windows/wsl/install).

```console
wsl --install

wsl --update
```

#### Opção 2: Instale o WSL via pacote MSI

Se o acesso à Microsoft Store estiver bloqueado devido a políticas de segurança:

1. Acesse a página oficial de
   [Releases do WSL no GitHub](https://github.com/microsoft/WSL/releases).
2. Baixe o instalador `.msi` da versão estável mais recente (no menu suspenso
   Assets).
3. Execute o instalador baixado e siga as instruções de instalação.

### Flags do instalador

> [!NOTE]
>
> Se você estiver usando o PowerShell, precisará usar o parâmetro `ArgumentList`
> antes de quaisquer flags.
> Por exemplo:
>
> ```powershell
> Start-Process 'Docker Desktop Installer.exe' -Wait -ArgumentList 'install', '--accept-license'
> ```

#### Comportamento da instalação

* `--user`: Instala o Docker Desktop no modo por usuário, em
  `%LOCALAPPDATA%\Programs\DockerDesktop`.
  Não são necessários privilégios de administrador.
  Este é o modo recomendado para a maioria das pessoas usuárias.
  Consulte [Modos de instalação](#modos-de-instalação).
* `--quiet`: Suprime a saída de informações ao executar o instalador.
* `--accept-license`: Aceita o
  [Contrato de Serviço de Assinatura do Docker](https://www.docker.com/legal/docker-subscription-service-agreement)
  agora, em vez de exigir que seja aceito na primeira execução da aplicação.
* `--installation-dir=<caminho>`: Altera o local de instalação padrão
  (`C:\Program Files\Docker\Docker`).
* `--backend=<nome do backend>`: Seleciona o backend padrão a ser usado para o
  Docker Desktop: `hyper-v`, `windows` ou `wsl-2` (padrão).
* `--always-run-service`: Após a conclusão da instalação, inicia o serviço
  `com.docker.service` e define o tipo de inicialização do serviço como
  Automático.
  Isso evita a necessidade de privilégios de administrador, necessários para
  iniciar o serviço `com.docker.service`.
  O serviço `com.docker.service` é necessário para contêineres Windows e backend
  Hyper-V.

#### Segurança e controle de acesso

* `--allowed-org=<nome da organização>`: Exige que a pessoa usuária faça login e
  pertença à organização Docker Hub especificada ao executar a aplicação.
* `--admin-settings`: Cria automaticamente um arquivo `admin-settings.json` que
  é usado por pessoas administradoras para controlar determinadas configurações
  do Docker Desktop em máquinas cliente dentro de sua organização.
  Para obter mais informações, consulte
  [Gerenciamento de configurações](/manuals/enterprise/security/hardened-desktop/settings-management/_index.md).
  * Deve ser usado em conjunto com a flag `--allowed-org=<nome da organização>`.
  * Por exemplo:
    `--allowed-org=<nome da organização> --admin-settings="{'configurationFileVersion': 2, 'enhancedContainerIsolation': {'value': true, 'locked': false}}"`.
* `--no-windows-containers`: Desativa a integração com contêineres do Windows.
  Isso pode melhorar a segurança.
  Para obter mais informações, consulte
  [Contêineres do Windows](/manuals/desktop/setup/install/windows-permission-requirements.md#contêineres-do-windows).

#### Configurações de proxy

* `--proxy-http-mode=<modo>`: Define o modo do proxy HTTP, `system` (padrão) ou
  `manual`.
* `--override-proxy-http=<URL>`: Define a URL do proxy HTTP que deve ser usada
  para requisições HTTP de saída.
  Requer que `--proxy-http-mode` seja `manual`.
* `--override-proxy-https=<URL>`: Define a URL do proxy HTTP que deve ser usada
  para requisições HTTPS de saída.
  Requer que `--proxy-http-mode` seja `manual`.
* `--override-proxy-exclude=<hosts/domínios>`: Ignora as configurações de proxy
  para os hosts e domínios especificados.
  Usa uma lista separada por vírgulas.
* `--proxy-enable-kerberosntlm`: Habilita a autenticação de proxy Kerberos e
  NTLM.
  Se você estiver habilitando isso, certifique-se de que seu servidor proxy
  esteja configurado corretamente para autenticação Kerberos/NTLM.
  Disponível no Docker Desktop 4.32 e versões posteriores.
* `--override-proxy-pac=<URL do arquivo PAC>`: Define a URL do arquivo PAC.
  Essa configuração só entra em vigor quando o modo proxy `manual` está sendo
  usado.
* `--override-proxy-embedded-pac=<script PAC>`: Especifica um script PAC (Proxy
  Auto-Config) incorporado.
  Essa configuração só entra em vigor quando o modo proxy `manual` está sendo
  usado e tem precedência sobre a flag `--override-proxy-pac`.

##### Exemplo de especificação do arquivo PAC

```console
"Docker Desktop Installer.exe" install --proxy-http-mode="manual" --override-proxy-pac="http://localhost:8080/myproxy.pac"
```

##### Exemplo de especificação do script PAC

```console
"Docker Desktop Installer.exe" install --proxy-http-mode="manual" --override-proxy-embedded-pac="function FindProxyForURL(url, host) { return \"DIRECT\"; }"
```

#### Diretório raiz de dados e localização do disco

* `--hyper-v-default-data-root=<caminho>`: Especifica o local padrão para o
  disco da VM Hyper-V.
* `--windows-containers-default-data-root=<caminho>`: Especifica o local padrão
  para os contêineres do Windows.
* `--wsl-default-data-root=<caminho>`: Especifica o local padrão para o disco de
  distribuição do WSL.

### Privilégios de administrador

No modo por usuário, o Docker Desktop pode ser instalado e atualizado sem
privilégios de administrador.
Algumas configurações ainda exigem elevação de privilégios e são marcadas como
**Requires password** na interface Settings.
Habilitar o WSL 2 pela primeira vez também requer privilégios de administrador,
mas essa é uma operação única, por máquina.

No modo para todos os usuários, a instalação do Docker Desktop requer
privilégios de administrador.
No entanto, uma vez instalado, ele pode ser usado sem acesso administrativo.
Algumas ações, porém, ainda precisam de permissões elevadas.
Consulte
[Entenda os requisitos de permissão para Windows](./windows-permission-requirements.md)
para obter mais detalhes.

Consulte as
[Perguntas frequentes](/manuals/desktop/troubleshoot-and-support/faqs/general.md#how-do-i-run-docker-desktop-without-administrator-privileges)
sobre como instalar e executar o Docker Desktop sem precisar de privilégios de
administrador.

Se você for uma pessoa administradora de TI e suas pessoas usuárias não tiverem
direitos de administrador e planejarem executar operações que exigem privilégios
elevados, certifique-se de instalar o Docker Desktop usando a flag de instalação
`--always-run-service`.
Isso garante que essas ações ainda possam ser executadas sem solicitar a
elevação de privilégios do Controle de Conta de Usuário (UAC).
Consulte [Flags do instalador](#flags-do-instalador) para obter mais detalhes.

### Contêineres do Windows

> [!NOTE]
>
> Os contêineres do Windows são suportados apenas no modo de instalação para
> todos os usuários.
> Eles não estão disponíveis quando o Docker Desktop é instalado por usuário.

No menu do Docker Desktop, você pode alternar o daemon (Linux ou Windows) com o
qual a CLI do Docker se comunica.
Selecione **Switch to Windows containers** para usar contêineres do Windows ou
selecione **Switch to Linux containers** para usar contêineres do Linux (o
padrão).

Para obter mais informações sobre contêineres do Windows, consulte a seguinte
documentação:

* Documentação da Microsoft sobre
  [Contêineres do Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/index).

* [Crie e execute seu primeiro contêiner do Windows Server (Postagem no blog)](https://www.docker.com/blog/build-your-first-docker-windows-server-container/)
  oferece um guia rápido sobre como criar e executar contêineres nativos do
  Docker para Windows nas versões de avaliação do Windows 10 e do Windows Server
  \2016.

* [Introdução aos contêineres do Windows (Laboratório)](https://github.com/docker/labs/blob/master/windows/windows-containers/README.md)
  mostra como usar a aplicação
  [MusicStore](https://github.com/aspnet/MusicStore/) com contêineres do
  Windows.
  O MusicStore é uma aplicação .NET padrão e,
  [foi feito o fork aqui para usar contêineres](https://github.com/friism/MusicStore),
  é um bom exemplo de uma aplicação multicontêineres.

* Para entender como se conectar a contêineres do Windows a partir do host
  local, consulte
  [Quero me conectar a um contêiner do Windows](/manuals/desktop/features/networking.md#i-want-to-connect-to-a-container-from-the-host).

> [!NOTE]
>
> Ao alternar para contêineres do Windows, **Settings** exibe apenas as guias
  ativas e aplicáveis aos seus contêineres do Windows.

Se você configurar proxies ou daemons no modo de contêineres do Windows, essas
configurações se aplicarão somente aos contêineres do Windows.
Se você voltar para contêineres do Linux, os proxies e as configurações de
daemons retornarão ao que você havia definido para contêineres do Linux.
As configurações dos seus contêineres do Windows serão mantidas e ficarão
disponíveis novamente ao retornar.

## Próximos passos

* Explore as
  [assinaturas do Docker](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopWindowsInstall)
  para ver o que o Docker pode oferecer.
* [Primeiros passos com o Docker](/get-started/introduction/_index.md).
* [Explore o Docker Desktop](/manuals/desktop/use-desktop/_index.md) e todos os
  seus recursos.
* [Solução de problemas](/manuals/desktop/troubleshoot-and-support/troubleshoot/_index.md)
  descreve problemas comuns, soluções alternativas e como obter suporte.
* [Perguntas frequentes](/manuals/desktop/troubleshoot-and-support/faqs/general.md)
  fornecem respostas para perguntas frequentes.
* [Notas de lançamento](/manuals/desktop/release-notes.md) lista atualizações de
  componentes, novos recursos e melhorias associadas às versões do Docker
  Desktop.
* [Backup e restauração de dados](/manuals/desktop/settings-and-maintenance/backup-and-restore.md)
  fornece instruções sobre como fazer backup e restaurar dados relacionados ao
  Docker.
