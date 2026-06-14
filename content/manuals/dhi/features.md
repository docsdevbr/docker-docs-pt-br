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

source_url: https://github.com/docker/docs/blob/main/content/manuals/dhi/features.md
source_revision: 1c519b2a8f5213d5733993f0ec17269cfdcb7f5d
translation_status: ready

title: Recursos das Imagens Docker Reforçadas
linktitle: Recursos
description: >-
  As Imagens Docker Reforçadas oferecem total transparência, superfície de
  ataque mínima e segurança de nível corporativo para todas as aplicações —
  gratuitas e de código aberto.
weight: 5
aliases:
  - /dhi/features/secure/
  - /dhi/features/integration/
  - /dhi/features/support/
  - /dhi/features/patching/
  - /dhi/features/flexible/
  - /dhi/features/helm/
---

As Imagens Docker Reforçadas (DHI) são imagens de contêineres e aplicações
mínimas, seguras e prontas para produção, mantidas pelo Docker.
Projetadas para reduzir vulnerabilidades e simplificar a conformidade, as DHI se
integram facilmente aos seus fluxos de trabalho existentes baseados em Docker,
com pouca ou nenhuma necessidade de reconfiguração.

A DHI oferece segurança para todas as pessoas:

- [DHI Community](#recursos-do-dhi-community) fornece recursos de segurança
  essenciais disponíveis para todas as pessoas, sem restrições de licenciamento
  sob a licença Apache 2.0.
- [DHI Select e DHI Enterprise](#recursos-do-dhi-select-e-enterprise) adicionam
  atualizações de segurança com garantia de SLA, variantes de conformidade com
  FIPS/STIG e recursos de personalização, com o DHI Enterprise oferecendo
  personalização ilimitada, acesso completo ao catálogo e a opção de Suporte
  ao Ciclo de Vida Estendido (ELS) para cobertura pós-fim de vida útil.

## Recursos do DHI Community

Os principais recursos da DHI são abertos e gratuitos para usar, compartilhar e
desenvolver, sem surpresas de licenciamento, com o respaldo da licença Apache
2.0.

### Segurança por padrão

- Quase zero CVEs: Varreduras e correções contínuas para manter o mínimo de
  vulnerabilidades exploráveis conhecidas, sem compromissos de tempo com
  garantia de SLA para pessoas usuárias do plano DHI Community.
- Superfície de ataque mínima: Variantes sem distribuição reduzem a superfície
  de ataque em até 95% removendo componentes desnecessários.
- Execução sem privilégios de root: Executado sem privilégios de root por
  padrão, seguindo o princípio do menor privilégio.
- Relatórios de vulnerabilidades transparentes: Cada CVE é visível e avaliado
  usando dados públicos — sem feeds suprimidos ou pontuação proprietária.

### Pacotes de sistema reforçados

As Imagens Docker Reforçadas mantêm a integridade da cadeia de suprimentos em
toda a pilha de imagens com pacotes de sistema reforçados:

- Pacotes compilados a partir do código-fonte: Para distribuições compatíveis,
  os pacotes de sistema são compilados a partir do código-fonte pelo Docker.
- Assinaturas criptográficas: Cada pacote é assinado e verificado
  criptograficamente.
- Segurança da cadeia de suprimentos: Elimina o risco de pacotes públicos
  potencialmente comprometidos.

Os pacotes de sistema reforçados estão incluídos nas distribuições compatíveis
das imagens DHI.

Pessoas usuárias do plano Community também podem configurar seu gerenciador de
pacotes para usar o repositório público de pacotes reforçados do Docker em suas
próprias imagens para os mesmos pacotes incluídos nas imagens base.
Consulte [Usar pacotes de sistema reforçados](./how-to/hardened-packages.md)
para obter detalhes.

### Transparência total

Cada imagem inclui metadados de segurança completos e verificáveis:

- Proveniência SLSA Build Nível 3: Construções verificáveis e resistentes a
  adulteração que atendem aos padrões de segurança da cadeia de suprimentos.
- SBOMs assinados: Lista completa de materiais de software para cada componente.
- Declarações VEX: Documentos da Vulnerability Exploitability eXchange fornecem
  contexto sobre CVEs conhecidos.
- Assinaturas criptográficas: Todas as imagens e metadados são assinados para
  autenticidade.

### Desenvolvida para pessoas desenvolvedoras

- Bases familiares: Construída sobre Alpine e Debian, exigindo alterações
  mínimas para adoção.
- Suporte a glibc e musl: Disponível em ambas as variantes para ampla
  compatibilidade de aplicações.
- Variantes de desenvolvimento e tempo de execução: Use imagens de
  desenvolvimento para compilação e imagens do tempo de execução mínimas para
  produção.
- Compatibilidade imediata: Funciona perfeitamente com fluxos de trabalho Docker
  existentes, pipelines de CI/CD e ferramentas.

### Manutenção contínua

- Aplicação automática de patches: As imagens são reconstruídas e atualizadas
  quando patches de segurança upstream estão disponíveis, sem compromissos de
  tempo com garantia de SLA para pessoas usuárias do plano DHI Community.
- Integração com scanners: Integração direta com scanners e outras plataformas
  de segurança.

### Suporte a Kubernetes e charts Helm

Os charts das Imagens Docker Reforçadas (DHI) são charts Helm fornecidos pelo
Docker, construídos a partir de fontes upstream, projetados para compatibilidade
com as Imagens Docker Reforçadas.
Esses charts estão disponíveis como artefatos OCI no catálogo DHI do Docker Hub.
Os charts DHI são rigorosamente testados após a construção para garantir que
funcionem imediatamente com as Imagens Docker Reforçadas.
Isso elimina atritos na migração e reduz a carga de trabalho das pessoas
desenvolvedoras na implementação dos charts, garantindo compatibilidade
perfeita.

Assim como as imagens reforçadas, os charts DHI incorporam múltiplas camadas de
metadados de segurança para garantir transparência e confiança:

- Conformidade com o SLSA Nível 3: Cada gráfico é construído com o sistema SLSA
  Build Nível 3 do Docker, incluindo uma procedência de construção detalhada e
  atendendo aos padrões definidos pelo framework Supply-chain Levels for
  Software Artifacts (SLSA).
- Listas de Materiais de Software (SBOMs): SBOMs abrangentes são fornecidas,
  detalhando todos os componentes referenciados no chart para facilitar o
  gerenciamento de vulnerabilidades e auditorias de conformidade.
- Assinatura criptográfica: Todos os metadados associados são assinados
  criptograficamente pelo Docker, garantindo integridade e autenticidade.
- Configuração reforçada: Os charts referenciam automaticamente Imagens Docker
  Reforçadas, garantindo segurança nas implantações.

## Recursos do DHI Select e Enterprise

Para organizações com requisitos de segurança rigorosos, demandas regulatórias
ou necessidades operacionais, o DHI Select e Enterprise oferecem recursos
adicionais.

O DHI Select oferece personalizações, variantes de conformidade e atualizações
com garantia de SLA para times e organizações com cargas de trabalho de
produção.
O DHI Enterprise inclui tudo do Select com personalizações ilimitadas, além de
um complemento opcional de Suporte ao Ciclo de Vida Estendido e acesso completo
ao catálogo para grandes empresas com necessidades avançadas de segurança.

Para uma comparação detalhada, consulte a
[Comparação de assinaturas das Imagens Docker Reforçadas](https://www.docker.com/products/hardened-images/#compare).

### Segurança com garantia de SLA {tier="DHI Select ou DHI Enterprise"}

- SLA de correção de CVE: SLA de 7 dias para vulnerabilidades críticas e de alta
  gravidade.
- Correção contínua: Atualizações de segurança regulares com garantia de SLA.
- Suporte corporativo: Acesso ao time de suporte do Docker para aplicações de
  missão crítica.

Para obter detalhes completos, consulte o
[Support Service Level Agreement](https://docs.docker.com/go/dhi-sla/).

### Variantes de conformidade {tier="DHI Select ou DHI Enterprise"}

- Imagens compatíveis com FIPS: Para setores regulamentados e sistemas
  governamentais.
- Imagens prontas para STIG: Atendem aos requisitos do Guia de Implementação
  Técnica de Segurança do Departamento de Defesa dos EUA (DoD).

### Personalização e controle {tier="DHI Select ou DHI Enterprise"}

- Crie imagens personalizadas: Adicione seus próprios pacotes, ferramentas,
  certificados e configurações.
  - DHI Select: Até 5 personalizações.
  - DHI Enterprise: Personalizações ilimitadas.
- Pacotes reforçados: Acesso a pacotes adicionais específicos de conformidade
  (como variantes FIPS) e pacotes com patches do Docker não disponíveis no
  repositório público.
  - DHI Select: Adicione esses pacotes por meio da interface de personalização
    ao personalizar imagens reforçadas.
  - DHI Enterprise: Adicione esses pacotes por meio da interface de
    personalização ou configure seu gerenciador de pacotes para usar o
    repositório de pacotes corporativos em suas próprias imagens.
- Infraestrutura de compilação segura: Personalizações criadas na infraestrutura
  confiável do Docker.
- Cadeia de confiança completa: Imagens personalizadas mantêm a procedência e a
  assinatura criptográfica.
- Atualizações automáticas: Imagens personalizadas são reconstruídas
  automaticamente quando as imagens base são corrigidas.

### Suporte ao Ciclo de Vida Estendido {tier="DHI Enterprise add-on"}

- Cobertura de segurança pós-fim de vida: Continue recebendo correções por anos
  após o término do suporte upstream.
- Conformidade contínua: SBOMs, procedência e assinatura atualizados para
  requisitos de auditoria.
- Continuidade da produção: Mantenha a produção em execução com segurança, sem
  migrações forçadas.

## Saiba mais

- [Explore como as imagens DHI são criadas e muito mais](/dhi/explore/)
- [Comece a usar DHIs](/dhi/get-started/)
- <a href="https://www.docker.com/pricing/contact-sales/" id="dkr_docs_cs_dhi_features" class="link" rel="noopener">Entre em contato com o Docker para obter o DHI Enterprise</a>
