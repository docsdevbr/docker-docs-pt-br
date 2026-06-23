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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/running-containers/sharing-local-files.md
source_revision: dcfd3df3cb473376da57941a966eaedc6724d4d1
translation_status: ready

title: Compartilhando arquivos locais com contêineres
weight: 4
keywords: conceitos, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará você sobre as várias opções de armazenamento
  disponíveis no Docker e seus usos comuns.
aliases:
 - /guides/docker-concepts/running-containers/sharing-local-files/
---

{{< youtube-embed 2dAzsVg3Dek >}}

## Explicação

Cada contêiner possui tudo o que precisa para funcionar, sem depender de nenhuma
dependência pré-instalada na máquina host.
Como os contêineres são executados isoladamente, eles têm influência mínima
sobre o host e outros contêineres.
Esse isolamento traz um grande benefício: os contêineres minimizam conflitos com
o sistema host e outros contêineres.
No entanto, esse isolamento também significa que, por padrão, os contêineres não
podem acessar diretamente os dados na máquina host.

Considere um cenário em que você tenha um contêiner de aplicação web que precise
acessar configurações armazenadas em um arquivo no seu sistema host.
Esse arquivo pode conter dados confidenciais, como credenciais de banco de dados
ou chaves de API.
Armazenar essas informações confidenciais diretamente na imagem do contêiner
representa riscos de segurança, especialmente durante o compartilhamento de
imagens.
Para solucionar esse desafio, o Docker oferece opções de armazenamento que
preenchem a lacuna entre o isolamento do contêiner e os dados da sua máquina
host.

O Docker oferece duas opções principais de armazenamento para persistir dados e
compartilhar arquivos entre a máquina host e os contêineres: volumes e bind
mounts (montagens de diretórios).

### Volumes versus bind mounts

Se você deseja garantir que os dados gerados ou modificados dentro do contêiner
persistam mesmo após a interrupção da execução, você deve optar por um volume.
Consulte
[Persistindo dados do contêiner](/get-started/docker-concepts/running-containers/persisting-container-data/)
para saber mais sobre volumes e seus casos de uso.

Se você tiver arquivos ou diretórios específicos em seu sistema host que deseja
compartilhar diretamente com seu contêiner, como arquivos de configuração ou
código de desenvolvimento, você deve usar uma montagem de diretórios.
É como abrir um portal direto entre seu host e o contêiner para
compartilhamento.
As montagens de diretórios são ideais para ambientes de desenvolvimento onde o
acesso e o compartilhamento de arquivos em tempo real entre o host e o contêiner
são cruciais.

### Compartilhando arquivos entre um host e um contêiner

As opções `-v` (ou `--volume`) e `--mount` usadas com o comando `docker run`
permitem compartilhar arquivos ou diretórios entre sua máquina local (host) e um
contêiner Docker.
No entanto, existem algumas diferenças importantes em seu comportamento e uso.

A opção `-v` é mais simples e conveniente para operações básicas de volumes ou
bind mounts.
Se o local no host não existir ao usar `-v` ou `--volume`, um diretório será
criado automaticamente.

Imagine que você é uma pessoa desenvolvedora trabalhando em um projeto.
Você tem um diretório de origem em sua máquina de desenvolvimento onde seu
código reside.
Quando você compila ou constrói seu código, os artefatos gerados (código
compilado, executáveis, imagens, etc.) são salvos em um subdiretório separado
dentro do seu diretório de origem.
Nos exemplos a seguir, este subdiretório é `/HOST/PATH`.
Agora você deseja que esses artefatos de construção sejam acessíveis em um
contêiner Docker executando sua aplicação.
Além disso, você deseja que o contêiner acesse automaticamente os artefatos de
compilação mais recentes sempre que recompilar seu código.

Veja como usar o `docker run` para iniciar um contêiner usando um bind mount e
mapeá-lo para o local do arquivo do contêiner.

```console
$ docker run -v /HOST/PATH:/CONTAINER/PATH -it nginx
```

A flag `--mount` oferece recursos mais avançados e controle granular, tornando-a
adequada para cenários de montagem complexos ou implantações em produção.
Por padrão, se você usar `--mount` para montar um arquivo ou diretório que ainda
não existe no host do Docker, o comando `docker run` não o criará
automaticamente, mas gerará um erro.

```console
$ docker run --mount type=bind,source=/HOST/PATH,target=/CONTAINER/PATH,readonly nginx
```

> [!NOTE]
>
> O Docker recomenda o uso da sintaxe `--mount` em vez de `-v`.
> Ela oferece melhor controle sobre o processo de montagem e evita possíveis
> problemas com diretórios ausentes.

### Permissões de arquivos para acesso do Docker aos arquivos do host

Ao usar bind mounts, é crucial garantir que o Docker tenha as permissões
necessárias para acessar o diretório do host.
Para conceder acesso de leitura/gravação, você pode usar a flag `:ro`
(read-only, somente leitura) ou `:rw` (read-write, leitura e gravação) com a
flag `-v` ou `--mount` durante a criação do contêiner.
Por exemplo, o comando a seguir concede permissão de acesso de leitura e
gravação.

```console
$ docker run -v HOST-DIRECTORY:/CONTAINER-DIRECTORY:rw nginx
```

Bind mounts somente leitura permitem que o contêiner acesse os arquivos montados
no host para leitura, mas não pode alterá-los ou excluí-los.
Com bind mounts de leitura e gravação, os contêineres podem modificar ou excluir
arquivos montados, e essas alterações ou exclusões também serão refletidas no
sistema host.
Binding mounts somente leitura garantem que os arquivos no host não possam ser
modificados ou excluídos acidentalmente por um contêiner.

> **Compartilhamento de arquivos sincronizado**
>
> À medida que sua base de código cresce, os métodos tradicionais de
> compartilhamento de arquivos, como bind mounts, podem se tornar ineficientes
> ou lentos, especialmente em ambientes de desenvolvimento onde o acesso
> frequente a arquivos é necessário.
> Os
> [compartilhamentos de arquivos sincronizados](/manuals/desktop/features/synchronized-file-sharing.md)
> melhoram o desempenho das montagens de diretórios, aproveitando os caches
> sincronizados do sistema de arquivos.
> Essa otimização garante que o acesso a arquivos entre o host e a máquina
> virtual (VM) seja rápido e eficiente.

## Experimente

Neste guia prático, você aprenderá como criar e usar uma montagem de diretórios
para compartilhar arquivos entre um host e um contêiner.

### Execute um contêiner

1. [Baixe e instale](/get-started/get-docker/) o Docker Desktop.

2. Inicie um contêiner usando a imagem [httpd](https://hub.docker.com/_/httpd)
   com o seguinte comando:

   ```console
   $ docker run -d -p 8080:80 --name my_site httpd:2.4
   ```

   Isso iniciará o serviço `httpd` em segundo plano e publicará a página web na
   porta `8080` do host.

3. Abra o navegador e acesse [http://localhost:8080](http://localhost:8080) ou
   use o comando curl para verificar se está funcionando corretamente.

   ```console
   $ curl localhost:8080
   ```

### Use um bind mount

Usando um bind mount, você pode mapear o arquivo de configuração do seu
computador host para um local específico dentro do contêiner.
Neste exemplo, você verá como alterar a aparência da página da web usando um
bind mount:

1. Exclua o contêiner existente usando o painel do Docker Desktop:

   ![Uma captura de tela do painel do Docker Desktop mostrando como excluir o contêiner httpd](images/delete-httpd-container.webp?border=true)

2. Crie um novo diretório chamado `public_html` no seu sistema host.

   ```console
   $ mkdir public_html
   ```

3. Navegue até o diretório recém-criado `public_html` e crie um arquivo chamado
   `index.html` com o seguinte conteúdo.
   Este é um documento HTML básico que cria uma página web simples que lhe dá as
   boas-vindas com uma baleia amigável.

   ```html
   <!DOCTYPE html>
   <html lang="pt-br">
   <head>
   <meta charset="UTF-8">
   <title>Meu site com uma baleia e Docker!</title>
   </head>
   <body>
   <h1>Boas vindas!!</h1>
   <p>Look! There's a friendly whale greeting you!</p>
   <pre id="docker-art">
       ##         .
      ## ## ##        ==
     ## ## ## ## ##    ===
    /"""""""""""""""""\___/ ===
   {                       /  ===-
    \______ O           __/
     \    \         __/
      \____\_______/

   Olá do Docker!
   </pre>
   </body>
   </html>
   ```

4. Chegou a hora de executar o contêiner.
   Os exemplos `--mount` e `-v` produzem o mesmo resultado.
   Você não pode executá-los simultaneamente a menos que remova o contêiner
   `my_site` após executar o primeiro.

   {{< tabs >}}
   {{< tab name="`-v`" >}}

   ```console
   $ docker run -d --name my_site -p 8080:80 -v .:/usr/local/apache2/htdocs/ httpd:2.4
   ```

   {{< /tab >}}
   {{< tab name="`--mount`" >}}

   ```console
   $ docker run -d --name my_site -p 8080:80 --mount type=bind,source=./,target=/usr/local/apache2/htdocs/ httpd:2.4
   ```

   {{< /tab >}}
   {{< /tabs >}}

   > [!TIP]
   >
   > Ao usar a opção `-v` ou `--mount` no Windows PowerShell, você precisa
   > fornecer o caminho absoluto para o seu diretório em vez de apenas `./`.
   > Isso ocorre porque o PowerShell lida com caminhos relativos de forma
   > diferente do bash (comumente usado em ambientes Mac e Linux).

   Com tudo configurado e funcionando, você poderá acessar o site via
   [http://localhost:8080](http://localhost:8080) e encontrará uma nova página
   web que lhe dá as boas-vindas com uma baleia amigável.

### Acesse o arquivo no painel do Docker Desktop

1. Você pode visualizar os arquivos montados em um contêiner selecionando a guia
   **Files** do contêiner e, em seguida, selecionando um arquivo dentro do
   diretório `/usr/local/apache2/htdocs/`.
   Depois, selecione **Open file editor**.

   ![Uma captura de tela do painel do Docker Desktop mostrando os arquivos montados no contêiner](images/mounted-files.webp?border=true)

2. Exclua o arquivo no host e verifique se ele também foi excluído no contêiner.
   Você verá que os arquivos não existem mais em **Files** no painel do Docker
   Desktop.

   ![Uma captura de tela do painel do Docker Desktop mostrando os arquivos excluídos no contêiner](images/deleted-files.webp?border=true)

3. Recrie o arquivo HTML no sistema host e veja que ele reaparece na guia
   **Files** em **Containers** no painel do Docker Desktop.
   Agora você também poderá acessar o site.

### Pare seu contêiner

O contêiner continua em execução até que você o pare.

1. Acesse a visualização **Containers** no painel do Docker Desktop.

2. Localize o contêiner que você deseja parar.

3. Selecione a ação **Delete** na coluna Actions.

![Uma captura de tela do painel do Docker Desktop mostrando como excluir o contêiner](images/delete-the-container.webp?border=true)

## Recursos adicionais

Os seguintes recursos ajudarão você a aprender mais sobre bind mounts:

- [Gerencie dados no Docker](/storage/)
- [Volumes](/storage/volumes/)
- [Bind mounts](/storage/bind-mounts/)
- [Executando contêineres](/reference/run/)
- [Solucione problemas de armazenamento](/storage/troubleshooting_volume_errors/)
- [Persistindo dados do contêiner](/get-started/docker-concepts/running-containers/persisting-container-data/)

## Próximos passos

Agora que você aprendeu sobre como compartilhar arquivos locais com contêineres,
é hora de aprender sobre aplicações multicontêineres.

{{< button text="Aplicações multicontêineres" url="multi-container-applications" >}}
