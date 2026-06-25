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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/running-containers/persisting-container-data.md
source_revision: eaff43affd5b3bdedbbc65047bc41a58237b4f29
translation_status: ready

title: Persistindo dados do contêiner
weight: 3
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará a importância da persistência de dados no
  Docker.
aliases:
 - /guides/walkthroughs/persist-data/
 - /guides/docker-concepts/running-containers/persisting-container-data/
---

{{< youtube-embed 10_2BjqB_Ls >}}

## Explicação

Quando um contêiner é iniciado, ele usa os arquivos e a configuração fornecidos
pela imagem.
Cada contêiner pode criar, modificar e excluir arquivos sem afetar outros
contêineres.
Quando o contêiner é excluído, essas alterações nos arquivos também são
excluídas.

Embora essa natureza efêmera dos contêineres seja excelente, ela representa um
desafio quando se deseja persistir os dados.
Por exemplo, ao reiniciar um contêiner de banco de dados, você provavelmente não
deseja começar com um banco de dados vazio.
Então, como persistir os arquivos?

### Volumes de contêiner

Volumes são um mecanismo de armazenamento que permite persistir dados além do
ciclo de vida de um contêiner individual.
Pense nisso como um atalho ou link simbólico de dentro do contêiner para fora
dele.

Por exemplo, imagine que você crie um volume chamado `log-data`.

```console
$ docker volume create log-data
```

Ao iniciar um contêiner com o seguinte comando, o volume será montado (ou
anexado) ao contêiner em `/logs`:

```console
$ docker run -d -p 80:80 -v log-data:/logs docker/welcome-to-docker
```

Se o volume `log-data` não existir, o Docker o criará automaticamente.

Quando o contêiner estiver em execução, todos os arquivos gravados na pasta
`/logs` serão salvos nesse volume, fora do contêiner.
Se você excluir o contêiner e iniciar um novo usando o mesmo volume, os arquivos
ainda estarão lá.

> ** Compartilhando arquivos usando volumes**
>
> Você pode anexar o mesmo volume a vários contêineres para compartilhar
> arquivos entre eles.
> Isso pode ser útil em cenários como agregação de logs, pipelines de dados ou
> outras aplicações orientadas a eventos.

### Gerenciando volumes

Os volumes têm seu próprio ciclo de vida, diferente do dos contêineres, e podem
crescer bastante dependendo do tipo de dados e aplicações que você estiver
usando.
Os comandos a seguir serão úteis para gerenciar volumes:

- `docker volume ls` - lista todos os volumes.
- `docker volume rm <nome-ou-id-do-volume>` - remove um volume (funciona apenas
  quando o volume não está associado a nenhum contêiner).
- `docker volume prune` - remove todos os volumes não usados (não associados).

## Experimente

Neste guia, você praticará a criação e o uso de volumes para persistir dados
criados por um contêiner Postgres.
Quando o banco de dados é executado, ele armazena arquivos no diretório
`/var/lib/postgresql/data`.
Ao associar o volume a este diretório, você poderá reiniciar o contêiner várias
vezes, mantendo os dados.

### Use volumes

1. [Baixe e instale](/get-started/get-docker/) o Docker Desktop.

2. Inicie um contêiner usando a
   [imagem do Postgres](https://hub.docker.com/_/postgres) com o seguinte
   comando:

   ```console
   $ docker run --name=db -e POSTGRES_PASSWORD=secret -d -v postgres_data:/var/lib/postgresql/data postgres
   ```

   Isso iniciará o banco de dados em segundo plano, configurará uma senha e
   associará um volume ao diretório onde o PostgreSQL armazenará os arquivos do
   banco de dados.

3. Conecte-se ao banco de dados usando o seguinte comando:

   ```console
   $ docker exec -ti db psql -U postgres
   ```

4. Na linha de comando do PostgreSQL, execute o seguinte comando para criar uma
   tabela no banco de dados e inserir dois registros:

   ```text
   CREATE TABLE tasks (
       id SERIAL PRIMARY KEY,
       description VARCHAR(100)
   );
   INSERT INTO tasks (description) VALUES ('Finish work'), ('Have fun');
   ```

5. Verifique se os dados estão no banco de dados executando o seguinte comando
   na linha de comando do PostgreSQL:

   ```text
   SELECT * FROM tasks;
   ```

   Você deverá obter uma saída semelhante à seguinte:

   ```text
    id | description
   ----+-------------
     1 | Finish work
     2 | Have fun
   (2 rows)
   ```

6. Saia do shell do PostgreSQL executando o seguinte comando:

   ```console
   \q
   ```

7. Pare e remova o contêiner do banco de dados.
   Lembre-se de que, mesmo que o contêiner tenha sido excluído, os dados são
   persistidos no volume `postgres_data`.

   ```console
   $ docker stop db
   $ docker rm db
   ```

8. Inicie um novo contêiner executando o seguinte comando, anexando o mesmo
   volume com os dados persistidos:

   ```console
   $ docker run --name=new-db -d -v postgres_data:/var/lib/postgresql/data postgres
   ```

   Você deve ter notado que a variável de ambiente `POSTGRES_PASSWORD` foi
   omitida.
   Isso ocorre porque essa variável é usada apenas ao inicializar um novo banco
   de dados.

9. Verifique se o banco de dados ainda contém os registros executando o seguinte
   comando:

   ```console
   $ docker exec -ti new-db psql -U postgres -c "SELECT * FROM tasks"
   ```

### Visualize o conteúdo de um volume

O painel do Docker Desktop permite visualizar o conteúdo de qualquer volume,
além de exportar, importar e clonar volumes.

1. Abra o painel do Docker Desktop e navegue até a visualização **Volumes**.
   Nessa visualização, você verá o volume **postgres_data**.

2. Selecione o nome do volume **postgres_data**.

3. A guia **Data** exibe o conteúdo do volume e permite navegar pelos arquivos.
   Clique duas vezes em um arquivo permite visualizar o conteúdo e fazer
   alterações.

4. Clique com o botão direito em qualquer arquivo para salvá-lo ou excluí-lo.

### Remova volumes

Antes de remover um volume, ele não deve estar associado a nenhum contêiner.
Se você ainda não removeu o contêiner anterior, faça isso com o seguinte comando
(a flag `-f` interromperá o contêiner primeiro e, em seguida, o removerá):

```console
$ docker rm -f new-db
```

Existem alguns métodos para remover volumes, incluindo os seguintes:

- Selecione a opção **Delete Volume** em um volume no painel do Docker Desktop.
- Use o comando `docker volume rm`:

  ```console
  $ docker volume rm postgres_data
  ```

- Use o comando `docker volume prune` para remover todos os volumes não usados:

  ```console
  $ docker volume prune
  ```

## Recursos adicionais

Os seguintes recursos ajudarão você a aprender mais sobre volumes:

- [Gerencie dados no Docker](/engine/storage)
- [Volumes](/engine/storage/volumes)
- [Montagens de volume](/engine/containers/run/#volume-mounts)

## Próximos passos

Agora que você aprendeu sobre como persistir dados de contêineres, é hora de
aprender sobre como compartilhar arquivos locais com contêineres.

{{< button text="Compartilhando arquivos locais com contêineres" url="sharing-local-files" >}}
