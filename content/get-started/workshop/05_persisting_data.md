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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/05_persisting_data.md
source_revision: 01416dc32ac571844519d118a64601a10a268d2b
translation_status: ready

title: Persista o banco de dados
weight: 50
linkTitle: "Parte 4: persista o banco de dados"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop
description: Tornando seu banco de dados persistente em sua aplicação.
aliases:
  - /get-started/05_persisting_data/
  - /guides/workshop/05_persisting_data/
---

Caso você não tenha percebido, sua lista de tarefas está vazia toda vez que você
inicia o contêiner.
Por quê?
Nesta parte, você vai entender como o contêiner funciona.

## O sistema de arquivos do contêiner

Quando um contêiner é executado, ele usa as várias camadas de uma imagem para
seu sistema de arquivos.
Cada contêiner também recebe seu próprio "espaço temporário" para criar,
atualizar e remover arquivos.
Quaisquer alterações feitas não serão visíveis em outro contêiner, mesmo que
ambos estejam usando a mesma imagem.

### Veja isso na prática

Para ver isso em ação, você iniciará dois contêineres.
Em um contêiner, você criará um arquivo.
No outro contêiner, você verificará se o mesmo arquivo existe.

1. Inicie um contêiner Alpine e crie um novo arquivo nele.

   ```console
   $ docker run --rm alpine touch greeting.txt
   ```

   > [!TIP]
   >
   > Quaisquer comandos que você especificar após o nome da imagem (neste caso,
   > `alpine`) são executados dentro do contêiner.
   > Neste caso, o comando `touch greeting.txt` cria um arquivo chamado
   > `greeting.txt` no sistema de arquivos do contêiner.

2. Execute um novo contêiner Alpine e use o comando `stat` para verificar se o
   arquivo existe.

   ```console
   $ docker run --rm alpine stat greeting.txt
   ```

   Você deverá ver uma saída semelhante à seguinte, que indica que o arquivo não
   existe no novo contêiner.

   ```console
   stat: can't stat 'greeting.txt': No such file or directory
   ```

O arquivo `greeting.txt` criado pelo primeiro contêiner não existia no segundo
contêiner.
Isso ocorre porque a "camada superior" gravável de cada contêiner é isolada.
Embora ambos os contêineres compartilhem as mesmas camadas subjacentes que
compõem a imagem base, a camada gravável é exclusiva de cada contêiner.

## Volumes de contêiner

No experimento anterior, você viu que cada contêiner inicia a partir da
definição da imagem sempre que é iniciado.
Embora os contêineres possam criar, atualizar e excluir arquivos, essas
alterações são perdidas quando você remove o contêiner, e o Docker isola todas
as alterações feitas nesse contêiner.
Com volumes, você pode mudar tudo isso.

Os [Volumes](/manuals/engine/storage/volumes.md) permitem conectar caminhos
específicos do sistema de arquivos do contêiner de volta à máquina host.
Se você montar um diretório no contêiner, as alterações nesse diretório também
serão vistas na máquina host.
Se você montar o mesmo diretório em reinicializações do contêiner, verá os
mesmos arquivos.

Existem dois tipos principais de volumes.
Você eventualmente usará ambos, mas começará com as montagens de volume.

## Persista os dados da lista de tarefas

Por padrão, a aplicação de tarefas armazena seus dados em um banco de dados
SQLite em `/etc/todos/todo.db` no sistema de arquivos do contêiner.
Se você não tiver familiaridade com o SQLite, não se preocupe!
É simplesmente um banco de dados relacional que armazena todos os dados em um
único arquivo.
Embora isso não seja o ideal para aplicações de grande escala, funciona para
pequenas demonstrações.
Você aprenderá como mudar para um mecanismo de banco de dados diferente mais
tarde.

Como o banco de dados é um único arquivo, se você puder persistir esse arquivo
no host e torná-lo disponível para o próximo contêiner, ele poderá continuar de
onde o anterior parou.
Criando um volume e anexando-o (geralmente chamado de "montagem") ao diretório
onde você armazenou os dados, você pode persistir os dados.
À medida que seu contêiner grava no arquivo `todo.db`, ele persistirá os dados
no volume do host.

Como mencionado, você usará uma montagem de volume.
Pense em um volume montado como um recipiente opaco de dados.
O Docker gerencia totalmente o volume, incluindo o local de armazenamento no
disco.
Você só precisa se lembrar do nome do volume.

### Crie um volume e iniciar o contêiner

Você pode criar o volume e iniciar o contêiner usando a CLI ou a interface
gráfica do Docker Desktop.

{{< tabs >}}
{{< tab name="CLI" >}}

1. Crie um volume usando o comando `docker volume create`.

   ```console
   $ docker volume create todo-db
   ```

2. Pare e remova o contêiner da aplicação de tarefas novamente com
   `docker rm -f <id>`, pois ele continua em execução sem usar o volume
   persistente.

3. Inicie o contêiner da aplicação de tarefas, mas adicione a opção `--mount`
   para especificar uma montagem de volume.
   Use o volume chamado `todo-db` e monte-o em `/etc/todos` no contêiner, que
   captura todos os arquivos criados nesse caminho.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
   ```

   > [!NOTE]
   >
   > Se você estiver usando o Git Bash, deverá usar uma sintaxe diferente para
   > este comando.
   >
   > ```console
   > $ docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started
   > ```
   >
   > Para obter mais detalhes sobre as diferenças de sintaxe do Git Bash,
   > consulte
   > [Trabalhando com Git Bash](/desktop/troubleshoot-and-support/troubleshoot/topics/#docker-commands-failing-in-git-bash).

{{< /tab >}}
{{< tab name="Docker Desktop" >}}

Para criar um volume:

1. Selecione **Volumes** no Docker Desktop.
2. Em **Volumes**, selecione **Create**.
3. Especifique `todo-db` como o nome do volume e selecione **Create**.

Para parar e remover o contêiner da aplicação:

1. Selecione **Containers** no Docker Desktop.
2. Selecione **Delete** na coluna **Actions** do contêiner.

Para iniciar o contêiner da aplicação de tarefas com o volume montado:

1. Selecione a caixa de pesquisa na parte superior do Docker Desktop.
2. Na janela de pesquisa, selecione a guia **Images**.
3. Na caixa de pesquisa, especifique o nome da imagem, `getting-started`.

   > [!TIP]
   >
   > Use o filtro de pesquisa para filtrar as imagens e exibir apenas
   > **Imagens locais**.

4. Selecione sua imagem e selecione **Run**.
5. Selecione **Optional settings**.
6. Em **Host port**, especifique a porta, por exemplo, `3000`.
7. Em **Host path**, especifique o nome do volume, `todo-db`.
8. Em **Container path**, especifique `/etc/todos`.
9. Selecione **Run**.

{{< /tab >}}
{{< /tabs >}}

### Verifique se os dados persistem

1. Assim que o contêiner iniciar, abra a aplicação e adicione alguns itens à sua
   lista de tarefas.

   ![Itens adicionados à lista de tarefas](images/items-added.webp)

2. Pare e remova o contêiner da aplicação de tarefas.
   Use o Docker Desktop ou `docker ps` para obter o ID e, em seguida,
   `docker rm -f <id>` para removê-lo.

3. Inicie um novo contêiner seguindo os passos anteriores.

4. Abra a aplicação.
   Você ainda deverá ver seus itens na sua lista.

5. Quando terminar de verificar sua lista, remova o contêiner.

Agora você aprendeu como persistir dados.

## Explore o volume

Muitas pessoas perguntam frequentemente: "Onde o Docker armazena meus dados
quando uso um volume?"
Se você quiser saber, você pode usar o comando `docker volume inspect`.

```console
$ docker volume inspect todo-db
```

Você deverá ver uma saída semelhante à seguinte:

```console
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

O `Mountpoint` é a localização real dos dados no disco.
Observe que, na maioria das máquinas, você precisará de acesso root para acessar
este diretório a partir do host.

## Resumo

Nesta seção, você aprendeu como persistir dados de contêineres.

Informações relacionadas:

 - [Referência da CLI do docker](/reference/cli/docker/)
 - [Volumes](/manuals/engine/storage/volumes.md)

## Próximos passos

A seguir, você aprenderá como desenvolver sua aplicação de forma mais eficiente
usando bind mounts.

{{< button text="Use bind mounts" url="06_bind_mounts.md" >}}
