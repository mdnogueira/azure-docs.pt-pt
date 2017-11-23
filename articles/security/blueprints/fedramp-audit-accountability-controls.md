---
title: "FedRAMP Blueprint da automatização do Azure - auditoria e Accountability"
description: "Aplicações Web para FedRAMP - auditoria e Accountability"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: c5858e07-ca74-4526-b31f-e6b4e55bb594
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 83ef9cbb7652bf128d7758237a8e6fbeed6c6565
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="audit-and-accountability-au"></a>Auditoria e Accountability (AU)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-au-1"></a>Controlo NIST 800-53 AU-1

#### <a name="audit-and-accountability-policy-and-procedures"></a>Auditoria e de política de Accountability e de procedimentos

**AU-1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de auditoria e accountability endereços objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da auditoria accountability de política e auditoria associada e controlos de accountability; analisa e atualiza a política de auditoria e accountability atual [atribuição: frequência definida de organização]; e procedimentos de auditoria e accountability [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de auditoria e accountability de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-2a"></a>AU-2.a do NIST 800-53 controlo

#### <a name="audit-events"></a>Eventos de auditoria

**AU 2.a** organização determina que o sistema de informações é capaz de auditoria os seguintes eventos: [atribuição: definido de organização auditável eventos].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Capacidade de auditoria para este Blueprint do Azure é fornecida pelo Monitor do Azure e o serviço de análise de registos no OMS. Monitor do Azure fornece registos de auditoria de detalhado sobre a atividade associados a recursos implementados. Estas e registos de nível de SO são recolhidos através da análise de registos e armazenados no repositório de OMS. Análise de registos está correlacionada com dados de auditoria em recursos implementados por esta solução e pode ser expandido para a aplicação web implementada de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-2b"></a>AU-2.b do NIST 800-53 controlo

#### <a name="audit-events"></a>Eventos de auditoria

**AU 2.b** organização coordena a função de auditoria de segurança com outras entidades organizacionais necessidade de informações relacionadas com a auditoria para melhorar a suporte mútua e para o orientar a seleção de syslogs eventos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de um processo de nível empresarial estabelecido que determina auditável eventos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-2c"></a>AU-2.c do NIST 800-53 controlo

#### <a name="audit-events"></a>Eventos de auditoria

**AU 2.c** organização fornece uma lógica por detrás de razão pela qual os eventos auditável considerados ser suficiente suportar as investigações de após o facto de incidentes de segurança.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os eventos auditados por esta Blueprint Azure incluem informações suficientes para determinar quando ocorrem eventos, a origem do evento, o resultado de evento e outras informações detalhadas que suporte a investigação de incidentes de segurança. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-2d"></a>AU-2.d do NIST 800-53 controlo

#### <a name="audit-events"></a>Eventos de auditoria

**AU 2.d** organização determina se os seguintes eventos são a serem auditados no sistema de informações: [atribuição: definido de organização auditada eventos (o subconjunto dos eventos auditável definido no recomeço AU-2), juntamente com a frequência (ou auditoria situation requiring) para cada evento identificado].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os eventos auditados por esta Blueprint Azure incluem esses auditada por registos de atividade do Azure para recursos implementados, os registos de nível de SO, registos do Active Directory e os registos do servidor de SQL. Os clientes poderão selecionar eventos adicionais a serem auditados para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-2-3"></a>NIST 800-53 controlo AU-2 (3)

#### <a name="audit-events--reviews-and-updates"></a>Eventos de auditoria | Revisões e atualizações

**AU-2 (3)** organização revê e atualiza os eventos auditados [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá baseiam-se uma revisão periódica de nível empresarial estabelecida e atualize o processo para o conjunto definido de eventos auditados. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-3"></a>Controlo NIST 800-53 AU-3

#### <a name="content-of-audit-records"></a>Conteúdo dos registos de auditoria

**AU 3** o sistema de informações gera registos de auditoria que contenham informações que estabelece o tipo de evento ocorreu, quando o evento ocorreu, onde o evento ocorreu, a origem do evento, o resultado do evento e a identidade de qualquer utilizadores individuais ou assuntos associados ao evento.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure baseia-se nas capacidades de auditoria incorporadas do Azure, o Windows Server e o SQL Server. Estes registos de auditoria de captura de soluções com detalhes suficientes para satisfazer os requisitos deste controlo de auditoria. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-3-1"></a>NIST 800-53 controlo AU-3 (1)

#### <a name="content-of-audit-records--additional-audit-information"></a>Conteúdo dos registos de auditoria | Informações adicionais de auditoria

**AU-3 (1)** o sistema de informações gera registos de auditoria que contém as seguintes informações adicionais: [atribuição: definida organização adicionais, informações mais detalhadas].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Eventos de registo de atividade do Azure utilizam um esquema de detalhado que contém campos de mais de 20 tipos de informações de auditoria. Para além do registo de atividade, este Blueprint Azure implementa a solução de análise de registos na OMS que suporta um conjunto diverso de origens de dados, incluindo os registos do Windows, Linux registos, registos de diagnóstico do Azure e os registos de cliente.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-3-2"></a>NIST 800-53 controlo AU-3 (2)

#### <a name="content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Conteúdo dos registos de auditoria | Gestão centralizada de conteúdo de registo de auditoria planeada

**AU-3 (2)** o sistema de informações fornece uma gestão centralizada e a configuração do conteúdo para serem capturadas nos registos de auditoria gerados por [atribuição: os componentes do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Todas as máquinas virtuais implementadas por esta Blueprint do Azure estão associadas ao domínio do Active Directory implementado. Todas as máquinas virtuais associados a um domínio de implementar uma política de grupo que pode ser configurada para gerir centralmente a configuração ao nível do SO do sistema de auditoria. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-4"></a>Controlo NIST 800-53 AU-4

#### <a name="audit-storage-capacity"></a>Capacidade de armazenamento de auditoria

**AU 4** organização aloca a capacidade de armazenamento de registos de auditoria em conformidade com [atribuição: requisitos de armazenamento de registos de auditoria definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure aloca a capacidade de armazenamento suficiente para reter os registos de auditoria durante um período de um ano. Todos os registos de auditoria são recolhidos através da análise de registos que está configurado para a retenção de um ano. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-5a"></a>AU-5.a do NIST 800-53 controlo

#### <a name="response-to-audit-processing-failures"></a>Resposta a auditoria de falhas de processamento

**AU 5.a** os alertas de sistema de informações [atribuição: técnico definido pela organização ou funções] na eventualidade de ocorrer uma falha no processamento de auditoria.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Estado do serviço para monitorização do Azure e análise de registos está disponível no Web site do Estado do Azure e o painel de estado de funcionamento do serviço no portal do Azure. Alertas podem ser configurados através da análise de registos para fornecer a notificação de outros tipos de auditoria de falhas de processamento. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-5b"></a>AU-5.b do NIST 800-53 controlo

#### <a name="response-to-audit-processing-failures"></a>Resposta a auditoria de falhas de processamento

**AU 5.b** o sistema de informações efetua as seguintes ações adicionais: [atribuição: definido de organização ações a executar (por exemplo, encerrar o sistema de informações, substituir os registos de auditoria mais antigos, parar geração de registos de auditoria)].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Todos os registos de auditoria gerados pelos recursos implementados por esta Blueprint do Azure são recolhidos através da análise de registos e mantidos durante um período de um ano. A alocação de armazenamento para este armazenamento de registos de auditoria é dinamicamente atribuída garantir capacidade suficiente está disponível. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-5-1"></a>NIST 800-53 controlo AU-5 (1)

#### <a name="response-to-audit-processing-failures--audit-storage-capacity"></a>Resposta a auditoria de falhas de processamento | Capacidade de armazenamento de auditoria

**AU-5 (1)** o sistema de informações fornece um aviso para [atribuição: definido de organização técnico, funções de e/ou localizações] dentro [atribuição: organização definidos pelo período de tempo] quando o volume de armazenamento de registos de auditoria alocado atinge [ Atribuição: percentagem definido de organização] do repositório máximo capacidade de armazenamento de registos de auditoria.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Todos os registos de auditoria gerados pelos recursos implementados por esta Blueprint do Azure são recolhidos através da análise de registos e mantidos durante um período de um ano. A alocação de armazenamento para este armazenamento de registos de auditoria é dinamicamente atribuída garantir capacidade suficiente está disponível. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-5-2"></a>NIST 800-53 controlo AU-5 (2)

#### <a name="response-to-audit-processing-failures--real-time-alerts"></a>Resposta a auditoria de falhas de processamento | Alertas em tempo real

**AU-5 (2)** o sistema de informações fornece um alerta em [atribuição: organização definidos pelo período em tempo real] para [atribuição: definido de organização técnico, funções de e/ou localizações] quando ocorrem os seguintes eventos de falha de auditoria: [atribuição: definido pelo organização falha eventos de auditoria que necessitam de alertas em tempo real].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Estado do serviço do Azure está disponível no painel de estado de funcionamento do serviço no portal do Azure. Alertas podem ser configurados através da análise de registos para fornecer a notificação de outros tipos de auditoria de falhas de processamento. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-6a"></a>AU-6.a do NIST 800-53 controlo

#### <a name="audit-review-analysis-and-reporting"></a>Revisão de auditoria, análises e relatórios

**AU 6.a** organização revê e analisa os registos de auditoria de sistema de informações [atribuição: frequência definida de organização] indicações de [atribuição: definido de organização inapropriada ou invulgar atividade].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por rever e analisar registos de auditoria dos recursos de implementação de cliente (para incluir aplicações, sistemas operativos, bases de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-6b"></a>NIST 800-53 controlo AU-6. b

#### <a name="audit-review-analysis-and-reporting"></a>Revisão de auditoria, análises e relatórios

**AU-6. b** organização findings para os relatórios [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por reporting findings da atividade inapropriada ou invulgar (definido no AU 06.a) nos recursos de implementação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-1"></a>NIST 800-53 controlo AU-6 (1)

#### <a name="audit-review-analysis-and-reporting--process-integration"></a>Reveja, análises e relatórios de auditoria | Processo de integração

**AU-6 (1)** organização emprega automatizados mecanismos para integrar a revisão de auditoria, análise e relatórios processos para suportar processos organizacionais para investigação e resposta a atividades suspeitas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de uma revisão de auditoria centralizada de nível empresarial, análise e capacidade de relatórios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-3"></a>NIST 800-53 controlo AU-6 (3)

#### <a name="audit-review-analysis-and-reporting--correlate-audit-repositories"></a>Reveja, análises e relatórios de auditoria | Correlacionar repositórios de auditoria

**AU-6 (3)** analisa a organização e correlaciona auditar registos em diferentes repositórios de obter deteção situational toda a organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de análise de registos na OMS para centralizar os dados de auditoria em recursos implementados, que suporta a deteção situational toda a organização. Os clientes podem optar por obter integrar a análise de registos com outros sistemas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-4"></a>NIST 800-53 controlo AU-6 (4)

#### <a name="audit-review-analysis-and-reporting--central-review-and-analysis"></a>Reveja, análises e relatórios de auditoria | Reveja central e análise

**AU-6 (4)** o sistema de informações fornece a capacidade de centralmente rever e analisar registos de auditoria de vários componentes no sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de análise de registos OMS para centralizar os dados de auditoria em recursos implementados, suporte revisão centralizada, análises e relatórios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-5"></a>NIST 800-53 controlo AU-6 (5)

#### <a name="audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>Reveja, análises e relatórios de auditoria | Integração / análise e capacidades de monitorização

**AU-6 (5)** organização integra-se a análise de registos de auditoria com a análise das [seleção (um ou mais): a análise de informações de dados de desempenho; sistema informações informações; de monitorização de vulnerabilidade [Atribuição: recolhidos a partir de outras origens de dados/informações definidas pela organização]] para melhorar ainda mais a capacidade de identificar a atividade inapropriada ou invulgar.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de auditoria e segurança do OMS. Esta solução fornece uma vista abrangente do postura de segurança. O dashboard de auditoria e segurança fornece aprofundadas alto nível sobre o estado de segurança dos recursos implementados utilizando os dados disponíveis em soluções do OMS implementadas, a integração de dados de registo e dos dados de vulnerabilidade de avaliação de patch e de linha de base. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-6"></a>NIST 800-53 controlo AU-6 (6)

#### <a name="audit-review-analysis-and-reporting--correlation-with-physical-monitoring"></a>Reveja, análises e relatórios de auditoria | Correlação com a monitorização físico

**AU-6 (6)** organização está correlacionada com informações a partir de registos de auditoria com as informações obtidas de acesso físico para melhorar ainda mais a capacidade de identificar a atividade suspeita, inadequada, invulgar ou malevolent de monitorização.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Os clientes não têm acesso físico a quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Equipa do Microsoft Azure SIM utiliza os dados de monitorização físicos relacionados e correlaciona-lo com registos de auditoria para determinar se havia qualquer violação lógica associada ou comportamento suspeito quando são detetadas incidentes físicos. |


 ### <a name="nist-800-53-control-au-6-7"></a>NIST 800-53 controlo AU-6 (7)

#### <a name="audit-review-analysis-and-reporting--permitted-actions"></a>Reveja, análises e relatórios de auditoria | Ações permitidas

**AU-6 (7)** organização Especifica as ações permitidas para cada [seleção (um ou mais): o processo de sistema de informações; a função de utilizador] associado a revisão, análise e relatórios de informações de auditoria.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais do Windows implementadas por esta Blueprint Azure implementar permissões de nível de SO que restringir as ações de que um utilizador pode fazer com as informações de auditoria relativamente. No Azure, os utilizadores ou grupos de utilizadores podem ser atribuídos a funções (por exemplo, proprietário, Contribuidor, leitor ou uma função personalizada) para restringir as ações disponíveis relativamente a quaisquer recursos ou implementados soluções, incluindo a análise de registos.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-6-10"></a>NIST 800-53 controlo AU-6 (10)

#### <a name="audit-review-analysis-and-reporting--audit-level-adjustment"></a>Reveja, análises e relatórios de auditoria | Ajuste de nível de auditoria

**AU-6 (10)** organização ajusta o nível de revisão de auditoria, análise e relatórios no sistema informações quando existe uma alteração de risco com base nas informações de imposição da lei, intelligence ou outras fontes credível de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por ajustar o nível de revisão de auditoria, análise e relatórios para os recursos implementados de cliente (incluir aplicações, sistemas operativos, bases de dados e software) quando existe uma alteração de risco com base nas informações fornecidas por lei imposição, intelligence ou outras origens credível. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-7a"></a>AU-7.a do NIST 800-53 controlo

#### <a name="audit-reduction-and-report-generation"></a>Redução de auditoria e a geração de relatórios

**AU 7.a** o sistema de informações fornece uma redução de auditoria e a capacidade de geração de relatórios que suporte a pedido de auditoria revisão, análise e os requisitos e as investigações de após o facto de incidentes de segurança de relatórios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de análise de registos no OMS. Análise de registos fornece serviços de monitorização para OMS através da recolha de dados a partir dos recursos geridos para um repositório central. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-7b"></a>Controlo NIST 800-53 AU-7

#### <a name="audit-reduction-and-report-generation"></a>Redução de auditoria e a geração de relatórios

**AU 7** o sistema de informações fornece uma auditoria redução de capacidade e de relatório geração que não altera o conteúdo original ou tempo ordenação dos registos de auditoria.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de análise de registos no OMS. Análise de registos fornece serviços de monitorização para OMS através da recolha de dados a partir dos recursos geridos para um repositório central. O conteúdo e a hora ordenação dos registos de auditoria não são alterados quando recolhidos através da análise de registos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-7-1"></a>NIST 800-53 controlo AU-7 (1)

#### <a name="audit-reduction-and-report-generation--automatic-processing"></a>Redução de auditoria e a geração de relatórios | Processamento automático

**AU-7 (1)** o sistema de informações fornece a capacidade de processar registos de auditoria para eventos de interesse em [atribuição: campos definidos de organização auditoria nos registos de auditoria].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de análise de registos no OMS. Análise de registos fornece serviços de monitorização para OMS através da recolha de dados a partir dos recursos geridos para um repositório central. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. O Log Analytics inclui uma poderosa linguagem de consultas, para extrair os dados armazenados no repositório. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-8a"></a>AU-8.a do NIST 800-53 controlo

#### <a name="time-stamps"></a>Carimbos de data / hora

**AU 8.a** o sistema de informações utiliza os relógios de sistema interno para gerar carimbos de data / hora para registos de auditoria.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Recursos implementados por esta Blueprint do Azure, utilize os relógios de sistema interno para gerar os carimbos de data / hora para registos de auditoria. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-8b"></a>AU-8.b do NIST 800-53 controlo

#### <a name="time-stamps"></a>Carimbos de data / hora

**AU 8.b** o sistema de informações regista carimbos de data / hora para os registos de auditoria que podem ser mapeados para Hora Universal Coordenada (UTC) ou a hora de Greenwich (GMT) e cumpre [atribuição: granularidade definido de organização de medição de hora].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Recursos implementados por esta Blueprint do Azure, utilize os relógios de sistema interno para gerar os carimbos de data / hora para registos de auditoria. Carimbos de data / hora é registadas em UTC. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-8-1a"></a>(1) .a do NIST 800-53 controlo AU-8

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Os carimbos de hora | Sincronização com a origem de hora autoritativo

**.A AU-8 (1)** o sistema de informações compara os relógios de sistema de informações internas [atribuição: frequência definida de organização] com [atribuição: origem de hora de autoritativo definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Recursos implementados por esta Blueprint do Azure, utilize os relógios de sistema interno para gerar os carimbos de data / hora para registos de auditoria. Os relógios de sistema interno estão configurados para sincronização com uma origem de hora autoritativo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-8-1b"></a>(1) .b do NIST 800-53 controlo AU-8

#### <a name="time-stamps--synchronization-with-authoritative-time-source"></a>Os carimbos de hora | Sincronização com a origem de hora autoritativo

**AU-8 (1) .b** o sistema de informações sincroniza os relógios de sistema interno para a origem de hora autoritativo quando a diferença de tempo é superior ao [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Recursos implementados por esta Blueprint do Azure, utilize os relógios de sistema interno para gerar os carimbos de data / hora para registos de auditoria. Os relógios de sistema interno estão configurados para sincronização com uma origem de hora autoritativo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-9"></a>Controlo NIST 800-53 AU-9

#### <a name="protection-of-audit-information"></a>Proteção de informações de auditoria

**AU 9** o sistema de informações protege as ferramentas de auditoria e informações de auditoria de acesso não autorizado, alteração e eliminação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Controlos de acesso lógica são utilizados para proteger informações de auditoria e ferramentas dentro deste Blueprint Azure contra acesso não autorizado, alteração e eliminação. Azure Active Directory impõe o acesso lógico aprovado utilizando as associações de grupo baseada em funções. A capacidade para ver informações de auditoria e utilizar as ferramentas de auditoria pode ser limitada a utilizadores que exigem essas permissões. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-9-2"></a>NIST 800-53 controlo AU-9 (2)

#### <a name="protection-of-audit-information--audit-backup-on-separate-physical-systems--components"></a>Proteção de informações de auditoria | Cópia de segurança em sistemas físicos separados de auditoria / componentes

**AU-9 (2)** o sistema de informações efetua cópias de segurança registos de auditoria [atribuição: frequência definida de organização] para um sistema fisicamente diferente ou componente do sistema que o sistema ou componente a ser auditada.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o serviço de análise de registos no OMS. Implementado VMs e o Azure contas de armazenamento do diagnostics origens ligada à análise de registos e mantidas em separado da respetiva origem. Dados recolhidos pelo OMS no praticamente em tempo real. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-9-3"></a>NIST 800-53 controlo AU-9 (3)

#### <a name="protection-of-audit-information--cryptographic-protection"></a>Proteção de informações de auditoria | Proteção de criptografia

**AU-9 (3)** mecanismos criptográficos para proteger a integridade das ferramentas de auditoria e informações de auditoria de implementa o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o serviço de análise de registos no OMS. Análise de registos assegura que recebidos dados de uma origem fidedigna validando certificados e a integridade dos dados com a autenticação do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-9-4"></a>NIST 800-53 controlo AU-9 (4)

#### <a name="protection-of-audit-information--access-by-subset-of-privileged-users"></a>Proteção de informações de auditoria | Acesso por subconjunto de utilizadores com privilégios

**AU-9 (4)** organização autoriza o acesso à gestão da funcionalidade de auditoria apenas [atribuição: definido de organização subconjunto de utilizadores privilegiados].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Controlos de acesso lógica são utilizados para proteger informações de auditoria e ferramentas dentro deste Blueprint Azure contra acesso não autorizado, alteração e eliminação. Azure Active Directory impõe o acesso lógico aprovado utilizando as associações de grupo baseada em funções. A capacidade para ver informações de auditoria e utilizar as ferramentas de auditoria pode ser limitada a utilizadores que exigem essas permissões.
 |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-10"></a>Controlo NIST 800-53 AU-10

#### <a name="non-repudiation"></a>Não rejeição

**AU 10** o sistema de informações protege contra uma pessoa (ou processo agir em nome de um indivíduo) falsely negar ter efetuado [atribuição: ações organização-definido para ser abrangidos pelo não rejeição].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Capacidade de auditoria para este Blueprint do Azure é fornecida pelo Monitor do Azure e o serviço de análise de registos no OMS. Monitor do Azure fornece registos de auditoria de detalhado sobre a atividade associados a recursos implementados. Estas e registos de nível de SO são recolhidos através da análise de registos e armazenados no repositório de OMS. Estes registos continham registos detalhados de eventos do sistema de informações e podem ajudar a proteger contra não rejeição. Além disso, o acesso aos dados de registo é restringido através de controlo de acesso baseado em funções para impedir que unauthored modificação ou eliminação de dados de registo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-11"></a>Controlo NIST 800-53 AU-11

#### <a name="audit-record-retention"></a>Retenção do registo de auditoria

**AU 11** organização mantém os registos de auditoria para [atribuição: organização definidos pelo período de tempo consistente com a política de retenção de registos] para fornecer suporte para as investigações de após o facto de incidentes de segurança e para cumprir os regulamentares e requisitos de retenção de informações organizacionais.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o serviço de análise de registos no OMS. Análise de registos fornece serviços de monitorização para OMS através da recolha de dados a partir dos recursos geridos para um repositório central. Depois de recolhidos, os dados retidos durante um ano por configuração de análise de registos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-12a"></a>AU-12.a do NIST 800-53 controlo

#### <a name="audit-generation"></a>Geração de auditoria

**AU 12.a** o sistema de informações fornece a capacidade de geração de registos de auditoria para os eventos auditável definidos no AU 2 um. no [atribuição: os componentes do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os eventos auditados por esta Blueprint Azure incluem esses auditada por registos de atividade do Azure para recursos implementados, os registos de nível de SO, registos do Active Directory e os registos do servidor de SQL. Os clientes poderão selecionar eventos adicionais a serem auditados para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-12b"></a>AU-12.b do NIST 800-53 controlo

#### <a name="audit-generation"></a>Geração de auditoria

**AU 12.b** permite que o sistema de informações [atribuição: técnico definido pela organização ou funções] para selecionar os eventos auditável estão a ser auditadas ao componentes específicos do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso às funções de auditoria é restringido através de controlo de acesso baseado em funções no Azure e na máquina virtual ao nível do SO. A configuração de eventos selecionado para ser auditadas ao recursos implementados por este Blueprint Azure pode ser configurada por utilizadores com adequadas de autorização baseada em funções. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-au-12c"></a>AU-12.c do NIST 800-53 controlo

#### <a name="audit-generation"></a>Geração de auditoria

**AU 12.c** o sistema de informações gera registos de auditoria para os eventos definidos no AU 2.d. com o conteúdo definido no AU-3.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os eventos auditados por esta Blueprint Azure incluem esses auditada por registos de atividade do Azure para recursos implementados, os registos de nível de SO, registos do Active Directory e os registos do servidor de SQL. Os clientes poderão selecionar eventos adicionais a serem auditados para satisfazer as necessidades de missão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-12-1"></a>NIST 800-53 controlo AU-12 (1)

#### <a name="audit-generation--system-wide--time-correlated-audit-trail"></a>Geração de auditoria | Registo de auditoria de todo sistema / correlacionado de tempo

**AU-12 (1)** o sistema de informações compila registos de auditoria da [atribuição: os componentes do sistema de informações definidas pela organização] para um registo de auditoria de (lógico ou físico) de todo sistema que é o tempo-correlacionada com dentro [atribuição: organização definido pelo nível de tolerância para a relação entre os carimbos de data / hora de registos individuais no registo de auditoria].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o serviço de análise de registos no OMS. Análise de registos fornece serviços de monitorização para OMS através da recolha de dados a partir dos recursos geridos para um repositório central. Carimbos de data / hora de registo de auditoria não é alterados, assim, o registo de auditoria é correlacionado de tempo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-au-12-3"></a>NIST 800-53 controlo AU-12 (3)

#### <a name="audit-generation--changes-by-authorized-individuals"></a>Geração de auditoria | Alterações por pessoas autorizadas

**AU-12 (3)** o sistema de informações fornece a capacidade para [atribuição: indivíduos definido pela organização ou funções] para alterar a auditoria para ser efetuada no [atribuição: componentes do sistema de informações definidas pela organização] em [ Atribuição: critérios definidos de organização selecionável eventos] dentro [atribuição: limiares de tempo definido pelo organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso às funções de auditoria é restringido através de controlo de acesso baseado em funções no Azure e na máquina virtual ao nível do SO. A configuração de eventos selecionado para ser auditadas ao recursos implementados por este Blueprint Azure pode ser configurada por utilizadores com adequadas de autorização baseada em funções. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |
