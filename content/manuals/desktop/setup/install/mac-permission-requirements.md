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

source_url: https://github.com/docker/docs/blob/main/content/manuals/desktop/setup/install/mac-permission-requirements.md
source_revision: f0e4e4790191aaee83f9375dce56ada3971c6773
translation_status: ready

description: >-
  Entenda os requisitos de permissão para o Docker Desktop no Mac e as
  diferenças entre as versões.
keywords: docker desktop, mac, segurança, instalação, permissões
title: Entenda os requisitos de permissão para o Docker Desktop no Mac
linkTitle: Requisitos de permissão do Mac
aliases:
  - /desktop/mac/permission-requirements/
weight: 20
---

Esta página contém informações sobre os requisitos de permissão para executar e
instalar o Docker Desktop no Mac.

Também esclarece a diferença entre executar contêineres como `root` e ter acesso
`root` no host.

O Docker Desktop para Mac foi projetado com foco em segurança.
Direitos administrativos são necessários apenas quando absolutamente
imprescindível.

## Requisitos de permissão

O Docker Desktop para Mac é executado como um usuário sem privilégios.
No entanto, o Docker Desktop requer certas funcionalidades para realizar um
conjunto limitado de configurações privilegiadas, como:
- [Instalar links simbólicos](#instalando-links-simbólicos) em `/usr/local/bin`.
- [Vincular portas privilegiadas](#vinculando-portas-privilegiadas) com número
  de acesso inferior a 1024.
  Embora as portas privilegiadas (portas abaixo de 1024) normalmente não sejam
  usadas como uma barreira de segurança, os sistemas operacionais ainda impedem
  que processos sem privilégios se vinculem a elas, o que impede a execução de
  comandos como `docker run -p 127.0.0.1:80:80 docker/getting-started`.
- [Garantir que `localhost` e `kubernetes.docker.internal` estejam definidos](#garantindo-que-localhost-e-kubernetesdockerinternal-estejam-definidos)
  em `/etc/hosts`.
  Algumas instalações antigas do macOS não possuem `localhost` em `/etc/hosts`,
  o que faz com que o Docker falhe.
  Definir o nome DNS `kubernetes.docker.internal` permite que o Docker
  compartilhe contextos do Kubernetes com os contêineres.
- Armazenar em cache com segurança a política de Registry Access Management, que
  é somente leitura para a pessoa desenvolvedora.

O acesso privilegiado é concedido durante a instalação.

Ao executar o Docker Desktop para Mac pela primeira vez, uma janela de
instalação será exibida, onde você poderá escolher entre usar as configurações
padrão, que atendem à maioria das pessoas desenvolvedoras e exigem a concessão
de acesso privilegiado, ou usar as configurações avançadas.

Se você trabalha em um ambiente com requisitos de segurança elevados, por
exemplo, onde o acesso administrativo local é proibido, você pode usar as
configurações avançadas para eliminar a necessidade de conceder acesso
privilegiado.
Você pode configurar:
- A localização das ferramentas de linha de comando do Docker, seja no diretório
  do sistema ou do usuário;
- O socket padrão do Docker;
- O mapeamento de portas privilegiadas.

Dependendo das configurações avançadas que você escolher, será necessário
inserir sua senha para confirmar.

Você pode alterar essas configurações posteriormente na página **Advanced** em
**Settings**.

### Instalando links simbólicos

Os binários do Docker são instalados por padrão em
`/Applications/Docker.app/Contents/Resources/bin`.
O Docker Desktop cria links simbólicos para os binários em `/usr/local/bin`, o
que significa que eles são automaticamente incluídos no `PATH` na maioria dos
sistemas.

Você pode escolher se deseja instalar links simbólicos em `/usr/local/bin` ou em
`$HOME/.docker/bin` durante a instalação do Docker Desktop.

Se `/usr/local/bin` for escolhido e esse local não tiver permissão de escrita
para usuários sem privilégios, o Docker Desktop exigirá autorização para
confirmar essa escolha antes que os links simbólicos para os binários do Docker
sejam criados em `/usr/local/bin`.
Se `$HOME/.docker/bin` for selecionado, a autorização não será necessária, mas
você deverá
[adicionar manualmente `$HOME/.docker/bin`](/manuals/desktop/settings-and-maintenance/settings.md#advanced)
ao seu PATH.

Você também tem a opção de habilitar a instalação do link simbólico
`/var/run/docker.sock`.
A criação desse link simbólico garante que vários clientes Docker que dependem
do caminho padrão do socket Docker funcionem sem alterações adicionais.

Como `/var/run` é montado como um sistema de arquivos temporário (tmpfs), seu
conteúdo é excluído na reinicialização, incluindo o link simbólico para o socket
Docker.
Para garantir que o socket Docker exista após a reinicialização, o Docker
Desktop configura uma tarefa de inicialização `launchd` que cria o link
simbólico executando
`ln -s -f /Users/<user>/.docker/run/docker.sock /var/run/docker.sock`.
Isso garante que você não tenha que criar o link simbólico a cada inicialização.
Se você não habilitar essa opção durante a instalação, o link simbólico e a
tarefa de inicialização não serão criados e você poderá ter que definir
explicitamente a variável de ambiente `DOCKER_HOST` como
`/Users/<usuário>/.docker/run/docker.sock` nos clientes que a usam.
A CLI do Docker depende do contexto atual para recuperar o caminho do socket; o
contexto atual é definido como `desktop-linux` na inicialização do Docker
Desktop.

### Vinculando portas privilegiadas

Você pode optar por habilitar o mapeamento de portas privilegiadas durante a
instalação ou na página **Advanced** em **Settings** após a instalação.
O Docker Desktop requer autorização para confirmar essa escolha.

### Garantindo que `localhost` e `kubernetes.docker.internal` estejam definidos

É sua responsabilidade garantir que `localhost` seja resolvido para `127.0.0.1`
e, se o Kubernetes for usado, que `kubernetes.docker.internal` seja resolvido
para `127.0.0.1`.

## Instalando pela linha de comando

Configurações com privilégios de administrador são aplicadas durante a
instalação com a opção `--user` no
[comando de instalação](/manuals/desktop/setup/install/mac-install.md#instale-pela-linha-de-comando).
Nesse caso, a conceção privilégios de administrador não será sugerida na
primeira execução do Docker Desktop.
Especificamente, a opção `--user`:
- Desinstala o `com.docker.vmnetd` anterior, se presente.
- Cria links simbólicos.
- Garante que `localhost` seja resolvido para `127.0.0.1`.

A limitação dessa abordagem é que o Docker Desktop só pode ser executado por uma
conta de usuário por máquina, ou seja, aquela especificada na opção `--user`.

## Auxiliar privilegiado

Em situações específicas onde o auxiliar privilegiado é necessário, como por
exemplo, para vincular portas privilegiadas ou armazenar em cache a política de
Registry Access Management, ele é iniciado pelo `launchd` e executado em segundo
plano, a menos que seja desativado em tempo de execução, conforme descrito
anteriormente.
O backend do Docker Desktop se comunica com o auxiliar privilegiado através do
socket de domínio UNIX `/var/run/com.docker.vmnetd.sock`.
As funcionalidades que ele executa são:
- Vincular portas privilegiadas com número de acesso inferior a 1024.
- Armazenar em cache, de forma segura, a política de Registry Access Management,
  que é somente leitura para a pessoa desenvolvedora.
- Desinstalar o auxiliar privilegiado.

A remoção do processo do auxiliar privilegiado é feita da mesma forma que a
remoção dos processos do `launchd`.

```console
$ ps aux | grep vmnetd
root             28739   0.0  0.0 34859128    228   ??  Ss    6:03PM   0:00.06 /Library/PrivilegedHelperTools/com.docker.vmnetd
user             32222   0.0  0.0 34122828    808 s000  R+   12:55PM   0:00.00 grep vmnetd

$ sudo launchctl unload -w /Library/LaunchDaemons/com.docker.vmnetd.plist
Password:

$ ps aux | grep vmnetd
user             32242   0.0  0.0 34122828    716 s000  R+   12:55PM   0:00.00 grep vmnetd

$ rm /Library/LaunchDaemons/com.docker.vmnetd.plist

$ rm /Library/PrivilegedHelperTools/com.docker.vmnetd
```

## Contêineres executados como root dentro da VM Linux

Com o Docker Desktop, o daemon do Docker e os contêineres são executados em uma
VM Linux leve gerenciada pelo Docker.
Isso significa que, embora os contêineres sejam executados por padrão como
`root`, isso não concede acesso de `root` à máquina host Mac.
A VM Linux serve como um limite de segurança e limita quais recursos podem ser
acessados a partir do host.
Quaisquer diretórios do host montados em contêineres Docker ainda mantêm suas
permissões originais.

## Isolamento de contêiner aprimorado

Além disso, o Docker Desktop oferece suporte ao
[modo de Isolamento de Contêiner Aprimorado (Enhanced Container Isolation, ou ECI)](/manuals/enterprise/security/hardened-desktop/enhanced-container-isolation/_index.md),
disponível apenas para clientes Business, que protege ainda mais os contêineres
sem impactar os fluxos de trabalho das pessoas desenvolvedoras.

O ECI executa automaticamente todos os contêineres em um namespace de usuário
Linux, de forma que o usuário root no contêiner seja mapeado para um usuário sem
privilégios dentro da VM Linux do Docker Desktop.
O ECI usa essa e outras técnicas avançadas para proteger ainda mais os
contêineres dentro da VM Linux do Docker Desktop, de forma que eles fiquem ainda
mais isolados do daemon do Docker e de outros serviços em execução dentro da VM.
