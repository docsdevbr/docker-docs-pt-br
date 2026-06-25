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

source_url: https://github.com/docker/docs/blob/main/content/get-started/workshop/07_multi_container.md
source_revision: ca0e85cf12f7a517cb633f75e414c2a4169dd5dd
translation_status: ready

title: Aplicações multicontêineres
weight: 70
linkTitle: "Parte 6: aplicações multicontêineres"
keywords: >-
  primeiros passos, configuração, orientação, início rápido, introdução,
  conceitos, contêineres, docker desktop
description: Usando mais de um contêiner em sua aplicação.
aliases:
  - /get-started/07_multi_container/
  - /guides/workshop/07_multi_container/
---

Até agora, você trabalhou com aplicações baseadas em um único contêiner.
No entanto, agora você adicionará o MySQL à pilha da aplicação.
Uma pergunta comum surge nesse momento: "Onde o MySQL deve ser executado?
Devo instalá-lo no mesmo contêiner ou executá-lo separadamente?"
De modo geral, cada contêiner deve realizar uma única tarefa e fazê-la bem.
Abaixo, listo alguns motivos para executar o contêiner separadamente:

- É muito provável que você precise escalar APIs e front-ends de maneira
  diferente dos bancos de dados.
- Contêineres separados permitem versionar e atualizar versões de forma isolada.
- Embora você possa usar um contêiner para o banco de dados em ambiente local,
  talvez prefira usar um serviço gerenciado para o banco de dados em produção.
  Nesse caso, você não vai querer incluir o mecanismo do banco de dados no
  pacote da sua aplicação.
- A execução de múltiplos processos exigiria um gerenciador de processos (já que
  o contêiner inicia apenas um processo), o que adiciona complexidade à
  inicialização e ao encerramento do contêiner.

Existem ainda outros motivos.
Portanto, conforme ilustrado no diagrama a seguir, o ideal é executar sua
aplicação usando múltiplos contêineres.

![Aplicação de tarefas conectada ao contêiner MySQL](images/multi-container.webp?w=350h=250)

## Rede de contêineres

Lembre-se de que os contêineres, por padrão, são executados de forma isolada e
não têm conhecimento de outros processos ou contêineres na mesma máquina.
Então, como permitir que um contêiner se comunique com outro?
A resposta é rede.
Se você colocar os dois contêineres na mesma rede, eles poderão se comunicar.

## Inicie o MySQL

Existem duas maneiras de colocar um contêiner em uma rede:
- Definir a rede ao iniciar o contêiner.
- Conectar um contêiner já em execução a uma rede.

Nos passos a seguir, você criará a rede primeiro e, em seguida, conectará o
contêiner MySQL durante a inicialização.

1. Crie a rede.

   ```console
   $ docker network create todo-app
   ```

2. Inicie um contêiner MySQL e conecte-o à rede.
   Você também definirá algumas variáveis de ambiente que o banco de dados
   usará para sua inicialização.
   Para saber mais sobre as variáveis de ambiente do MySQL, consulte a seção
   "Environment Variables" na
   [página do MySQL no Docker Hub](https://hub.docker.com/_/mysql/).

   {{< tabs >}}
   {{< tab name="Mac / Linux / Git Bash" >}}

   ```console
   $ docker run -d \
       --network todo-app --network-alias mysql \
       -v todo-mysql-data:/var/lib/mysql \
       -e MYSQL_ROOT_PASSWORD=secret \
       -e MYSQL_DATABASE=todos \
       mysql:8.0
   ```

   {{< /tab >}}
   {{< tab name="PowerShell" >}}

   ```powershell
   $ docker run -d `
       --network todo-app --network-alias mysql `
       -v todo-mysql-data:/var/lib/mysql `
       -e MYSQL_ROOT_PASSWORD=secret `
       -e MYSQL_DATABASE=todos `
       mysql:8.0
   ```

   {{< /tab >}}
   {{< tab name="Prompt de Comando" >}}

   ```console
   $ docker run -d ^
       --network todo-app --network-alias mysql ^
       -v todo-mysql-data:/var/lib/mysql ^
       -e MYSQL_ROOT_PASSWORD=secret ^
       -e MYSQL_DATABASE=todos ^
       mysql:8.0
   ```

   {{< /tab >}}
   {{< /tabs >}}

   No comando anterior, você pode ver a flag `--network-alias`.
   Em uma seção posterior, você aprenderá mais sobre essa flag.

   > [!TIP]
   >
   > Você notará um volume chamado `todo-mysql-data` no comando acima, montado
   > em `/var/lib/mysql`, que é onde o MySQL armazena seus dados.
   > No entanto, você nunca executou um comando `docker volume create`.
   > O Docker reconhece que você deseja usar um volume nomeado e cria um
   > automaticamente para você.

3. Para confirmar que o banco de dados está em execução, conecte-se a ele e
   verifique se a conexão é estabelecida.

   ```console
   $ docker exec -it <id-do-contêiner-do-mysql> mysql -u root -p
   ```

   Quando a solicitação de senha aparecer, digite `secret`.
   No shell do MySQL, liste os bancos de dados e verifique se você vê o banco de
   dados `todos`.

   ```console
   mysql> SHOW DATABASES;
   ```

   Você deve ver uma saída semelhante a esta:

   ```plaintext
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | performance_schema |
   | sys                |
   | todos              |
   +--------------------+
   5 rows in set (0.00 sec)
   ```

4. Saia do shell do MySQL para retornar ao shell da sua máquina.

   ```console
   mysql> exit
   ```

   Agora você tem um banco de dados chamado `todos` e ele está pronto para uso.

## Conecte ao MySQL

Agora que você sabe que o MySQL está em execução, pode usá-lo.
Mas como fazer isso?
Se você executar outro contêiner na mesma rede, como localizar esse contêiner?
Lembre-se de que cada contêiner possui seu próprio endereço IP.

Para responder às perguntas acima e compreender melhor a rede de contêiners,
você usará o contêiner
[nicolaka/netshoot](https://github.com/nicolaka/netshoot), que já vem com
diversas ferramentas úteis para diagnóstico ou depuração de problemas de rede.

1. Inicie um novo contêiner usando a imagem `nicolaka/netshoot`.
   Certifique-se de conectá-lo à mesma rede.

   ```console
   $ docker run -it --network todo-app nicolaka/netshoot
   ```

2. Dentro do contêiner, você usará o comando `dig`, uma ferramenta DNS útil.
   Você buscará o endereço IP correspondente ao hostname `mysql`.

   ```console
   $ dig mysql
   ```

   Você deve obter uma saída como a seguinte.

   ```text
   ; <<>> DiG 9.18.8 <<>> mysql
   ;; global options: +cmd
   ;; Got answer:
   ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32162
   ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

   ;; QUESTION SECTION:
   ;mysql.				IN	A

   ;; ANSWER SECTION:
   mysql.			600	IN	A	172.23.0.2

   ;; Query time: 0 msec
   ;; SERVER: 127.0.0.11#53(127.0.0.11)
   ;; WHEN: Tue Oct 01 23:47:24 UTC 2019
   ;; MSG SIZE  rcvd: 44
   ```

   Na seção "ANSWER SECTION", você verá um registro do tipo `A` para `mysql` que
   é resolvido para `172.23.0.2` (o seu endereço IP provavelmente terá um valor
   diferente).
   Embora `mysql` não seja normalmente um nome de host válido, o Docker
   conseguiu resolvê-lo para o endereço IP do contêiner que possuía esse alias
   de rede.
   Lembre-se de que você usou o parâmetro `--network-alias` anteriormente.

   Isso significa que sua aplicação só precisa se conectar a um host chamado
   `mysql` para se comunicar com o banco de dados.

## Execute sua aplicação com MySQL

A aplicação de lista de tarefas permite definir algumas variáveis de ambiente
para especificar as configurações de conexão com o MySQL.
São elas:

- `MYSQL_HOST` - o nome do host do servidor MySQL em execução.
- `MYSQL_USER` - o nome de usuário a ser usado na conexão.
- `MYSQL_PASSWORD` - a senha a ser usada na conexão.
- `MYSQL_DB` - o banco de dados a ser usado após a conexão.

> [!NOTE]
>
> Embora o uso de variáveis de ambiente para definir configurações de conexão
> seja geralmente aceitável durante o desenvolvimento, essa prática é fortemente
> desaconselhada ao executar aplicações em produção.
> Diogo Monica, ex-líder de segurança da Docker,
> [escreveu um artigo excelente](https://blog.diogomonica.com/2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)
> explicando os motivos.
>
> Um mecanismo mais seguro é usar o suporte a segredos oferecido pelo seu
> framework de orquestração de contêineres.
> Geralmente, esses segredos são montados como arquivos dentro do contêiner em
> execução.
> Você verá que muitas aplicações (incluindo a imagem do MySQL e a aplicação de
> lista de tarefas) também oferecem suporte a variáveis de ambiente com o sufixo
> `_FILE`, que apontam para um arquivo contendo o valor da variável.
>
> Por exemplo, definir a variável `MYSQL_PASSWORD_FILE` fará com que a aplicação
> use o conteúdo do arquivo referenciado como senha de conexão.
> O Docker não realiza nenhuma ação específica para suportar essas variáveis de
> ambiente; cabe à sua aplicação saber procurar pela variável e ler o conteúdo
> do arquivo.

Agora você pode iniciar seu contêiner pronto para desenvolvimento.

1. Defina cada uma das variáveis de ambiente anteriores e conecte o contêiner à
   rede da sua aplicação.
   Certifique-se de estar no diretório `getting-started-app` ao executar este
   comando.

   {{< tabs >}}
   {{< tab name="Mac / Linux" >}}

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

   {{< /tab >}}
   {{< tab name="PowerShell" >}}

   No Windows, execute este comando no PowerShell.

   ```powershell
   $ docker run -dp 127.0.0.1:3000:3000 `
     -w /app -v ".:/app" `
     --network todo-app `
     -e MYSQL_HOST=mysql `
     -e MYSQL_USER=root `
     -e MYSQL_PASSWORD=secret `
     -e MYSQL_DB=todos `
     node:24-alpine `
     sh -c "npm install && npm run dev"
   ```

   {{< /tab >}}
   {{< tab name="Prompt de Comando" >}}

   No Windows, execute este comando no Prompt de Comando.

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 ^
     -w /app -v "%cd%:/app" ^
     --network todo-app ^
     -e MYSQL_HOST=mysql ^
     -e MYSQL_USER=root ^
     -e MYSQL_PASSWORD=secret ^
     -e MYSQL_DB=todos ^
     node:24-alpine ^
     sh -c "npm install && npm run dev"
   ```

   {{< /tab >}}
   {{< tab name="Git Bash" >}}

   ```console
   $ docker run -dp 127.0.0.1:3000:3000 \
     -w //app -v "/.:/app" \
     --network todo-app \
     -e MYSQL_HOST=mysql \
     -e MYSQL_USER=root \
     -e MYSQL_PASSWORD=secret \
     -e MYSQL_DB=todos \
     node:24-alpine \
     sh -c "npm install && npm run dev"
   ```

   {{< /tab >}}
   {{< /tabs >}}

2. Se você verificar os logs do contêiner (`docker logs -f <id-do-contêiner>`),
   deverá ver uma mensagem semelhante à seguinte, indicando que ele está usando
   o banco de dados MySQL.

   ```console
   [nodemon] 3.1.11
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,cjs,json
   [nodemon] starting `node src/index.js`
   Waiting for mysql:3306.
   Connected!
   Connected to mysql db at host mysql
   Listening on port 3000
   ```

3. Abra a aplicação no seu navegador e adicione alguns itens à sua lista de
   tarefas.

4. Conecte-se ao banco de dados MySQL e comprove que os itens estão sendo
   gravados no banco de dados.
   Lembre-se de que a senha é `secret`.

   ```console
   $ docker exec -it <id-do-contêiner-do-mysql> mysql -p todos
   ```

   E no shell do MySQL, execute o seguinte:

   ```console
   mysql> select * from todo_items;
   +--------------------------------------+--------------------+-----------+
   | id                                   | name               | completed |
   +--------------------------------------+--------------------+-----------+
   | c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
   | 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
   +--------------------------------------+--------------------+-----------+
   ```

   Sua tabela terá uma aparência diferente porque contém seus itens.
   No entanto, você deve vê-los armazenados nela.

## Resumo

Neste ponto, você tem uma aplicação que armazena seus dados em um banco de dados
externo, executado em um contêiner separado.
Você aprendeu um pouco sobre redes de contêineres e descoberta de serviços
usando DNS.

Informações relacionadas:
- [Referência da CLI do Docker](/reference/cli/docker/)
- [Visão geral de redes](/manuals/engine/network/_index.md)

## Próximos passos

É bem provável que você esteja começando a sentir a sobrecarga de tudo o que é
necessário para iniciar esta aplicação.
Você precisa criar uma rede, iniciar contêineres, especificar todas as variáveis
de ambiente, expor portas e muito mais.
É muita coisa para lembrar e isso certamente dificulta o compartilhamento do
processo com outras pessoas.

Na próxima seção, você aprenderá sobre o Docker Compose.
Com o Docker Compose, você pode compartilhar as pilhas da sua aplicação de
maneira muito mais fácil e permitir que outras pessoas as iniciem com um único
comando simples.

{{< button text="Use o Docker Compose" url="08_using_compose.md" >}}
