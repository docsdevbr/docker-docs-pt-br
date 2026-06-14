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

source_url: https://github.com/docker/docs/blob/main/content/manuals/desktop/setup/install/mac-install.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

description: >-
  Instale o Docker Desktop para Mac para começar.
  Este guia aborda os requisitos do sistema, onde baixar e instruções sobre como
  instalar e atualizar.
keywords: >-
  docker para mac, instalar docker no macos, docker mac, instalação do docker no
  mac, docker instalar no macos, instalar docker no mac, instalar docker no
  macbook, docker desktop para mac, como instalar docker no mac, configurar
  docker no mac
title: Instale o Docker Desktop no Mac
linkTitle: Mac
weight: 10
aliases:
  - /desktop/mac/install/
  - /docker-for-mac/install/
  - /engine/installation/mac/
  - /desktop/install/mac-install/
  - /desktop/install/mac/
---

> **Termos do Docker Desktop**
>
> O uso comercial do Docker Desktop em empresas maiores (mais de 250 pessoas
> funcionárias OU mais de US$ 10 milhões em receita anual) requer uma
> [assinatura paga](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopMacInstall).

Esta página fornece links para download, requisitos de sistema e instruções de
instalação passo a passo para o Docker Desktop no Mac.

{{< button text="Docker Desktop para Mac com Apple Silicon" url="https://desktop.docker.com/mac/main/arm64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-mac-arm64" >}}
{{< button text="Docker Desktop para Mac com chip Intel" url="https://desktop.docker.com/mac/main/amd64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-mac-amd64" >}}

*Para checksums, consulte as
[Notas de lançamento](/manuals/desktop/release-notes.md).*

## Requisitos de sistema

{{< tabs >}}
{{< tab name="Mac com Apple Silicon" >}}

- Uma versão compatível do macOS.

  > [!IMPORTANT]
  >
  > O Docker Desktop é compatível com a versão atual e as duas versões
  > principais anteriores do macOS.
  > À medida que novas versões principais do macOS são disponibilizadas, o
  > Docker deixa de oferecer suporte à versão mais antiga e passa a oferecer
  > suporte à versão mais recente do macOS (além das duas versões anteriores).

- Pelo menos 4 GB de RAM.

- Para obter a melhor experiência, recomenda-se a instalação do Rosetta 2.
  O Rosetta 2 não é mais estritamente necessário, porém, algumas ferramentas
  opcionais de linha de comando ainda o exigem ao usar o Darwin/AMD64.
  Consulte
  [Problemas conhecidos](/manuals/desktop/troubleshoot-and-support/troubleshoot/known-issues.md).
  Para instalar o Rosetta 2 manualmente pela linha de comando, execute o
  seguinte comando:

   ```console
   $ softwareupdate --install-rosetta
   ```
{{< /tab >}}
{{< tab name="Mac com chip Intel" >}}

- Uma versão compatível do macOS.

  > [!IMPORTANT]
  >
  > O Docker Desktop é compatível com a versão atual e as duas versões
  > principais anteriores do macOS.
  > À medida que novas versões principais do macOS são disponibilizadas, o
  > Docker deixa de oferecer suporte à versão mais antiga e passa a oferecer
  > suporte à versão mais recente do macOS (além das duas versões anteriores).

- Pelo menos 4 GB de RAM.

{{< /tab >}}
{{< /tabs >}}

> **Antes de instalar ou atualizar**
>
> - Feche as ferramentas que possam estar chamando o Docker em segundo plano
>   (Visual Studio Code, terminais, aplicações de agente).
>
> - Se você gerencia frotas ou instala via MDM, use o
>   [**instalador PKG**](/manuals/enterprise/enterprise-deployment/pkg-install-and-configure.md).
>
> - Mantenha o volume do instalador montado até que a instalação seja concluída.
>
> Se você encontrar uma caixa de diálogo "Docker.app is damaged", consulte
> [Corrigir "Docker.app is damaged" no macOS](/manuals/desktop/troubleshoot-and-support/troubleshoot/mac-damaged-dialog.md).

## Instale e execute o Docker Desktop no Mac

> [!TIP]
>
> Consulte as
> [Perguntas frequentes](/manuals/desktop/troubleshoot-and-support/faqs/general.md#how-do-I-run-docker-desktop-without-administrator-privileges)
> para saber como instalar e executar o Docker Desktop sem precisar de
> privilégios de administrador.

### Instale interativamente

1. Baixe o instalador usando os botões de download na parte superior da página
   ou nas [notas de lançamento](/manuals/desktop/release-notes.md).

2. Clique duas vezes em `Docker.dmg` para abrir o instalador e arraste o ícone
   do Docker para a pasta **Aplicativos**.
   Por padrão, o Docker Desktop é instalado em `/Aplicativos/Docker.app`.

3. Clique duas vezes em `Docker.app` na pasta **Aplicativos** para iniciar o
   Docker.

4. O menu do Docker exibe o Contrato de Serviço de Assinatura do Docker.

   Aqui está um resumo dos pontos principais:

   - O Docker Desktop é gratuito para pequenas empresas (menos de 250 pessoas
     funcionárias E menos de US$ 10 milhões em receita anual), uso pessoal,
     educação e projetos de código aberto não comerciais.
   - Caso contrário, é necessária uma assinatura paga para uso profissional.
   - Assinaturas pagas também são necessárias para entidades governamentais.
   - As assinaturas Docker Pro, Team e Business incluem o uso comercial do
     Docker Desktop.

5. Selecione **Accept** para continuar.

   Observe que o Docker Desktop não será executado se você não concordar com os
   termos.
   Você pode optar por aceitar os termos posteriormente, abrindo o Docker
   Desktop.

   Para obter mais informações, consulte o
   [Contrato de Serviço de Assinatura do Docker Desktop](https://www.docker.com/legal/docker-subscription-service-agreement).
   Recomenda-se também a leitura das
   [Perguntas frequentes](https://www.docker.com/pricing/faq).

6. Na janela de instalação, selecione:

   - **Use recommended settings (Requires password)**.
     Isso permite que o Docker Desktop defina automaticamente as configurações
     necessárias.
   - **Use advanced settings**.
     Você poderá definir o local das ferramentas de linha de comando do Docker
     no diretório do sistema ou do usuário, habilitar o socket padrão do Docker
     e habilitar o mapeamento de portas privilegiadas.
     Consulte
     [Configurações](/manuals/desktop/settings-and-maintenance/settings.md#advanced)
     para obter mais informações e saber como definir o local das ferramentas da
     CLI do Docker.

7. Selecione **Finish**.
   Se você aplicou alguma das configurações anteriores que exigem uma senha no
   passo 6, digite sua senha para confirmar sua escolha.

### Instale pela linha de comando

Após baixar o arquivo `Docker.dmg` pelos botões de download no topo da página ou
pelas [notas de lançamento](/manuals/desktop/release-notes.md), execute os
seguintes comandos em um terminal para instalar o Docker Desktop na pasta
**Aplicativos**:

```console
$ sudo hdiutil attach Docker.dmg
$ sudo /Volumes/Docker/Docker.app/Contents/MacOS/install
$ sudo hdiutil detach /Volumes/Docker
```

Por padrão, o Docker Desktop é instalado em `/Aplicativos/Docker.app`.
Como o macOS geralmente realiza verificações de segurança na primeira vez que
uma aplicação é usada, o comando `install` pode levar alguns minutos para ser
executado.

#### Opções do instalador

O comando `install` aceita as seguintes flags:

##### Comportamento da instalação

- `--accept-license`: Aceita o
  [Contrato de Serviço de Assinatura do Docker](https://www.docker.com/legal/docker-subscription-service-agreement)
  agora, em vez de exigir que ele seja aceito na primeira execução da aplicação.
- `--user=<nome-de-usuário>`: Executa as configurações privilegiadas uma única
  vez durante a instalação.
  Isso elimina a necessidade de a pessoa usuária conceder privilégios de root na
  primeira execução.
  Para obter mais informações, consulte
  [Requisitos de permissão do auxiliar privilegiado](/manuals/desktop/setup/install/mac-permission-requirements.md#requisitos-de-permissão).
  Para encontrar o nome de usuário, digite `ls /Users` na linha de comando.

- ##### Segurança e acesso

- `--allowed-org=<nome-da-organização>`: Exige que a pessoa usuária faça login e
  pertença à organização especificada do Docker Hub ao executar a aplicação.
- `--user=<nome-de-usuário>`: Executa as configurações privilegiadas uma única
  vez durante a instalação.
  Isso elimina a necessidade de a pessoa usuária conceder privilégios de root na
  primeira execução.
  Para obter mais informações, consulte
  [Requisitos de permissão do auxiliar privilegiado](/manuals/desktop/setup/install/mac-permission-requirements.md#requisitos-de-permissão).
  Para encontrar o nome de usuário, digite `ls /Users` na CLI.
- `--admin-settings`: Cria automaticamente um arquivo `admin-settings.json` que
  é usado por pessoas administradoras para controlar determinadas configurações
  do Docker Desktop em máquinas cliente dentro de sua organização.
  Para obter mais informações, consulte
  [Gerenciamento de configurações](/manuals/enterprise/security/hardened-desktop/settings-management/_index.md).
  - Deve ser usado em conjunto com a flag `--allowed-org=<nome-da-organização>`.
  - Por exemplo:
    `--allowed-org=<nome-da-organização> --admin-settings="{'configurationFileVersion': 2, 'enhancedContainerIsolation': {'value': true, 'locked': false}}"`

##### Configuração de proxy

- `--proxy-http-mode=<modo>`: Define o modo do proxy HTTP. Os dois modos são
  `system` (padrão) ou `manual`.
- `--override-proxy-http=<url>`: Define a URL do proxy HTTP que deve ser usado
  para requisições HTTP de saída.
  É necessário que `--proxy-http-mode` seja definido como `manual`.
- `--override-proxy-https=<url>`: Define a URL do proxy HTTP que deve ser usado
  para requisições HTTPS de saída.
  É necessário que `--proxy-http-mode` seja definido como `manual`.
- `--override-proxy-exclude=<hosts/domains>`: Ignora as configurações de proxy
  para os hosts e domínios especificados.
  É uma lista separada por vírgulas.
- `--override-proxy-pac=<url-do-arquivo-pac>`: Define a URL do arquivo PAC.
  Essa configuração só entra em vigor quando o modo de proxy é definido como
  `manual`.
- `--override-proxy-embedded-pac=<script-pac>`: Especifica um script PAC (Proxy
  Auto-Config) incorporado.
  Essa configuração só entra em vigor quando o modo de proxy é definido como
  `manual` e tem precedência sobre a opção `--override-proxy-pac`.

###### Exemplo de especificação do arquivo PAC

```console
$ sudo /Aplicativos/Docker.app/Contents/MacOS/install --user testuser --proxy-http-mode="manual" --override-proxy-pac="http://localhost:8080/myproxy.pac"
```

###### Exemplo de especificação do script PAC

```console
$ sudo /Aplicativos/Docker.app/Contents/MacOS/install --user testuser --proxy-http-mode="manual" --override-proxy-embedded-pac="function FindProxyForURL(url, host) { return \"DIRECT\"; }"
```

> [!TIP]
>
> Como pessoa administradora de TI, você pode usar um software de gerenciamento
> de endpoints (MDM) para identificar o número de instâncias do Docker Desktop e
> suas versões em seu ambiente.
> Isso pode fornecer relatórios de licença precisos, ajudar a garantir que suas
> máquinas usem a versão mais recente do Docker Desktop e permitir que você
> [imponha o login](/manuals/enterprise/security/enforce-sign-in/_index.md).
>
> - [Intune](https://learn.microsoft.com/en-us/mem/intune/apps/app-discovered-apps)
> - [Jamf](https://docs.jamf.com/10.25.0/jamf-pro/administrator-guide/Application_Usage.html)
> - [Kandji](https://support.kandji.io/support/solutions/articles/72000559793-view-a-device-application-list)
> - [Kolide](https://www.kolide.com/features/device-inventory/properties/mac-apps)
> - [Workspace One](https://blogs.vmware.com/euc/2022/11/how-to-use-workspace-one-intelligence-to-manage-app-licenses-and-reduce-costs.html)

## Próximos passos

- Explore as
  [assinaturas do Docker](https://www.docker.com/pricing?ref=Docs&refAction=DocsDesktopMacInstall)
  para ver o que o Docker pode oferecer.
- [Começando com o Docker](/get-started/introduction/_index.md).
- [Explore o Docker Desktop](/manuals/desktop/use-desktop/_index.md) e todos os
  seus recursos.
- [Solução de problemas](/manuals/desktop/troubleshoot-and-support/troubleshoot/_index.md)
  descreve problemas comuns, soluções alternativas, como executar e enviar
  diagnósticos e como relatar problemas.
- [Perguntas frequentes](/manuals/desktop/troubleshoot-and-support/faqs/general.md)
  fornece respostas para perguntas frequentes.
- [Notas de lançamento](/manuals/desktop/release-notes.md) lista atualizações de
  componentes, novos recursos e melhorias associadas às versões do Docker
  Desktop.
- [Backup e restauração de dados](/manuals/desktop/settings-and-maintenance/backup-and-restore.md)
  fornece instruções sobre como fazer backup e restaurar dados relacionados ao
  Docker.
