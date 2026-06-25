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

source_url: https://github.com/docker/docs/blob/main/content/get-started/docker-concepts/building-images/multi-stage-builds.md
source_revision: fbcabd5b97fcfdf62d76e6735765d3388ed184d6
translation_status: ready

title: Construções multiestágio
keywords: conceitos, construção, imagens, contêiner, docker desktop
description: >-
  Esta página conceitual ensinará você sobre o propósito da construção
  multiestágio e seus benefícios.
summary: >-
  Ao separar o ambiente de construção do ambiente de execução final, você pode
  reduzir significativamente o tamanho da imagem e a superfície de ataque.
  Neste guia, você descobrirá o poder das construções multiestágio para criar
  imagens Docker enxutas e eficientes, essenciais para minimizar a sobrecarga e
  aprimorar a implantação em ambientes de produção.
weight: 5
aliases:
 - /guides/docker-concepts/building-images/multi-stage-builds/
---

{{< youtube-embed vR185cjwxZ8 >}}

## Explicação

Em uma construção tradicional, todas as instruções de construção são executadas
em sequência e em um único contêiner de construção: baixar dependências,
compilar o código e empacotar a aplicação.
Todas essas camadas resultam na sua imagem final.
Essa abordagem funciona, mas leva a imagens volumosas, com peso desnecessário e
aumentando os riscos de segurança.
É aí que entram as construções multiestágio.

As construções multiestágio introduzem várias etapas no seu Dockerfile, cada uma
com um propósito específico.
Pense nisso como a capacidade de executar diferentes partes de uma construção em
vários ambientes diferentes, simultaneamente.
Ao separar o ambiente de construção do ambiente de execução final, você pode
reduzir significativamente o tamanho da imagem e a superfície de ataque.
Isso é especialmente benéfico para aplicações com grandes dependências de
construção.

As construções multiestágio são recomendadas para todos os tipos de aplicações.

- Para linguagens interpretadas, como JavaScript, Ruby ou Python, você pode
  construir e minificar seu código em uma etapa e copiar os arquivos prontos
  para produção para uma imagem de execução menor.
  Isso otimiza sua imagem para implantação.
- Para linguagens compiladas, como C, Go ou Rust, a compilação multiestágio
  permite compilar em uma etapa e copiar os binários compilados para uma imagem
  de tempo de execução final.
  Não é necessário incluir todo o compilador na imagem final.

Aqui está um exemplo simplificado de uma estrutura de construção multiestágio
usando pseudocódigo.
Observe que existem várias instruções `FROM` e uma nova instrução
`AS <nome-da-etapa>`.
Além disso, a instrução `COPY` na segunda etapa está copiando `--from` da etapa
anterior.

```dockerfile
# Etapa 1: ambiente de construção
FROM builder-image AS build-stage
# Instala as ferramentas de construção (ex.: Maven, Gradle)
# Copia o código-fonte
# Comandos de construção (ex.: compile, package)

# Etapa 2: ambiente de execução
FROM runtime-image AS final-stage
# Copia os artefatos da aplicação da etapa de construção (ex.: arquivo JAR)
COPY --from=build-stage /path/in/build/stage /path/to/place/in/final/stage
# Define a configuração de tempo de execução (ex.: CMD, ENTRYPOINT)
```

Este Dockerfile usa duas etapas:

- A etapa de construção usa uma imagem base contendo as ferramentas de
  compilação necessárias para compilar sua aplicação.
  Ela inclui comandos para instalar as ferramentas de compilação, copiar o
  código-fonte e executar os comandos de compilação.
- A etapa final usa uma imagem base menor, adequada para executar sua aplicação.
  Ela copia os artefatos compilados (um arquivo JAR, por exemplo) da etapa de
  construção.
  Finalmente, define a configuração de tempo de execução (usando `CMD` ou
  `ENTRYPOINT`) para iniciar sua aplicação.

## Experimente

Neste guia prático, você descobrirá o poder das construções multiestágio para
criar imagens Docker enxutas e eficientes para uma aplicação Java de exemplo.
Você usará como exemplo uma aplicação simples "Olá Mundo" baseada em Spring
Boot, construída com Maven.

1. [Baixe e instale](https://www.docker.com/products/docker-desktop/) o Docker
   Desktop.

2. Abra este
   [projeto pré-inicializado](https://start.spring.io/#!type=maven-project&language=java&platformVersion=4.0.1&packaging=jar&configurationFileFormat=properties&jvmVersion=21&groupId=com.example&artifactId=spring-boot-docker&name=spring-boot-docker&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.spring-boot-docker&dependencies=web)
   para gerar um arquivo ZIP.
   Veja como fica:

   ![Uma captura de tela da ferramenta Spring Initializr selecionada com Java 21, Spring Web e Spring Boot 3.4.0](images/multi-stage-builds-spring-initializer.webp?border=true)

   O [Spring Initializr](https://start.spring.io/) é um gerador de projetos
   Spring de início rápido.
   Ele fornece uma API extensível para gerar projetos baseados na JVM com
   implementações para diversos conceitos comuns — como geração básica de
   linguagem para Java, Kotlin, Groovy e Maven.

   Selecione **Generate** para criar e baixar o arquivo zip deste projeto.

   Para esta demonstração, você combinou a automação de compilação do Maven com
   Java, uma dependência do Spring Web e Java 21 para seus metadados.

3. Navegue até o diretório do projeto.
   Após descompactar o arquivo, você verá a seguinte estrutura de diretórios do
   projeto:

   ```plaintext
   spring-boot-docker
   ├── HELP.md
   ├── mvnw
   ├── mvnw.cmd
   ├── pom.xml
   └── src
       ├── main
       │   ├── java
       │   │   └── com
       │   │       └── example
       │   │           └── spring_boot_docker
       │   │               └── SpringBootDockerApplication.java
       │   └── resources
       │       ├── application.properties
       │       ├── static
       │       └── templates
       └── test
           └── java
               └── com
                   └── example
                       └── spring_boot_docker
                           └── SpringBootDockerApplicationTests.java

   15 directories, 7 files
   ```

   O diretório `src/main/java` contém o código-fonte do seu projeto, o diretório
   `src/test/java` contém o código-fonte dos testes e o arquivo `pom.xml` é o
   Modelo de Objeto do Projeto (POM) do seu projeto.

   O arquivo `pom.xml` é o núcleo da configuração de um projeto Maven.
   É um único arquivo de configuração que contém a maioria das informações
   necessárias para construir um projeto personalizado.
   O POM é enorme e pode parecer intimidante.
   Felizmente, você ainda não precisa entender todas as suas complexidades para
   usá-lo com eficácia.

4. Crie um serviço web RESTful que exiba "Olá, Mundo!".

   No diretório `src/main/java/com/example/spring_boot_docker/`, você pode
   modificar o seu arquivo `SpringBootDockerApplication.java` com o seguinte
   conteúdo:

   ```java
   package com.example.spring_boot_docker;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   @SpringBootApplication
   public class SpringBootDockerApplication {

       @RequestMapping("/")
       public String home() {
           return "Olá, Mundo!";
       }

       public static void main(String[] args) {
           SpringApplication.run(SpringBootDockerApplication.class, args);
       }

   }
   ```

   O arquivo `SpringbootDockerApplication.java` começa declarando o pacote
   `com.example.spring_boot_docker` e importando os frameworks Spring
   necessários.
   Este arquivo Java cria uma aplicação web Spring Boot simples que responde com
   "Olá, Mundo!" quando uma pessoa usuária visita sua página inicial.

### Crie o Dockerfile

Agora que você tem o projeto, já pode criar o `Dockerfile`.

1. Crie um arquivo chamado `Dockerfile` na mesma pasta que contém todas as
   outras pastas e arquivos (como src, pom.xml, etc.).

2. No `Dockerfile`, defina sua imagem base adicionando a seguinte linha:

   ```dockerfile
   FROM eclipse-temurin:21.0.8_9-jdk-jammy
   ```

3. Agora, defina o diretório de trabalho usando a instrução `WORKDIR`.
   Isso especificará onde os comandos futuros serão executados e onde os
   arquivos do diretório serão copiados para dentro da imagem do contêiner.

   ```dockerfile
   WORKDIR /app
   ```

4. Copie o script wrapper do Maven e o arquivo `pom.xml` do seu projeto para o
   diretório de trabalho atual `/app` dentro do contêiner Docker.

   ```dockerfile
   COPY .mvn/ .mvn
   COPY mvnw pom.xml ./
   ```

5. Execute um comando dentro do contêiner.
   Ele executa o comando `./mvnw dependency:go-offline`, que usa o wrapper do
   Maven (`./mvnw`) para baixar todas as dependências do seu projeto sem
   construir o arquivo JAR final (útil para builds mais rápidos).

   ```dockerfile
   RUN ./mvnw dependency:go-offline
   ```

6. Copie o diretório `src` do seu projeto na máquina host para o diretório
   `/app` dentro do contêiner.

   ```dockerfile
   COPY src ./src
   ```

7. Defina o comando padrão a ser executado quando o contêiner for iniciado.
   Este comando instrui o contêiner a executar o wrapper do Maven (`./mvnw`) com
   o goal `spring-boot:run`, que irá compilar e executar sua aplicação Spring
   Boot.

   ```dockerfile
   CMD ["./mvnw", "spring-boot:run"]
   ```

   Com isso, você deverá ter o seguinte Dockerfile:

   ```dockerfile
   FROM eclipse-temurin:21.0.8_9-jdk-jammy
   WORKDIR /app
   COPY .mvn/ .mvn
   COPY mvnw pom.xml ./
   RUN ./mvnw dependency:go-offline
   COPY src ./src
   CMD ["./mvnw", "spring-boot:run"]
   ```

### Construa a imagem do contêiner

1. Execute o seguinte comando para criar a imagem Docker:

   ```console
   $ docker build -t spring-helloworld .
   ```

2. Verifique o tamanho da imagem Docker usando o comando `docker images`:

   ```console
   $ docker images
   ```

   Ao fazer isso, você verá uma saída semelhante a esta:

   ```console
   REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
   spring-helloworld   latest    ff708d5ee194   3 minutes ago    880MB
   ```

   Essa saída mostra que sua imagem tem 880 MB.
   Ela contém o JDK completo, o conjunto de ferramentas Maven e outros recursos.
   Em produção, você não precisa disso na sua imagem final.

### Execute a aplicação Spring Boot

1. Agora que você criou uma imagem, é hora de executar o contêiner.

   ```console
   $ docker run -p 8080:8080 spring-helloworld
   ```

   Você verá uma saída semelhante à seguinte no log do contêiner:

   ```plaintext
   [INFO] --- spring-boot:3.3.4:run (default-cli) @ spring-boot-docker ---
   [INFO] Attaching agents: []

        .   ____          _            __ _ _
       /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
      ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
       \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
        '  |____| .__|_| |_|_| |_\__, | / / / /
       =========|_|==============|___/=/_/_/_/

       :: Spring Boot ::                (v3.3.4)

   2024-09-29T23:54:07.157Z  INFO 159 --- [spring-boot-docker] [           main]
   c.e.s.SpringBootDockerApplication        : Starting SpringBootDockerApplication using Java
   21.0.2 with PID 159 (/app/target/classes started by root in /app)
   ….
   ```

2. Acesse sua página “Olá, Mundo!” através do seu navegador em
   [http://localhost:8080](http://localhost:8080), ou através deste comando
   curl:

   ```console
   $ curl localhost:8080
   Olá, Mundo!
   ```

### Use construções multiestágio

1. Considere o seguinte Dockerfile:

   ```dockerfile
   FROM eclipse-temurin:21.0.8_9-jdk-jammy AS builder
   WORKDIR /opt/app
   COPY .mvn/ .mvn
   COPY mvnw pom.xml ./
   RUN ./mvnw dependency:go-offline
   COPY ./src ./src
   RUN ./mvnw clean install

   FROM eclipse-temurin:21.0.8_9-jre-jammy AS final
   WORKDIR /opt/app
   EXPOSE 8080
   COPY --from=builder /opt/app/target/*.jar /opt/app/*.jar
   ENTRYPOINT ["java", "-jar", "/opt/app/*.jar"]
   ```

   Observe que este Dockerfile foi dividido em duas etapas.

   - A primeira etapa permanece a mesma do Dockerfile anterior, fornecendo um
     ambiente Java Development Kit (JDK) para a construção da aplicação.
     Esta etapa recebeu o nome de `builder`.

   - A segunda etapa é uma nova etapa chamada `final`.
     Ela usa uma imagem mais enxuta `eclipse-temurin:21.0.2_13-jre-jammy`,
     contendo apenas o Java Runtime Environment (JRE) necessário para executar a
     aplicação.
     Esta imagem fornece um Java Runtime Environment (JRE) suficiente para
     executar a aplicação compilada (arquivo JAR).

   > Para uso em produção, é altamente recomendável que você crie um ambiente de
     execução personalizado semelhante ao JRE usando o jlink.
     Imagens JRE estão disponíveis para todas as versões do Eclipse Temurin, mas
     o jlink permite criar um ambiente de execução mínimo contendo apenas os
     módulos Java necessários para sua aplicação.
     Isso pode reduzir significativamente o tamanho e melhorar a segurança da
     sua imagem final.
     [Consulte esta página](https://hub.docker.com/_/eclipse-temurin) para obter
     mais informações.

   Com construções multiestágio, uma construção do Docker usa uma imagem base
   para compilação, empacotamento e testes unitários e, em seguida, uma imagem
   separada para o ambiente de execução da aplicação.
   Como resultado, a imagem final tem um tamanho menor, pois não contém
   ferramentas de desenvolvimento ou depuração.
   Ao separar o ambiente de construção do ambiente de execução final, você pode
   reduzir significativamente o tamanho da imagem e aumentar a segurança das
   suas imagens finais.

2. Agora, reconstrua sua imagem e execute sua construção de produção pronta para
   uso.

   ```console
   $ docker build -t spring-helloworld-builder .
   ```

   Este comando cria uma imagem Docker chamada `spring-helloworld-builder`
   usando o estágio final do seu arquivo `Dockerfile` localizado no diretório
   atual.

   > [!NOTE]
   >
   > Em seu Dockerfile multiestágio, o estágio final (final) é o alvo padrão
   > para a construção.
   > Isso significa que, se você não especificar explicitamente um estágio de
   > destino usando a flag `--target` no comando `docker build`, o Docker
   > construirá automaticamente o último estágio por padrão.
   > Você pode usar
   > `docker build -t spring-helloworld-builder --target builder .` para
   > construir apenas o estágio builder com o ambiente JDK.

3. Observe a diferença no tamanho da imagem usando o comando `docker images`:

   ```console
   $ docker images
   ```

   Você verá uma saída semelhante a esta:

   ```console
   spring-helloworld-builder latest    c5c76cb815c0   24 minutes ago      428MB
   spring-helloworld         latest    ff708d5ee194   About an hour ago   880MB
   ```

   Sua imagem final tem apenas 428 MB, em comparação com o tamanho original de
   880 MB.

   Ao otimizar cada etapa e incluir apenas o necessário, você conseguiu reduzir
   significativamente o tamanho total da imagem, mantendo a mesma
   funcionalidade.
   Isso não só melhora o desempenho, como também torna suas imagens Docker mais
   leves, seguras e fáceis de gerenciar.

## Recursos adicionais

* [Construções multiestágio](/build/building/multi-stage/)
* [Melhores práticas para o Dockerfile](/develop/develop-images/dockerfile_best-practices/)
* [Imagens base](/build/building/base-images/)
* [Spring Boot Docker](https://spring.io/guides/topicals/spring-boot-docker)
