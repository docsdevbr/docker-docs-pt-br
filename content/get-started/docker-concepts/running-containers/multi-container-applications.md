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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/running-containers/multi-container-applications.md
source_revision: eaff43affd5b3bdedbbc65047bc41a58237b4f29
translation_status: ready

title: Aplicações multicontêineres
weight: 5
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará a importância de uma aplicação
  multicontêineres e como ela difere de uma aplicação com um único contêiner.
aliases:
  - /guides/docker-concepts/running-containers/multi-container-applications/
---

{{< youtube-embed 1jUwR6F9hvM >}}

## Explicação

Iniciar uma aplicação em um único contêiner é fácil.
Por exemplo, um script Python que executa uma tarefa específica de processamento
de dados roda em um contêiner com todas as suas dependências.
Da mesma forma, uma aplicação Node.js que hospeda um site estático com um
pequeno endpoint de API pode ser efetivamente conteinerizada com todas as suas
bibliotecas e dependências necessárias.
No entanto, à medida que as aplicações crescem, gerenciá-las como contêineres
individuais fica mais difícil.

Imagine que o script Python de processamento de dados precise se conectar a um
banco de dados.
De repente, você está gerenciando não apenas o script, mas também um servidor de
banco de dados dentro do mesmo contêiner.
Se o script exigir logins de pessoas usuárias, você precisará de um mecanismo de
autenticação, aumentando ainda mais o tamanho do contêiner.

Uma boa prática para contêineres é que cada contêiner deve fazer uma coisa e
fazê-la bem.
Embora existam exceções a essa regra, evite a tendência de ter um contêiner
executando várias coisas.

Agora você pode perguntar: "Preciso executar esses contêineres separadamente? Se
eu executá-los separadamente, como devo conectá-los?"

Embora o `docker run` seja uma ferramenta conveniente para iniciar contêineres,
torna-se difícil gerenciar uma pilha de aplicações em crescimento com ele.
Eis o porquê:

- Imagine executar vários comandos `docker run` (frontend, backend e banco de
  dados) com configurações diferentes para ambientes de desenvolvimento, teste e
  produção.
  É propenso a erros e demorado.
- As aplicações geralmente dependem umas das outras.
  Iniciar contêineres manualmente em uma ordem específica e gerenciar conexões
  de rede fica mais difícil à medida que a pilha se expande.
- Cada aplicação precisa de seu próprio comando `docker run`, o que dificulta o
  escalonamento de serviços individuais.
  Escalar a aplicação inteira significa potencialmente desperdiçar recursos em
  componentes que não precisam de um aumento de desempenho.
- Persistir dados para cada aplicação requer montagens de volume separadas ou
  configurações em cada comando `docker run`.
  Isso cria uma abordagem de gerenciamento de dados dispersa.
- Definir variáveis de ambiente para cada aplicação por meio de comandos
  `docker run` separados é tedioso e propenso a erros.

É aí que o Docker Compose entra em ação.

O Docker Compose define toda a sua aplicação multicontêineres em um único
arquivo YAML chamado `compose.yml`.
Este arquivo especifica as configurações de todos os seus contêineres, suas
dependências, variáveis de ambiente e até mesmo volumes e redes.
Com o Docker Compose:

- Você não precisa executar vários comandos `docker run`.
  Basta definir toda a sua aplicação multicontêineres em um único arquivo YAML.
  Isso centraliza a configuração e simplifica o gerenciamento.
- Você pode executar contêineres em uma ordem específica e gerenciar conexões de
  rede facilmente.
- Você pode aumentar ou diminuir a escala de serviços individuais dentro da
  configuração multicontêineres com facilidade.
  Isso permite uma alocação eficiente com base nas necessidades em tempo real.
- Você pode implementar volumes persistentes com facilidade.
- É fácil definir variáveis de ambiente uma única vez no seu arquivo Docker
  Compose.

Ao usar o Docker Compose para executar configurações multicontêineres, você pode
criar aplicações complexas com modularidade, escalabilidade e consistência como
pilares fundamentais.

## Experimente

Neste guia prático, você verá como criar e executar uma aplicação web de
contador baseada em Node.js, um proxy reverso Nginx e um banco de dados Redis
usando os comandos `docker run`.
Você também verá como simplificar todo o processo de implantação usando o Docker
Compose.

### Configuração

1. Baixe a aplicação de exemplo.
   Se você tiver o Git instalado, poderá clonar o repositório da aplicação de
   exemplo.
   Caso contrário, você pode baixar a aplicação de exemplo.
   Escolha uma das seguintes opções.

   {{< tabs >}}
   {{< tab name="Clone com o git" >}}

   Use o seguinte comando em um terminal para clonar o repositório da aplicação
   de exemplo.

   ```console
   $ git clone https://github.com/dockersamples/nginx-node-redis
   ```

   Navegue até o diretório `nginx-node-redis`:

   ```console
   $ cd nginx-node-redis
   ```

   Dentro deste diretório, você encontrará dois subdiretórios: `nginx` e `web`.

   {{< /tab >}}
   {{< tab name="Baixe" >}}

   Baixe o código-fonte e extraia-o.

   {{< button url="https://github.com/dockersamples/nginx-node-redis/archive/refs/heads/main.zip" text="Baixe o código-fonte" >}}

   Navegue até o diretório `nginx-node-redis-main`:

   ```console
   $ cd nginx-node-redis-main
   ```

   Dentro deste diretório, você encontrará dois subdiretórios: `nginx` e `web`.

   {{< /tab >}}
   {{< /tabs >}}

2. [Baixe e instale](/get-started/get-docker.md) o Docker Desktop.

### Construa as imagens

1. Navegue até o diretório `nginx` para criar a imagem executando o seguinte
   comando:

   ```console
   $ docker build -t nginx .
   ```

2. Navegue até o diretório `web` e execute o seguinte comando para criar a
   primeira imagem web:

   ```console
   $ docker build -t web .
   ```

### Execute os contêineres

1. Antes de executar uma aplicação multicontêineres, você precisa criar uma rede
   para que todos eles se comuniquem.
   Você pode fazer isso usando o comando `docker network create`:

   ```console
   $ docker network create sample-app
   ```

2. Inicie o contêiner Redis executando o seguinte comando, que o conectará à
   rede criada anteriormente e criará um alias de rede (útil para pesquisas de
   DNS):

   ```console
   $ docker run -d  --name redis --network sample-app --network-alias redis redis
   ```

3. Inicie o primeiro contêiner web executando o seguinte comando:

   ```console
   $ docker run -d --name web1 -h web1 --network sample-app --network-alias web1 web
   ```

4. Inicie o segundo contêiner web executando o seguinte comando:

   ```console
   $ docker run -d --name web2 -h web2 --network sample-app --network-alias web2 web
   ```

5. Inicie o contêiner Nginx executando o seguinte comando:

   ```console
   $ docker run -d --name nginx --network sample-app  -p 80:80 nginx
   ```

   > [!NOTE]
   >
   > O Nginx é normalmente usado como um proxy reverso para aplicações web,
   > encaminhando o tráfego para servidores de backend.
   > Neste caso, ele encaminha para os contêineres de backend Node.js (web1 ou
   > web2).

6. Verifique se os contêineres estão em execução executando o seguinte comando:

   ```console
   $ docker ps
   ```

   Você verá uma saída semelhante à seguinte:

   ```text
   CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                NAMES
   2cf7c484c144   nginx     "/docker-entrypoint.…"   9 seconds ago        Up 8 seconds        0.0.0.0:80->80/tcp   nginx
   7a070c9ffeaa   web       "docker-entrypoint.s…"   19 seconds ago       Up 18 seconds                            web2
   6dc6d4e60aaf   web       "docker-entrypoint.s…"   34 seconds ago       Up 33 seconds                            web1
   008e0ecf4f36   redis     "docker-entrypoint.s…"   About a minute ago   Up About a minute   6379/tcp             redis
   ```

7. Se você observar o painel do Docker Desktop, poderá visualizar os contêineres
   e explorar suas configurações em detalhes.

  ![Uma captura de tela do painel do Docker Desktop mostrando aplicações multicontêineres](images/multi-container-apps.webp?w=5000&border=true)

8. Com tudo configurado e funcionando, você pode abrir
   [http://localhost](http://localhost) no seu navegador para visualizar o site.
   Atualize a página algumas vezes para ver o host que está processando a
   requisição e o número total de requisições:

   ```console
   web2: Number of visits is: 9
   web1: Number of visits is: 10
   web2: Number of visits is: 11
   web1: Number of visits is: 12
   ```

   > [!NOTE]
   >
   > Você deve ter notado que o Nginx, atuando como um proxy reverso,
   > distribui provavelmente as requisições recebidas em um esquema de rodízio
   > entre os dois contêineres de backend.
   > Isso significa que cada requisição pode ser direcionada para um contêiner
   > diferente (web1 e web2) de forma rotativa.
   > A saída mostra incrementos consecutivos para os contêineres web1 e web2, e
   > o valor real do contador armazenado no Redis é atualizado somente após a
   > resposta ser enviada de volta ao cliente.

9. Você pode usar o painel do Docker Desktop para remover os contêineres
   selecionando-os e clicando no botão **Delete**.

    ![Uma captura de tela do painel do Docker Desktop mostrando como excluir aplicações multicontêineres](images/delete-multi-container-apps.webp?border=true)

## Simplifique a implantação usando o Docker Compose

O Docker Compose oferece uma abordagem estruturada e simplificada para gerenciar
implantações multicontêineres.
Como mencionado anteriormente, com o Docker Compose, você não precisa executar
vários comandos `docker run`.
Tudo o que você precisa fazer é definir toda a sua aplicação multicontêineres em
um único arquivo YAML chamado `compose.yml`.
Vamos ver como funciona.

Navegue até a raiz do diretório do projeto.
Dentro deste diretório, você encontrará um arquivo chamado `compose.yml`.
É neste arquivo YAML que toda a mágica acontece.
Ele define todos os serviços que compõem sua aplicação, juntamente com suas
configurações.
Cada serviço especifica sua imagem, portas, volumes, redes e quaisquer outras
configurações necessárias para sua funcionalidade.

1. Use o comando `docker compose up` para iniciar a aplicação:

   ```console
   $ docker compose up -d --build
   ```

   Ao executar este comando, você deverá ver uma saída semelhante à seguinte:

   ```console
   Running 5/5
   ✔ Network nginx-nodejs-redis_default    Created                                                0.0s
   ✔ Container nginx-nodejs-redis-web1-1   Started                                                0.1s
   ✔ Container nginx-nodejs-redis-redis-1  Started                                                0.1s
   ✔ Container nginx-nodejs-redis-web2-1   Started                                                0.1s
   ✔ Container nginx-nodejs-redis-nginx-1  Started
   ```

2. Se você observar o painel do Docker Desktop, poderá visualizar os contêineres
   e explorar suas configurações em detalhes.

   ![Uma captura de tela do painel do Docker Desktop mostrando os contêineres da pilha da aplicação implantada usando o Docker Compose](images/list-containers.webp?border=true)

3. Como alternativa, você pode usar o painel do Docker Desktop para remover os
   contêineres selecionando a pilha da aplicação e clicando no botão **Delete**.

   ![Uma captura de tela do painel do Docker Desktop mostrando como remover os contêineres implantados usando o Docker Compose](images/delete-containers.webp?border=true)

Neste guia, você aprendeu como é fácil usar o Docker Compose para iniciar e
parar uma aplicação multicontêineres, em comparação com o comando `docker run`,
que é propenso a erros e difícil de gerenciar.

## Recursos adicionais

- [Referência da CLI `docker container run`](reference/cli/docker/container/run/)
- [O que é o Docker Compose?](/get-started/docker-concepts/the-basics/what-is-docker-compose/)
