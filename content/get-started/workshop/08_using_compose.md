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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/08_using_compose.md
source_revision: 70d0c07c698bfc18bbe08b44c85f9dc859bd7718
translation_status: ready

title: Use o Docker Compose
weight: 80
linkTitle: "Parte 7: use o Docker Compose"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop
description: Usando o Docker Compose para aplicações multicontêineres
aliases:
  - /get-started/08_using_compose/
  - /guides/workshop/08_using_compose/
---

O [Docker Compose](/manuals/compose/_index.md) é uma ferramenta que ajuda você a
definir e compartilhar aplicações multicontêineres.
Com o Compose, você pode criar um arquivo YAML para definir os serviços e, com
um único comando, iniciar ou encerrar toda a aplicação.

A grande vantagem de usar o Compose é que você pode definir a pilha da sua
aplicação em um arquivo, mantê-lo na raiz do repositório do projeto (passando
assim a contar com controle de versão) e permitir facilmente que outras pessoas
contribuam com o seu projeto.
Basta que a pessoa clone o repositório e inicie a aplicação usando o Compose.
Na verdade, é possível encontrar muitos projetos no GitHub ou GitLab que adotam
exatamente essa prática atualmente.

## Crie o arquivo Compose

No diretório `getting-started-app`, crie um arquivo chamado `compose.yaml`.

```text
├── getting-started-app/
│ ├── Dockerfile
│ ├── compose.yaml
│ ├── node_modules/
│ ├── package.json
│ ├── package-lock.json
│ ├── spec/
│ └── src/
```

## Defina o serviço da aplicação

Na [parte 6](./07_multi_container.md), você usou o seguinte comando para iniciar
o serviço da aplicação.

```console
$ docker run -dp 127.0.0.1:3000:3000 \
  -w /app -v ".:/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:24-alpine \
  sh -c "npm install && npm run dev"
```

Agora, você definirá esse serviço no arquivo `compose.yaml`.

1. Abra o `compose.yaml` em um editor de texto ou de código e comece definindo o
   nome e a imagem do primeiro serviço (ou contêiner) que você deseja executar
   como parte da sua aplicação.
   O nome se tornará automaticamente um alias de rede, o que será útil ao
   definir o seu serviço MySQL.

   ```yaml
   services:
     app:
       image: node:24-alpine
   ```

2. Normalmente, você verá o `command` próximo à definição da `image`, embora não
   haja exigência quanto à ordem.
   Adicione o `command` ao seu arquivo `compose.yaml`.

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
   ```

3. Agora, migre a parte `-p 127.0.0.1:3000:3000` do comando definindo as `ports`
   para o serviço.

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
   ```

4. Em seguida, migre tanto o diretório de trabalho (`-w /app`) quanto o
   mapeamento de volume (`-v ".:/app"`) usando as definições `working_dir` e
   `volumes`.

   Uma vantagem das definições de volume no Docker Compose é que você pode usar
   caminhos relativos a partir do diretório atual.

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
       working_dir: /app
       volumes:
         - ./:/app
   ```

5. Por fim, você precisa migrar as definições de variáveis de ambiente usando a
   chave `environment`.

   ```yaml
   services:
     app:
       image: node:24-alpine
       command: sh -c "npm install && npm run dev"
       ports:
         - 127.0.0.1:3000:3000
       working_dir: /app
       volumes:
         - ./:/app
       environment:
         MYSQL_HOST: mysql
         MYSQL_USER: root
         MYSQL_PASSWORD: secret
         MYSQL_DB: todos
   ```

### Defina o serviço MySQL

Agora, é hora de definir o serviço MySQL.
O comando que você usou para esse contêiner foi o seguinte:

```console
$ docker run -d \
  --network todo-app --network-alias mysql \
  -v todo-mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=todos \
  mysql:8.0
```

1. Primeiro, defina o novo serviço e nomeie-o como `mysql` para que ele obtenha
   automaticamente o alias de rede.
   Especifique também a imagem a ser usada.

   ```yaml

   services:
     app:
       # The app service definition
     mysql:
       image: mysql:8.0
   ```

2. Em seguida, defina o mapeamento de volume.
   Quando você executou o contêiner com `docker run`, o Docker criou o volume
   nomeado automaticamente.
   No entanto, isso não acontece ao executar com o Compose.
   Você precisa definir o volume na seção de nível superior `volumes:` e, então,
   especificar o ponto de montagem na configuração do serviço.
   Ao fornecer apenas o nome do volume, as opções padrão são usadas.

   ```yaml
   services:
     app:
       # A definição do serviço da aplicação
     mysql:
       image: mysql:8.0
       volumes:
         - todo-mysql-data:/var/lib/mysql

   volumes:
     todo-mysql-data:
   ```

3. Por fim, você precisa especificar as variáveis de ambiente.

   ```yaml
   services:
     app:
       # A definição do serviço da aplicação
     mysql:
       image: mysql:8.0
       volumes:
         - todo-mysql-data:/var/lib/mysql
       environment:
         MYSQL_ROOT_PASSWORD: secret
         MYSQL_DATABASE: todos

   volumes:
     todo-mysql-data:
   ```

Neste ponto, o seu arquivo `compose.yaml` completo deve ficar assim:

```yaml
services:
  app:
    image: node:24-alpine
    command: sh -c "npm install && npm run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

## Execute a pilha da aplicação

Agora que você tem o arquivo `compose.yaml`, pode iniciar a sua aplicação.

1. Certifique-se de que não haja outras cópias dos contêineres em execução.
   Use `docker ps` para listar os contêineres e `docker rm -f <ids>` para
   removê-los.

2. Inicie a pilha da aplicação usando o comando `docker compose up`.
   Adicione a flag `-d` para executar tudo em segundo plano.

   ```console
   $ docker compose up -d
   ```

   Ao executar o comando anterior, você deverá ver uma saída como a seguinte:

   ```plaintext
   Creating network "app_default" with the default driver
   Creating volume "app_todo-mysql-data" with default driver
   Creating app_app_1   ... done
   Creating app_mysql_1 ... done
   ```

   Você notará que o Docker Compose criou o volume, bem como uma rede.
   Por padrão, o Docker Compose cria automaticamente uma rede específica para a
   pilha da aplicação (e é por isso que você não definiu uma no arquivo
   Compose).

3. Verifique os logs usando o comando `docker compose logs -f`.
   Você verá os logs de cada um dos serviços intercalados em um único fluxo.
   Isso é extremamente útil quando você deseja monitorar problemas relacionados
   a questões de tempo (timing).
   A flag `-f` acompanha o log, fornecendo assim uma saída em tempo real à
   medida que ele é gerado.

   Se você já tiver executado o comando, verá uma saída semelhante a esta:

   ```plaintext
   mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
   mysql_1  | Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
   app_1    | Connected to mysql db at host mysql
   app_1    | Listening on port 3000
   ```

   O nome do serviço é exibido no início da linha (geralmente colorido) para
   distinguir as mensagens.
   Se você quiser visualizar os logs de um serviço específico, pode adicionar o
   nome do serviço ao final do comando de logs (por exemplo,
   `docker compose logs -f app`).

4. Neste ponto, você deve conseguir abrir sua aplicação no navegador em
   [http://localhost:3000](http://localhost:3000) e vê-la em execução.

## Veja a pilha da aplicação no Dashboard do Docker Desktop

Se você olhar para o Dashboard do Docker Desktop, verá que existe um grupo
chamado **getting-started-app**.
Esse é o nome do projeto definido no Docker Compose e usado para agrupar os
contêineres.
Por padrão, o nome do projeto é simplesmente o nome do diretório onde o arquivo
`compose.yaml` está localizado.

Se você expandir a stack, verá os dois contêineres que definiu no arquivo
Compose.
Os nomes também são um pouco mais descritivos, pois seguem o padrão `<nome-do-serviço>-<número-da-réplica>`.
Assim, é muito fácil identificar rapidamente qual contêiner é a sua aplicação e
qual é o banco de dados MySQL.

## Remova tudo

Quando estiver pronto para remover tudo, basta executar `docker compose down`
ou clicar no ícone de lixeira no Dashboard do Docker Desktop referente a toda a
aplicação.
Os contêineres serão interrompidos e a rede será removida.

> [!WARNING]
>
> Por padrão, volumes nomeados no seu arquivo compose não são removidos quando
> você executa `docker compose down`.
> Se quiser remover os volumes, você precisa adicionar a flag `--volumes`.
>
> O Dashboard do Docker Desktop não remove volumes quando você exclui a pilha da
> aplicação.

## Resumo

Nesta seção, você aprendeu sobre o Docker Compose e como ele ajuda a simplificar
a maneira como você define e compartilha aplicações de múltiplos serviços.

Informações relacionadas:

- [Visão geral do Compose](/manuals/compose/_index.md)
- [Referência do arquivo Compose](/reference/compose-file/_index.md)
- [Referência da CLI do Compose](/reference/cli/docker/compose/)

## Próximos passos

A seguir, você conhecerá algumas práticas recomendadas para melhorar seu
Dockerfile.

{{< button text="Boas práticas para a construção de imagens" url="09_image_best.md" >}}
