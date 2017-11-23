---
title: "FedRAMP Blueprint da automatização do Azure - sistema e informações de integridade"
description: "Aplicações Web para FedRAMP - sistema e informações de integridade"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 2ff2778b-2c37-41b5-a39c-6594b3e3b10b
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 1dc6805a5a1f610f06ce58bd4bd644346436294e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-information-integrity-si"></a>Sistema e informações de integridade (TAMA)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-si-1"></a>Controlo NIST 800-53 TAMA-1

#### <a name="system-and-information-integrity-policy-and-procedures"></a>Sistema e política de integridade de informações e procedimentos

**TAMA-1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de integridade do sistema e informações de endereços do objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação do sistema e política de integridade de informações e sistema associado e controlos de integridade de informações; analisa e atualiza a política de integridade de sistema e informações atual [atribuição: frequência definida de organização]; e procedimentos de integridade de sistema e informações [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de integridade de sistema e informações de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-2a"></a>TAMA-2.a do NIST 800-53 controlo

#### <a name="flaw-remediation"></a>Falha de remediação

**TAMA 2.a** organização identifica, relatórios e corrige falhas de sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução OMS automatização e controlo para controlar o estado das atualizações para as máquinas de virtuais do Windows implementadas nesta arquitetura. A partir do OMS dashboard, o mosaico de gestão de atualizações apresenta o estado de remediação de falha de todos os servidores de Windows implementados. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-2b"></a>TAMA-2.b do NIST 800-53 controlo

#### <a name="flaw-remediation"></a>Falha de remediação

**TAMA 2.b** organização os testes de atualizações de software e firmware relacionados com a remediação de falha de eficácia e potenciais efeitos secundários antes da instalação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por atualizações relacionados com a remediação de falha para eficácia e os possíveis efeitos secundários antes da instalação em recursos implementados de cliente de teste. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-2c"></a>TAMA-2.c do NIST 800-53 controlo

#### <a name="flaw-remediation"></a>Falha de remediação

**TAMA 2.c** organização instala atualizações de software e firmware relevantes de segurança na [atribuição: organização definidos pelo período de tempo] da versão das atualizações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais do Windows implementadas por esta Blueprint do Azure estão configuradas por predefinição para receber as atualizações automáticas do serviço de atualização do Windows. Esta solução também implementa a solução de automatização do OMS & controlo através do qual as implementações de atualização podem ser criadas para implementar patches nos servidores do Windows quando for necessário. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-2d"></a>TAMA-2.d do NIST 800-53 controlo

#### <a name="flaw-remediation"></a>Falha de remediação

**TAMA 2.d** organização incorpora remediação falha o processo de gestão de configuração organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por incluindo remediação de falha na gestão de configuração. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-2-1"></a>TAMA do controlo NIST 800-53-2 (1)

#### <a name="flaw-remediation--central-management"></a>Falha de remediação | Gestão centralizada

**TAMA-2 (1)** organização gere centralmente o processo de remediação de falha.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução OMS automatização e controlo para controlar o estado das atualizações para as máquinas de virtuais do Windows implementadas nesta arquitetura. A partir do OMS dashboard, o mosaico de gestão de atualizações apresenta o estado de remediação de falha de todos os servidores de Windows implementados. Implementações de atualização podem ser criadas para implementar patches nos servidores do Windows quando for necessário. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-2-2"></a>TAMA do controlo NIST 800-53-2 (2)

#### <a name="flaw-remediation--automated-flaw-remediation-status"></a>Falha de remediação | Estado de remediação de falha automatizada

**TAMA-2 (2)** organização emprega mecanismos automatizados [atribuição: frequência definida de organização] para determinar o estado dos componentes de sistema de informações em relação a remediação de falha.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução OMS automatização e controlo para controlar o estado das atualizações para as máquinas de virtuais do Windows implementadas nesta arquitetura. Em cada computador Windows gerido, é feita uma análise duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o estado foi alterado e, se for esse o caso, é iniciada uma análise de conformidade. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-2-3a"></a>Controlar o NIST 800-53 .a (3) TAMA-2

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Falha de remediação | Tempo para remediar falhas / Benchmarks para ações Corretivas

**TAMA-2 (3) .a** organização mede o tempo entre a identificação de falha e a remediação de falha.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por falhas de remediação no recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-2-3b"></a>Controlar o NIST 800-53 .b (3) TAMA-2

#### <a name="flaw-remediation--time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>Falha de remediação | Tempo para remediar falhas / Benchmarks para ações Corretivas

**TAMA-2 (3) .b** organização estabelece [atribuição: benchmarks definido de organização] para colocar as ações corretivas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de testes de desempenho de nível empresarial para processos de remediação de falha. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-3a"></a>TAMA-3.a do NIST 800-53 controlo

#### <a name="malicious-code-protection"></a>Proteção de código malicioso

**TAMA 3.a** a organização utiliza mecanismos de proteção de código malicioso em informações sistema entrada e saída pontos para detetar e eradicate código malicioso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-3b"></a>TAMA-3.b do NIST 800-53 controlo

#### <a name="malicious-code-protection"></a>Proteção de código malicioso

**TAMA 3.b** mecanismos de proteção de código malicioso de atualizações de organização sempre que os novos lançamentos estão disponíveis em conformidade com a política de gestão de configuração organizacional e procedimentos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. Esta extensão está configurada para atualizar automaticamente ambas as assinaturas antimalware da proteção e de motor como versão fique disponível. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-3c"></a>TAMA-3.c do NIST 800-53 controlo

#### <a name="malicious-code-protection"></a>Proteção de código malicioso

**TAMA 3.c** organização configura mecanismos de proteção de código malicioso para efetuar verificações periódicas do sistema de informações [atribuição: frequência definida de organização] e análises em tempo real de ficheiros a partir de origens externas em [seleção (um ou mais informações); ponto final; pontos de entrada/saída de rede] como os ficheiros são transferidos, abriram, ou executado de acordo com a política de segurança organizacional; e [seleção (um ou mais): bloquear código malicioso; colocar em quarentena código malicioso; alerta de envio para o administrador; [Atribuição: ação definida por organização]] em resposta a deteção de código malicioso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. Esta extensão está configurada para efetuar análises em tempo real e periódicos (semanal), automaticamente ambas as assinaturas antimalware da proteção e de motor de atualização e efetuar ações de remediação automática. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-3d"></a>TAMA-3.d do NIST 800-53 controlo

#### <a name="malicious-code-protection"></a>Proteção de código malicioso

**TAMA 3.d** organização endereços a receção de falsos positivos durante a deteção de código malicioso e eradication e o impacto potencial resultante na disponibilidade do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger contra código malicioso. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-3-1"></a>TAMA do controlo NIST 800-53-3 (1)

#### <a name="malicious-code-protection--central-management"></a>Proteção de código malicioso | Gestão centralizada

**TAMA-3 (1)** organização gere centralmente mecanismos de proteção de código malicioso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. Azure OMS proporciona uma capacidade centralizada para rever o estado atual da solução antimalware. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-3-2"></a>TAMA do controlo NIST 800-53-3 (2)

#### <a name="malicious-code-protection--automatic-updates"></a>Proteção de código malicioso | Atualizações automáticas

**TAMA-3 (2)** o sistema de informações atualiza automaticamente os mecanismos de proteção de código malicioso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. Esta extensão está configurada para atualizar automaticamente ambas as assinaturas antimalware da proteção e de motor como versão fique disponível. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-3-7"></a>TAMA do controlo NIST 800-53-3 (7)

#### <a name="malicious-code-protection--nonsignature-based-detection"></a>Proteção de código malicioso | Deteção baseada em nonsignature

**TAMA-3 (7)** o sistema de informações implementa mecanismos de deteção baseada em nonsignature código malicioso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa as proteções de antimalware baseada no anfitrião para todos os Windows as máquinas virtuais implementadas implementadas através da extensão da máquina virtual de Antimalware da Microsoft. Esta extensão está configurada para executar a deteção heurística. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4a"></a>TAMA-4.a do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.a** organização monitoriza o sistema de informações para detetar ataques e indicadores de potenciais ataques em conformidade com [atribuição: definida organização monitorizar objetivos]; e locais não autorizados, rede e remoto ligações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a análise de registos e a solução de auditoria e segurança do OMS. Esta solução fornece uma vista abrangente de postura de segurança, ataques e indicadores de potenciais ataques. O dashboard de auditoria e segurança fornece aprofundadas alto nível sobre o estado de segurança dos recursos implementados utilizando os dados disponíveis em soluções de OMS implementadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4b"></a>TAMA-4.b do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.b** organização identifica utilização não autorizada do sistema de informações através de [atribuição: métodos e técnicas definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a solução de auditoria e segurança do OMS. O domínio de identidade e acesso fornece um dashboard com uma descrição geral das informações identidade do Estado do sistema, incluindo o número de tentativas falhadas para iniciar sessão e o número atual de contas que é registado no. As informações disponíveis neste dashboard podem ajudar a identificação de atividade suspeita potencial. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4c"></a>TAMA-4.c do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.c** organização implementa dispositivos monitorização estrategicamente no sistema informações para recolher informações essenciais determinado de organização; e nas localizações ad hoc no sistema controlar a tipos específicos de transações de interesse para a organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a análise de registos e a solução de auditoria e segurança do OMS. O dashboard de auditoria e segurança fornece aprofundadas alto nível sobre o estado de segurança dos recursos implementados utilizando os dados disponíveis em soluções do OMS implementadas, incluindo aprofundadas sobre o sistema de operativo VM dados de monitorização. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4d"></a>TAMA-4.d do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.d** a organização protege as informações obtidas a partir da monitorização de intrusões ferramentas a partir do acesso não autorizado, alteração e eliminação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Controlos de acesso lógica são utilizados para proteger as informações de monitorização dentro deste Blueprint Azure contra acesso não autorizado, alteração e eliminação. Azure Active Directory impõe o acesso lógico aprovado utilizando as associações de grupo baseada em funções. A capacidade para ver informações de monitorização e utilizar as ferramentas de monitorização pode ser limitada a utilizadores que exigem essas permissões. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4e"></a>TAMA-4.e do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.e** A organização heightens o nível de atividade de monitorização, sempre que exista uma indicação de maior risco para operações organizacionais e recursos, pessoas, outras organizações ou Nation com base nas informações de imposição da lei, de sistema de informações informações de Intelligence ou outras origens credível de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4f"></a>TAMA-4.f do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.f** organização obtém opinião legal, relativamente à sistema informações monitorização de atividades de acordo com as federais leis, as ordens executivo, diretivas, políticas ou regulamentos aplicáveis.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-4g"></a>TAMA-4.g do NIST 800-53 controlo

#### <a name="information-system-monitoring"></a>Informações de monitorização de sistema

**TAMA 4.g** organização fornece [atribuição: informações de monitorização de sistema de informações definidas pela organização] para [atribuição: técnico definido pela organização ou funções] [seleção (um ou mais): conforme necessário; [Atribuição: frequência definida de organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-1"></a>TAMA do controlo NIST 800-53-4 (1)

#### <a name="information-system-monitoring--system-wide-intrusion-detection-system"></a>Monitorização de sistema de informações | Sistema de deteção de intrusão de todo sistema

**TAMA-4 (1)** organização estabelece ligação e configura as ferramentas de deteção de intrusões individuais para um sistema de deteção de intrusão de todo o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-2"></a>TAMA do controlo NIST 800-53-4 (2)

#### <a name="information-system-monitoring--automated-tools-for-real-time-analysis"></a>Monitorização de sistema de informações | Ferramentas automatizadas para análise em tempo real

**TAMA-4 (2)** organização emprega automatizadas ferramentas para suportar quase em tempo real análise de eventos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a análise de registos e várias soluções OMS, incluindo a solução de auditoria e segurança. Análise de registos fornece quase em tempo real análise de eventos em recursos implementados. Soluções OMS fornece uma vista abrangente de postura de segurança em vários domínios de solução. OMS fornece informações sobre o estado de segurança de recursos implementados utilizando os dados disponíveis em soluções de OMS implementadas. OMS pode ser configurado para gerar alertas com base nos critérios definidos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-4"></a>NIST 800-53 controlo TAMA-4 (4)

#### <a name="information-system-monitoring--inbound-and-outbound-communications-traffic"></a>Monitorização de sistema de informações | Tráfego de comunicações de entrada e saída

**TAMA-4 (4)** o sistema de informações monitoriza o tráfego de entrada e saída comunicações [atribuição: frequência definida de organização] para atividades invulgares ou não autorizadas ou condições.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-5"></a>TAMA do controlo NIST 800-53-4 (5)

#### <a name="information-system-monitoring--system-generated-alerts"></a>Monitorização de sistema de informações | Alertas gerados pelo sistema

**TAMA-4 (5)** os alertas de sistema de informações [atribuição: técnico definido pela organização ou funções] quando ocorrem as indicações seguintes de comprometimento ou potencial comprometimento: [atribuição: indicadores de comprometimento definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa várias soluções OMS, incluindo a solução de auditoria e segurança. Análise de registos fornece quase em tempo real análise de eventos em recursos implementados. Soluções OMS fornece uma vista abrangente de postura de segurança em vários domínios de solução. OMS pode ser configurado para gerar alertas com base nos critérios definidos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-11"></a>TAMA do controlo NIST 800-53-4 (11)

#### <a name="information-system-monitoring--analyze-communications-traffic-anomalies"></a>Monitorização de sistema de informações | Analisar as comunicações tráfego anomalias

**TAMA-4 (11)** organização analisa o tráfego de comunicações de saída no limite externo do sistema de informações e selecionado [atribuição: interior definido de organização pontos no sistema (por exemplo, sub-redes, subsistemas)] para Deteção de anomalias.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela análise anomalias de tráfego de comunicações para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-14"></a>TAMA do controlo NIST 800-53-4 (14)

#### <a name="information-system-monitoring--wireless-intrusion-detection"></a>Monitorização de sistema de informações | Deteção de intrusão sem fios

**TAMA-4 (14)** a organização utiliza um sistema de deteção de intrusões sem fios para identificar dispositivos sem fios rogue e para detetar ataques tenta e potenciais compromissos por danos no sistema de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Nenhum hardware controlados pelo cliente, incluindo dispositivos sem fios, é permitido nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure monitoriza regularmente sinais de sem fios rogue numa base trimestral tal como explicado 18 de AC. <br /> Microsoft Azure implementa este controlo em nome dos clientes de PaaS e IaaS. |


 ### <a name="nist-800-53-control-si-4-16"></a>TAMA do controlo NIST 800-53-4 (16)

#### <a name="information-system-monitoring--correlate-monitoring-information"></a>Monitorização de sistema de informações | Correlacionar informações de monitorização

**TAMA-4 (16)** organização está correlacionada com informações a partir de ferramentas, empregue se encontram em todo o sistema de informações de monitorização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa a análise de registos e várias soluções OMS, incluindo a solução de auditoria e segurança. OMS fornece informações sobre o estado de segurança de recursos implementados utilizando os dados disponíveis em soluções de OMS implementadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-18"></a>TAMA do controlo NIST 800-53-4 (18)

#### <a name="information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitorização de sistema de informações | Analisar o tráfego de / Covert Exfiltration

**TAMA-4 (18)** a organização analisa o tráfego de comunicações de saída no limite externo do sistema de informações (ou seja, perímetro system) e em [atribuição: interior definido de organização pontos no sistema (por exemplo, subsistemas, sub-redes)] para detetar exfiltration covert de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por analisar o tráfego de comunicações para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-19"></a>TAMA do controlo NIST 800-53-4 (19)

#### <a name="information-system-monitoring--individuals-posing-greater-risk"></a>Monitorização de sistema de informações | Indivíduos que está a representar maior risco

**TAMA-4 (19)** implementa a organização [atribuição: monitorização adicional definida para organização] de indivíduos que tenham sido identificados pelo [atribuição: origens definido de organização] como está a representar um maior nível de risco.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização indivíduos que apresentam um risco superior. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-20"></a>TAMA do controlo NIST 800-53-4 (20)

#### <a name="information-system-monitoring--privileged-users"></a>Monitorização de sistema de informações | Utilizadores com privilégios

**TAMA-4 (20)** implementa a organização [atribuição: monitorização adicional definida para organização] de utilizadores privilegiados.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela monitorização de utilizadores privilegiados. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-22"></a>TAMA do controlo NIST 800-53-4 (22)

#### <a name="information-system-monitoring--unauthorized-network-services"></a>Monitorização de sistema de informações | Serviços de rede não autorizado

**TAMA-4 (22)** o sistema de informações Deteta os serviços de rede que não autorizados ou não aprovados por [atribuição: processos de autorização ou aprovação definido para organização] e [seleção (um ou mais): auditorias; alertas [atribuição: técnico definido pela organização ou funções]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por detetar os serviços de rede não autorizado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-23"></a>TAMA do controlo NIST 800-53-4 (23)

#### <a name="information-system-monitoring--host-based-devices"></a>Monitorização de sistema de informações | Dispositivos baseados no anfitrião

**TAMA-4 (23)** implementa a organização [atribuição: definido de organização anfitrião mecanismos baseados na monitorização] em [atribuição: componentes do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure recolhe dados de monitorização de recursos implementados, incluindo dados de capacidades de monitorização baseada no anfitrião. O Microsoft Monitoring Agent está instalado em todas as máquinas virtuais do Windows para recolher dados de monitorização utilizados pela análise de registos e outras soluções OMS. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-4-24"></a>TAMA do controlo NIST 800-53-4 (24)

#### <a name="information-system-monitoring--indicators-of-compromise"></a>Monitorização de sistema de informações | Indicadores de comprometimento

**TAMA-4 (24)** o sistema de informações Deteta, recolhe, distribui e utiliza indicadores de comprometimento.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por detetar, recolha, distribuir e utilizar indicadores de comprometimento aos recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-5a"></a>TAMA-5.a do NIST 800-53 controlo

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de segurança, Advisories e diretivas

**TAMA 5.a** a organização recebe alertas de segurança do sistema de informações, advisories e as diretivas de [atribuição: organizações externas definido de organização] numa base contínua.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão de alertas de segurança, advisories e diretivas para os recursos implementados de cliente (incluir aplicações, sistemas operativos, bases de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-5b"></a>TAMA-5.b do NIST 800-53 controlo

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de segurança, Advisories e diretivas

**TAMA 5.b** organização gera alertas de segurança interna, advisories e diretivas como considerar necessário.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão de alertas de segurança, advisories e diretivas para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-5c"></a>TAMA-5.c do NIST 800-53 controlo

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de segurança, Advisories e diretivas

**TAMA 5.c** organização disseminates alertas de segurança, advisories e diretivas para: [seleção (um ou mais): [atribuição: técnico definido pela organização ou funções]; [Atribuição: elementos definido de organização dentro da organização]; [Atribuição: organizações externas definido de organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão de alertas de segurança, advisories e diretivas para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-5d"></a>TAMA-5.d do NIST 800-53 controlo

#### <a name="security-alerts-advisories-and-directives"></a>Alertas de segurança, Advisories e diretivas

**TAMA 5.d** organização implementa as diretivas de segurança em conformidade com estabelecida de tempo de fotogramas ou notifica a organização emissora do grau de não conformidade.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão de alertas de segurança, advisories e diretivas para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-5-1"></a>TAMA do controlo NIST 800-53-5 (1)

#### <a name="security-alerts-advisories-and-directives--automated-alerts-and-advisories"></a>Alertas de segurança, Advisories e diretivas | Alertas automatizadas e Advisories

**TAMA-5 (1)** organização emprega automatizados mecanismos para tornar segurança alerta e advisory informações disponíveis sobre a organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão de alertas de segurança, advisories e diretivas para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-6a"></a>TAMA-6.a do NIST 800-53 controlo

#### <a name="security-function-verification"></a>Verificação da função de segurança

**TAMA 6.a** o sistema de informações verifica o funcionamento correto do [atribuição: funções de segurança definido para organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para os recursos implementados de cliente (incluir aplicações, sistemas operativos, bases de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-6b"></a>NIST 800-53 controlo TAMA-6. b

#### <a name="security-function-verification"></a>Verificação da função de segurança

**TAMA-6. b** o sistema de informações executa esta verificação [seleção (um ou mais): [atribuição: Estados de transição de organização definidos pelo sistema]; após o comando por utilizador com privilégios adequados; [Atribuição: frequência definida de organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-6c"></a>NIST 800-53 controlo TAMA-6. c

#### <a name="security-function-verification"></a>Verificação da função de segurança

**TAMA-6. c** notifica o sistema de informações [atribuição: técnico definido pela organização ou funções] dos testes de verificação de segurança falhada.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-6d"></a>TAMA-6.d do NIST 800-53 controlo

#### <a name="security-function-verification"></a>Verificação da função de segurança

**TAMA 6.d** o sistema de informações [seleção (um ou mais): encerra o sistema de informações; reiniciar o sistema de informações; [Atribuição: definido de organização alternativas ações]] Quando são detetadas anomalias.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela verificação de função de segurança para os recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-7"></a>Controlo NIST 800-53 TAMA-7

#### <a name="software-firmware-and-information-integrity"></a>Informações de integridade, Firmware e software

**TAMA 7** a organização utiliza as ferramentas de verificação de integridade para detetar as alterações não autorizadas a [atribuição: software definida pelo organização, de firmware e informações].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows oferece a validação de integridade de ficheiro em tempo real, a proteção e recuperação de ficheiros de sistema de núcleos que são instaladas como parte do Windows ou atualizações do sistema Windows autorizadas através da capacidade de proteção de recursos do Windows (WRP). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-7-1"></a>TAMA do controlo NIST 800-53-7 (1)

#### <a name="software-firmware-and-information-integrity--integrity-checks"></a>Informações de integridade, Firmware e software | Verificações de integridade

**TAMA-7 (1)** o sistema de informações executa uma verificação de integridade de [atribuição: software definida pelo organização, de firmware e informações] [seleção (um ou mais): no arranque; em [atribuição: os Estados de transição definido pela organização ou eventos de segurança relevantes]; [Atribuição: frequência definida de organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows oferece a validação de integridade de ficheiro em tempo real, a proteção e recuperação de ficheiros de sistema de núcleos que são instaladas como parte do Windows ou atualizações do sistema Windows autorizadas através da capacidade de proteção de recursos do Windows (WRP). WRP permite que a verificação de integridade em tempo real. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-7-2"></a>TAMA do controlo NIST 800-53-7 (2)

#### <a name="software-firmware-and-information-integrity--automated-notifications-of-integrity-violations"></a>Informações de integridade, Firmware e software | Notificações automatizadas de violações de integridade

**TAMA-7 (2)** organização emprega automatizadas ferramentas que fornecem a notificação para o [atribuição: técnico definido pela organização ou funções] após deteção discrepâncias durante a verificação de integridade.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows oferece a validação de integridade de ficheiro em tempo real, a proteção e recuperação de ficheiros de sistema de núcleos que são instaladas como parte do Windows ou atualizações do sistema Windows autorizadas através da capacidade de proteção de recursos do Windows (WRP).  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-7-5"></a>TAMA do controlo NIST 800-53-7 (5)

#### <a name="software-firmware-and-information-integrity--automated-response-to-integrity-violations"></a>Informações de integridade, Firmware e software | Resposta automática a violações de integridade

**TAMA-7 (5)** o sistema de informações automaticamente [seleção (um ou mais): encerra o sistema de informações; reiniciar o sistema de informações; implementa [atribuição: as proteções de segurança definido para organização]] quando são violações de integridade detetado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por automaticamente a responder a violações de integridade no recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-7-7"></a>TAMA do controlo NIST 800-53-7 (7)

#### <a name="software-firmware-and-information-integrity--integration-of-detection-and-response"></a>Informações de integridade, Firmware e software | Integração de deteção e a resposta

**TAMA-7 (7)** organização incorpora a deteção não autorizado [atribuição: definido de organização alterações relevantes de segurança para o sistema de informações] para a capacidade de resposta a incidentes organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger integridade software e informações de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-7-14"></a>TAMA do controlo NIST 800-53-7 (14)

#### <a name="software-firmware-and-information-integrity--binary-or-machine-executable-code"></a>Informações de integridade, Firmware e software | Código executável binário ou de computador

**TAMA-7 (14)** organização proíbe a utilização de código binary ou o executável de máquina de origens com limitada ou nenhuma garantia e sem o aprovisionamento de código de origem e fornece exceções para o requisito de código de origem apenas para o missão convincentes / requisitos operacionais e com aprovação de authorizing oficial.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Nível empresarial sistema e informações integridade procedimentos o cliente poderão estabelecer requisitos para obter o código de origem de código executável binário ou de computador de algumas origens. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-8a"></a>TAMA-8.a do NIST 800-53 controlo

#### <a name="spam-protection"></a>Proteção de spam

**TAMA 8.a** os organização emprega spam proteção mecanismos em informações sistema entrada e saída pontos para detetar e executar ações em mensagens não solicitadas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem não existem servidores de correio implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-8b"></a>TAMA-8.b do NIST 800-53 controlo

#### <a name="spam-protection"></a>Proteção de spam

**TAMA 8.b** organização atualizações spam proteção mecanismos de quando os novos lançamentos estão disponíveis em conformidade com a política de gestão de configuração organizacional e procedimentos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem não existem servidores de correio implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-8-1"></a>TAMA do controlo NIST 800-53-8 (1)

#### <a name="spam-protection--central-management"></a>Proteção de spam | Gestão centralizada

**TAMA-8 (1)** organização gere centralmente mecanismos de proteção de spam.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem não existem servidores de correio implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-si-8-2"></a>TAMA do controlo NIST 800-53-8 (2)

#### <a name="spam-protection--automatic-updates"></a>Proteção de spam | Atualizações automáticas

**TAMA-8 (2)** o sistema de informações atualiza automaticamente os mecanismos de proteção de spam.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem não existem servidores de correio implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-10"></a>TAMA-10 do NIST 800-53 controlo

#### <a name="information-input-validation"></a>Validação de entrada de informações

**TAMA 10** o sistema de informações verifica a validade do [atribuição: entradas de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela validação de entrada de informações para os recursos implementados de cliente (incluir aplicações, sistemas operativos, bases de dados e software). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-11a"></a>TAMA-11.a do NIST 800-53 controlo

#### <a name="error-handling"></a>Processamento de erros

**TAMA 11.a** o sistema de informações gera mensagens de erro que fornecem informações necessárias para ações corretivas sem revelando informações que podem ser forem exploradas por adversários qualificados.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Utilizam os recursos implementados por este Blueprint Azure comerciais sistemas operativos e aplicações de software. Este software utiliza melhores práticas industriais para assegurar a informações confidenciais não são reveladas nas mensagens de erro. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-11b"></a>TAMA-11.b do NIST 800-53 controlo

#### <a name="error-handling"></a>Processamento de erros

**TAMA 11.b** o sistema de informações mensagens de erro de revela só [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Utilizam os recursos implementados por este Blueprint Azure comerciais sistemas operativos e aplicações de software. Este software utiliza melhores práticas industriais para fornecer as mensagens de erro que são adequadas no contexto de utiliza a receber a mensagem. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-12"></a>Controlo NIST 800-53 TAMA-12

#### <a name="information-handling-and-retention"></a>Processamento de informações e retenção

**TAMA-12** processa e mantém informações do sistema de informações e saída de informações do sistema de acordo com as leis federais aplicáveis, a organização executivo ordens, diretivas, políticas, normas, normas, e requisitos operacionais.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por processar e a reter informações recursos implementados de cliente (para incluir aplicações, sistemas operativos, bases de dados e software) e saída de informações desses recursos. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-si-16"></a>Controlo NIST 800-53 TAMA-16

#### <a name="memory-protection"></a>Proteção de memória

**TAMA-16** implementa o sistema de informações [atribuição: as proteções de segurança definido para organização] para proteger a respetiva memória da execução do código não autorizado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows tem proteções para impedir a execução do código em localizações de memória restrita: sem executar (NX), prazos de esquema de espaço de endereço (ASLR) e a prevenção de execução de dados (DEP). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |
