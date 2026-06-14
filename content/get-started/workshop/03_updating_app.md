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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/03_updating_app.md
source_revision: 9e8032b3547382a2b630770bda187253b426722f
translation_status: ready

title: Atualize a aplicação
weight: 30
linkTitle: "Parte 2: Atualize a aplicação"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop
description: Fazendo alterações na sua aplicação.
aliases:
  - /get-started/03_updating_app/
  - /guides/workshop/03_updating_app/
---

Na [parte 1](./02_our_app.md), você conteinerizou uma aplicação de lista de
tarefas.
Nesta parte, você atualizará a aplicação e a imagem.
Você também aprenderá como parar e remover um contêiner.

## Atualize o código-fonte

Nos passos a seguir, você alterará o "texto vazio" que aparece quando não há
itens na lista de tarefas para "Você ainda não tem itens na lista! Adicione um
acima!"

1. No arquivo `src/static/js/app.js`, atualize a linha 56 para usar o novo texto
   vazio.

   ```diff
   - <p className="text-center">No items yet! Add one above!</p>
   + <p className="text-center">Você ainda não tem itens na lista! Adicione um acima!</p>
   ```

2. Crie sua versão atualizada da imagem usando o comando `docker build`.

   ```console
   $ docker build -t getting-started .
   ```

3. Inicie um novo contêiner usando o código atualizado.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 getting-started
   ```

Você provavelmente viu um erro como este:

```console
docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell
(bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 127.0.0.1:3000 failed: port is already allocated.
```

O erro ocorreu porque você não consegue iniciar o novo contêiner enquanto o
contêiner antigo continua em execução.
O motivo é que o contêiner antigo já está usando a porta 3000 do host e apenas
um processo na máquina (incluindo contêineres) pode escutar em uma porta
específica.
Para corrigir isso, você precisa remover o contêiner antigo.

## Remova o contêiner antigo

Para remover um contêiner, primeiro você precisa pará-lo.
Após parado, você pode removê-lo.
Você pode remover o contêiner antigo usando a CLI ou a interface gráfica do
Docker Desktop.
Escolha a opção com a qual você se sente mais confortável.

{{< tabs >}}
{{< tab name="CLI" >}}

### Remova um contêiner usando a CLI

1. Obtenha o ID do contêiner usando o comando `docker ps`.

   ```console
   $ docker ps
   ```

2. Use o comando `docker stop` para parar o contêiner.
   Substitua `<id-do-container>` pelo ID obtido com `docker ps`.

   ```console
   $ docker stop <id-do-container>
   ```

3. Depois que o contêiner for parado, você pode removê-lo usando o comando
   `docker rm`.

   ```console
   $ docker rm <id-do-container>
   ```

> [!NOTE]
>
> Você pode parar e remover um container com um único comando adicionando a flag
> `--force` ao comando `docker rm`.
> Por exemplo: `docker rm -f <id-do-container>`

{{< /tab >}}
{{< tab name="Docker Desktop" >}}

### Remova um container usando o Docker Desktop

1. Abra o Docker Desktop na visualização **Containers**.
2. Selecione o ícone da lixeira na coluna **Actions** do container que você
   deseja excluir.
3. Na caixa de diálogo de confirmação, selecione **Delete forever**.

{{< /tab >}}
{{< /tabs >}}

### Inicie o container da aplicação atualizada

1. Agora, inicie sua aplicação atualizada usando o comando `docker run`.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 getting-started
   ```

2. Atualize seu navegador em [http://localhost:3000](http://localhost:3000) e
   você deverá ver o texto de ajuda atualizado.

## Resumo

Nesta seção, você aprendeu como atualizar e reconstruir uma imagem, bem como
como parar e remover um contêiner.

Informações relacionadas:
- [Referência da CLI do Docker](/reference/cli/docker/)

## Próximos passos

Em seguida, você aprenderá como compartilhar imagens com outras pessoas.

{{< button text="Compartilhe a aplicação" url="04_sharing_app.md" >}}
