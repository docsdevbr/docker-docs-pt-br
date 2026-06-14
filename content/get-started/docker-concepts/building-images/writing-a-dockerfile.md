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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/building-images/writing-a-dockerfile.md
source_revision: 0ed6c96eb61fc593788a8baa7a718ade6d7a1f42
translation_status: ready

title: Escrevendo um Dockerfile
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará como criar imagens usando o Dockerfile.
summary: >-
  Dominar as práticas do Dockerfile é vital para aproveitar a tecnologia de
  contêineres de forma eficaz, aumentando a confiabilidade da aplicação e dando
  suporte a DevOps e a metodologias de CI/CD.
  Neste guia, você aprenderá como escrever um Dockerfile, como definir uma
  imagem base e instruções de configuração, incluindo a instalação de programas
  e a cópia dos arquivos necessários.
weight: 2
aliases:
 - /guides/docker-concepts/building-images/writing-a-dockerfile/
---

{{< youtube-embed Jx8zoIhiP4c >}}

## Explicação

Um Dockerfile é um documento baseado em texto usado para criar uma imagem de
contêiner.
Ele fornece instruções ao construtor de imagens sobre os comandos a serem
executados, os arquivos a serem copiados, o comando de inicialização e muito
mais.

Por exemplo, o seguinte Dockerfile produziria uma aplicação Python pronta para
execução:

```dockerfile
FROM python:3.13
WORKDIR /usr/local/app

# Instala as dependências da aplicação
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Copia o código-fonte
COPY src ./src
EXPOSE 8080

# Configura um usuário de aplicação para que o contêiner não seja executado como
# usuário root
RUN useradd app
USER app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8080"]
```

### Instruções comuns

Algumas das instruções mais comuns em um `Dockerfile` incluem:

- `FROM <imagem>` - Especifica a imagem base que a construção estenderá.
- `WORKDIR <caminho>` - Esta instrução especifica o "diretório de trabalho" ou o
  caminho na imagem onde os arquivos serão copiados e os comandos serão
  executados.
- `COPY <caminho-no-host> <caminho-na-imagem>` - Esta instrução informa ao
  construtor para copiar os arquivos do host e colocá-los na imagem do
  contêiner.
- `RUN <comando>` - Esta instrução informa ao construtor para executar o comando
  especificado.
- `ENV <nome> <valor>` - Esta instrução define uma variável de ambiente que um
  contêiner em execução usará.
- `EXPOSE <numero-da-porta>` - Esta instrução define a configuração na imagem
  que indica uma porta que a imagem gostaria de expor.
- `USER <usuario-ou-uid>` - Esta instrução define o usuário padrão para todas as
  instruções subsequentes.
- `CMD ["<comando>", "<arg1>"]` - Esta instrução define o comando padrão que um
  contêiner que usa esta imagem executará.

Para ler todas as instruções ou entrar em mais detalhes, confira a
[referência do Dockerfile](https://docs.docker.com/engine/reference/builder/).

## Experimente

Assim como você viu no exemplo anterior, um Dockerfile normalmente segue estas
etapas:

1. Determina a imagem base.
2. Instala as dependências da aplicação.
3. Copia qualquer código-fonte e/ou binários relevantes.
4. Configura a imagem final.

Neste guia prático rápido, você escreverá um Dockerfile que cria uma aplicação
Node.js simples.
Se você não tiver familiaridade com aplicações baseadas em JavaScript, não se
preocupe.
Isso não é necessário para acompanhar este guia.

### Configuração

[Baixe este arquivo ZIP](https://github.com/docker/getting-started-todo-app/archive/refs/heads/build-image-from-scratch.zip)
e extraia o conteúdo em um diretório na sua máquina.

Se preferir não baixar um arquivo ZIP, clone o projeto
https://github.com/docker/getting-started-todo-app e faça o checkout do branch
`build-image-from-scratch`.

### Criando o Dockerfile

Agora que você tem o projeto, pode criar o `Dockerfile`.

1. [Baixe e instale](https://www.docker.com/products/docker-desktop/) o Docker
   Desktop.

2. Examine o projeto.

   Explore o conteúdo de `getting-started-todo-app/app/`.
   Você notará que um `Dockerfile` já existe.
   É um arquivo de texto simples que você pode abrir em qualquer editor de texto
   ou código.

3. Exclua o `Dockerfile` existente.

   Para este exercício, você fingirá que está começando do zero e criará um novo
   `Dockerfile`.

4. Crie um arquivo chamado `Dockerfile` na pasta
   `getting-started-todo-app/app/`.

   > **Extensões de arquivo Dockerfile**
   >
   > É importante observar que o `Dockerfile` _não_ possui extensão de arquivo.
   > Alguns editores adicionarão automaticamente uma extensão ao arquivo (ou
   > reclamarão que ele não possui uma).

5. No `Dockerfile`, defina sua imagem base adicionando a seguinte linha:

   ```dockerfile
   FROM node:22-alpine
   ```

6. Agora, defina o diretório de trabalho usando a instrução `WORKDIR`.
   Isso especificará onde os comandos futuros serão executados e o diretório
   para onde os arquivos serão copiados dentro da imagem do contêiner.

   ```dockerfile
   WORKDIR /app
   ```

7. Copie todos os arquivos do seu projeto na sua máquina para a imagem do
   contêiner usando a instrução `COPY`:

   ```dockerfile
   COPY . .
   ```

8. Instale as dependências da aplicação usando a CLI e o gerenciador de pacotes
   `yarn`.
   Para fazer isso, execute um comando usando a instrução `RUN`:

   ```dockerfile
   RUN yarn install --production
   ```

9. Por fim, especifique o comando padrão a ser executado usando a instrução
   `CMD`:

   ```dockerfile
   CMD ["node", "./src/index.js"]
   ```

   E com isso, você deve ter o seguinte Dockerfile:

   ```dockerfile
   FROM node:22-alpine
   WORKDIR /app
   COPY . .
   RUN yarn install --production
   CMD ["node", "./src/index.js"]
   ```

> **Este Dockerfile ainda não está pronto para produção**
>
> É importante observar que este Dockerfile _não_ segue todas as práticas
> recomendadas ainda (intencionalmente).
> Ele criará a aplicação, mas as construções não serão tão rápidas, nem as
> imagens tão seguras quanto poderiam ser.
>
> Continue lendo para saber mais sobre como fazer a imagem maximizar o cache de
> construção, como executar como um usuário não root e sobre construções em
> vários estágios.

## Recursos adicionais

Para saber mais sobre como escrever um Dockerfile, visite os seguintes recursos:

- [Referência do Dockerfile](/reference/dockerfile/)
- [Melhores práticas do Dockerfile](/develop/develop-images/dockerfile_best-practices/)
- [Imagens base](/build/building/base-images/)
- [Gordon](/ai/gordon/) — O assistente de IA do Docker pode gerar um Dockerfile
  para o seu projeto.
  Peça ao Gordon para analisar seu código e sugerir um Dockerfile otimizado para
  sua linguagem e framework.

## Próximos passos

Agora que você criou um Dockerfile e aprendeu o básico, é hora de aprender a
construir, criar tags e publicar as imagens.

{{< button text="Construa, crie tags e publique a imagem" url="build-tag-and-publish-an-image" >}}
