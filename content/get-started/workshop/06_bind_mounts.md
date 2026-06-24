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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/06_bind_mounts.md
source_revision: cd5615c3da190b1f44e54c5105b6d937251eed72
translation_status: ready

title: Use bind mounts
weight: 60
linkTitle: "Parte 5: Use bind mounts"
keywords: >-
  introdução, configuração, orientação, guia rápido, conceitos, containers,
  docker desktop
description: Usando bind mounts em nossa aplicação.
aliases:
  - /guides/walkthroughs/access-local-folder/
  - /get-started/06_bind_mounts/
  - /guides/workshop/06_bind_mounts/
---

Na [parte 4](./05_persisting_data.md), você utilizou uma montagem de volume para
persistir os dados do seu banco de dados.
Uma montagem de volume é uma excelente escolha quando você precisa de um local
persistente para armazenar os dados da sua aplicação.

Um bind mount (montagem bind) é outro tipo de montagem que permite compartilhar
um diretório do sistema de arquivos do host com o container.
Ao desenvolver uma aplicação, você pode usar um bind mount para montar o
código-fonte dentro do container.
O container percebe as alterações feitas no código imediatamente, assim que você
salva um arquivo.
Isso significa que você pode executar processos no container que monitoram
alterações no sistema de arquivos e reagem a elas.

Neste capítulo, você verá como utilizar bind mounts e uma ferramenta chamada
[nodemon](https://npmjs.com/package/nodemon) para monitorar alterações em
arquivos e, em seguida, reiniciar a aplicação automaticamente.
Existem ferramentas equivalentes na maioria das outras linguagens e frameworks.

## Comparação rápida entre tipos de volume

Abaixo estão exemplos de um volume nomeado e de um bind mount utilizando
`--mount`:

- Volume nomeado: `type=volume,src=my-volume,target=/usr/local/data`
- Bind mount: `type=bind,src=/path/to/data,target=/usr/local/data`

A tabela a seguir apresenta as principais diferenças entre montagens de volume e
bind mounts.

|                                                | Volumes nomeados | Bind mounts |
|------------------------------------------------|------------------| ----------- |
| Localização no host                            | O Docker escolhe | Você decide |
| Preenche novo volume com conteúdo do container | Sim              | Não         |
| Suporta drivers de volume                      | Sim              | Não         |

## Experimentando bind mounts

Antes de ver como você pode usar bind mounts para desenvolver sua aplicação,
você pode realizar um experimento rápido para entender na prática como as
bind mounts funcionam.

1. Verifique se o diretório `getting-started-app` está localizado em um
   diretório definido nas configurações de compartilhamento de arquivos do
   Docker Desktop.
   Essa configuração define quais partes do seu sistema de arquivos podem ser
   compartilhadas com os containers.
   Para detalhes sobre como acessar essa configuração, consulte
   [Compartilhamento de arquivos](/manuals/desktop/settings-and-maintenance/settings.md#file-sharing).

   > [!NOTE]
   >
   > A aba **File sharing** está disponível apenas no modo Hyper-V, pois os
   > arquivos são compartilhados automaticamente nos modos WSL 2 e containers do
   > Windows.

2. Abra um terminal e navegue até o diretório `getting-started-app`.

3. Execute o comando a seguir para iniciar o `bash` em um container `ubuntu` com
   um bind mount.

   {{< tabs >}}
   {{< tab name="Mac / Linux" >}}

   ```console
   $ docker run -it --mount type=bind,src=.,target=/src ubuntu bash
   ```

   {{< /tab >}}
   {{< tab name="Prompt de Comando" >}}

   ```console
   $ docker run -it --mount "type=bind,src=%cd%,target=/src" ubuntu bash
   ```

   {{< /tab >}}
   {{< tab name="Git Bash" >}}

   ```console
   $ docker run -it --mount type=bind,src="./",target=/src ubuntu bash
   ```

   {{< /tab >}}
   {{< tab name="PowerShell" >}}

   ```console
   $ docker run -it --mount "type=bind,src=.,target=/src" ubuntu bash
   ```

   {{< /tab >}}
   {{< /tabs >}}

   A opção `--mount type=bind` instrui o Docker a criar um bind mount, no qual
   `src` é o diretório de trabalho atual na sua máquina host
   (`getting-started-app`) e `target` é o local onde esse diretório deve
   aparecer dentro do container (`/src`).

4. Após executar o comando, o Docker inicia uma sessão `bash` interativa no
   diretório raiz do sistema de arquivos do contêiner.

   ```console
   root@ac1237fad8db:/# pwd
   /
   root@ac1237fad8db:/# ls
   bin   dev  home  media  opt   root  sbin  srv  tmp  var
   boot  etc  lib   mnt    proc  run   src   sys  usr
   ```

5. Mude para o diretório `src`.

   Este é o diretório que você montou ao iniciar o container.
   Listar o conteúdo deste diretório exibe os mesmos arquivos presentes no
   diretório `getting-started-app` da sua máquina host.

   ```console
   root@ac1237fad8db:/# cd src
   root@ac1237fad8db:/src# ls
   Dockerfile  node_modules  package.json  package-lock.json  spec  src
   ```

6. Crie um novo arquivo chamado `myfile.txt`.

   ```console
   root@ac1237fad8db:/src# touch myfile.txt
   root@ac1237fad8db:/src# ls
   Dockerfile  myfile.txt  node_modules  package.json  package-lock.json  spec  src
   ```

7. Abra o diretório `getting-started-app` no host e observe que o arquivo
   `myfile.txt` está no diretório.

   ```text
   ├── getting-started-app/
   │ ├── Dockerfile
   │ ├── myfile.txt
   │ ├── node_modules/
   │ ├── package.json
   │ ├── package-lock.json
   │ ├── spec/
   │ └── src/
   ```

8. No host, exclua o arquivo `myfile.txt`.

9. No contêiner, liste o conteúdo do diretório `app` mais uma vez.
   Observe que o arquivo não está mais presente.

   ```console
   root@ac1237fad8db:/src# ls
   Dockerfile  node_modules  package.json  package-lock.json spec  src
   ```

10. Encerre a sessão interativa do contêiner com `Ctrl` + `D`.

Isso é tudo para uma breve introdução aos bind mounts.
Este procedimento demonstrou como os arquivos são compartilhados entre o host e
o contêiner, e como as alterações são refletidas imediatamente em ambos os
lados.
Agora você pode usar bind mounts para desenvolver software.

## Containers de desenvolvimento

O uso de bind mounts é comum em configurações de desenvolvimento local.
A vantagem é que a máquina de desenvolvimento não precisa ter todas as
ferramentas de build e ambientes instalados.
Com um único comando `docker run`, o Docker baixa as dependências e ferramentas
necessárias.

### Execute sua aplicação em um container de desenvolvimento

Os passos a seguir descrevem como executar um container de desenvolvimento com
um bind mount que realiza as seguintes ações:

- Monta o código-fonte dentro do container.
- Instala todas as dependências.
- Inicia o `nodemon` para monitorar alterações no sistema de arquivos.

Você pode usar a CLI ou o Docker Desktop para executar seu container com um bind
mount.

{{< tabs >}}
{{< tab name="CLI para Mac / Linux" >}}

1. Certifique-se de que não há nenhum contêiner `getting-started` em execução no
   momento.

2. Execute o seguinte comando a partir do diretório `getting-started-app`.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 \
       -w /app --mount type=bind,src=.,target=/app \
       node:24-alpine \
       sh -c "npm install && npm run dev"
   ```

   Abaixo está o detalhamento do comando:
   - `-dp 127.0.0.1:3000:3000` - o mesmo que anteriormente.
     Executa em modo detached (em segundo plano) e cria um mapeamento de
     porta.
   - `-w /app` - define o "diretório de trabalho" ou o diretório atual a partir
     do qual o comando será executado.
   - `--mount type=bind,src=.,target=/app` - cria um bind mount do diretório
     atual do host para o diretório `/app` dentro do container.
   - `node:24-alpine` - a imagem a ser utilizada.
     Observe que esta é a imagem base para sua aplicação, definida no
     Dockerfile.
   - `sh -c "npm install && npm run dev"` - o comando.
     Você inicia um shell usando `sh` (o Alpine não possui `bash`) e executa
     `npm install` para instalar os pacotes e, em seguida, executa `npm run dev`
     para iniciar o servidor de desenvolvimento.
     Se você verificar o arquivo `package.json`, verá que o script `dev` inicia
     o `nodemon`.

3. Você pode acompanhar os logs usando `docker logs <id-do-contêiner>`.
   Você saberá que está tudo pronto quando vir isto:

   ```console
   $ docker logs -f <id-do-contêiner>
   nodemon -L src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,json
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

   Quando terminar de acompanhar os logs, saia pressionando `Ctrl`+`C`.

{{< /tab >}}
{{< tab name="CLI do PowerShell" >}}

1. Certifique-se de que não há nenhum contêiner `getting-started` em execução no
   momento.

2. Execute o seguinte comando a partir do diretório `getting-started-app`.

   ```powershell
   $ docker run -dp 127.0.0.1:3000:3000 `
       -w /app --mount "type=bind,src=.,target=/app" `
       node:24-alpine `
       sh -c "npm install && npm run dev"
   ```

   Abaixo está o detalhamento do comando:
   - `-dp 127.0.0.1:3000:3000` - o mesmo que anteriormente.
     Executa em modo detached (em segundo plano) e cria um mapeamento de
     porta.
   - `-w /app` - define o "diretório de trabalho" ou o diretório atual a partir
     do qual o comando será executado.
   - `--mount "type=bind,src=.,target=/app"` - cria um bind mount do diretório
     atual do host para o diretório `/app` dentro do container.
   - `node:24-alpine` - a imagem a ser utilizada.
     Observe que esta é a imagem base para sua aplicação, definida no
     Dockerfile.
   - `sh -c "npm install && npm run dev"` - o comando.
     Você inicia um shell usando `sh` (o Alpine não possui `bash`) e executa
     `npm install` para instalar os pacotes e, em seguida, executa `npm run dev`
     para iniciar o servidor de desenvolvimento.
     Se você verificar o arquivo `package.json`, verá que o script `dev` inicia
     o `nodemon`.

3. Você pode acompanhar os logs usando `docker logs <id-do-contêiner>`.
   Você saberá que está tudo pronto quando vir isto:

   ```console
   $ docker logs -f <id-do-contêiner>
   nodemon -L src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,json
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

   Quando terminar de acompanhar os logs, saia pressionando `Ctrl`+`C`.

{{< /tab >}}
{{< tab name="CLI do Prompt de Comando" >}}

1. Certifique-se de que não haja nenhum contêiner `getting-started` em execução
   no momento.

2. Execute o seguinte comando a partir do diretório `getting-started-app`.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 ^
       -w /app --mount "type=bind,src=%cd%,target=/app" ^
       node:24-alpine ^
       sh -c "npm install && npm run dev"
   ```

   Abaixo está o detalhamento do comando:
   - `-dp 127.0.0.1:3000:3000` - o mesmo que anteriormente.
     Executa em modo detached (em segundo plano) e cria um mapeamento de
     porta.
   - `-w /app` - define o "diretório de trabalho" ou o diretório atual a partir
     do qual o comando será executado.
   - `--mount "type=bind,src=%cd%,target=/app"` - cria um bind mount do diretório
     atual do host para o diretório `/app` dentro do container.
   - `node:24-alpine` - a imagem a ser utilizada.
     Observe que esta é a imagem base para sua aplicação, definida no
     Dockerfile.
   - `sh -c "npm install && npm run dev"` - o comando.
     Você inicia um shell usando `sh` (o Alpine não possui `bash`) e executa
     `npm install` para instalar os pacotes e, em seguida, executa `npm run dev`
     para iniciar o servidor de desenvolvimento.
     Se você verificar o arquivo `package.json`, verá que o script `dev` inicia
     o `nodemon`.

3. Você pode acompanhar os logs usando `docker logs <id-do-contêiner>`.
   Você saberá que está tudo pronto quando vir isto:

   ```console
   $ docker logs -f <id-do-contêiner>
   nodemon -L src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,json
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

   Quando terminar de acompanhar os logs, saia pressionando `Ctrl`+`C`.

{{< /tab >}}
{{< tab name="CLI do Git Bash" >}}

1. Certifique-se de que não há nenhum contêiner `getting-started` em execução no
   momento.

2. Execute o seguinte comando a partir do diretório `getting-started-app`.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 \
       -w //app --mount type=bind,src="./",target=/app \
       node:24-alpine \
       sh -c "npm install && npm run dev"
   ```

   Abaixo está o detalhamento do comando:
   - `-dp 127.0.0.1:3000:3000` - o mesmo que anteriormente.
     Executa em modo detached (em segundo plano) e cria um mapeamento de
     porta.
   - `-w //app` - define o "diretório de trabalho" ou o diretório atual a partir
     do qual o comando será executado.
   - `--mount type=bind,src="./",target=/app` - cria um bind mount do diretório
     atual do host para o diretório `/app` dentro do container.
   - `node:24-alpine` - a imagem a ser utilizada.
     Observe que esta é a imagem base para sua aplicação, definida no
     Dockerfile.
   - `sh -c "npm install && npm run dev"` - o comando.
     Você inicia um shell usando `sh` (o Alpine não possui `bash`) e executa
     `npm install` para instalar os pacotes e, em seguida, executa `npm run dev`
     para iniciar o servidor de desenvolvimento.
     Se você verificar o arquivo `package.json`, verá que o script `dev` inicia
     o `nodemon`.

3. Você pode acompanhar os logs usando `docker logs <id-do-contêiner>`.
   Você saberá que está tudo pronto quando vir isto:

   ```console
   $ docker logs -f <id-do-contêiner>
   nodemon -L src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,json
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

   Quando terminar de acompanhar os logs, saia pressionando `Ctrl`+`C`.

{{< /tab >}}
{{< tab name="Docker Desktop" >}}

Certifique-se de que não haja nenhum contêiner `getting-started` em execução no
momento.

Execute a imagem com um bind mount.

1. Selecione a caixa de pesquisa na parte superior do Docker Desktop.
2. Na janela de pesquisa, selecione a aba **Images**.
3. Na caixa de pesquisa, especifique o nome do contêiner: `getting-started`.

   > [!TIP]
   >
   > Use o filtro de pesquisa para filtrar as imagens e exibir apenas **Local
   > images**.

4. Selecione sua imagem e, em seguida, selecione **Run**.
5. Selecione **Optional settings**.
6. Em **Host path**, especifique o caminho para o diretório
   `getting-started-app` em sua máquina host.
7. Em **Container path**, especifique `/app`.
8. Selecione **Run**.

Você pode acompanhar os logs do contêiner usando o Docker Desktop.

1. Selecione **Containers** no Docker Desktop.
2. Selecione o nome do seu contêiner.

Você saberá que está tudo pronto quando vir isto:

```console
nodemon -L src/index.js
[nodemon] 2.0.20
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: js,mjs,json
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```

{{< /tab >}}
{{< /tabs >}}

### Desenvolva sua aplicação com o contêiner de desenvolvimento

Atualize sua aplicação na máquina host e veja as alterações refletidas no
contêiner.

1. No arquivo `src/static/js/app.js`, na linha 109, altere o botão "Add Item"
   para exibir apenas "Add":

   ```diff
   - {submitting ? 'Adding...' : 'Add Item'}
   + {submitting ? 'Adding...' : 'Add'}
   ```

   Salve o arquivo.

2. Atualize a página no seu navegador; você deverá ver a alteração refletida
   quase imediatamente devido ao bind mount.
   O Nodemon detecta a alteração e reinicia o servidor.
   Pode levar alguns segundos para o servidor Node reiniciar.
   Se ocorrer um erro, tente atualizar a página após alguns segundos.

   ![Captura de tela do rótulo atualizado do botão Add](images/updated-add-button.webp)

3. Sinta-se à vontade para fazer outras alterações que desejar.
   Sempre que você faz uma alteração e salva um arquivo, ela é refletida no
   contêiner devido ao bind mount.
   Quando o Nodemon detecta uma alteração, ele reinicia a aplicação dentro do
   contêiner automaticamente.
   Quando terminar, pare o contêiner e construa sua nova imagem usando:

   ```console
   $ docker build -t getting-started .
   ```

## Resumo

Neste ponto, você consegue persistir os dados do banco de dados e visualizar as
alterações na aplicação enquanto desenvolve, sem precisar reconstruir a imagem.

Além de montagens de volume e bind mounts, o Docker também oferece suporte a
outros tipos de montagem e drivers de armazenamento para lidar com casos de uso
mais complexos e especializados.

Informações relacionadas:

- [Referência da CLI do Docker](/reference/cli/docker/)
- [Gerenciamento de dados no Docker](https://docs.docker.com/storage/)

## Próximos passos

Para preparar sua aplicação para produção, você precisa migrar o banco de dados
do SQLite para uma solução que ofereça melhor escalabilidade.
Para simplificar, você continuará usando um banco de dados relacional e
configurará sua aplicação para utilizar o MySQL.
Mas como executar o MySQL?
Como permitir que os contêineres se comuniquem entre si?
Você aprenderá sobre isso na próxima seção.

{{< button text="Aplicações multicontêineres" url="07_multi_container.md" >}}
