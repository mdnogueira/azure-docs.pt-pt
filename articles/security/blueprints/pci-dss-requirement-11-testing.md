---
title: Azure Blueprint de processamento do pagamento - teste requisitos
description: Requisito de PCI DSS 11
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 9753853b-ad80-4be4-9416-2583b8fd2029
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 57429741afd2ffd16c09a0f1485cb1cfbdda5571
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="testing-requirements-for-pci-dss-compliant-environments"></a>Requisitos de conformidade de PCI DSS ambientes de teste 
## <a name="pci-dss-requirement-11"></a>Requisito de PCI DSS 11

**Testar regularmente os sistemas de segurança e processos**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Vulnerabilidades estão a ser detetadas continuamente por que pessoas e os investigadores e que está a ser introduzido por software novo. Componentes do sistema, processos e personalizadas de software devem ser testadas frequentemente para garantir que os controlos de segurança continuam para refletir uma alteração de ambiente.

## <a name="pci-dss-requirement-111"></a>Requisito de PCI DSS 11.1

**11.1** implementar os processos para testar a presença dos pontos de acesso sem fios (802.11) e detetar e identificar todos os autorizados e não autorizado pontos de acesso sem fios trimestralmente.

> [!NOTE]
> Métodos que podem ser utilizados no processo de incluem, mas não são análises de rede limitada a sem fios, inspections lógico/físico de componentes do sistema e infraestrutura, controlo de acesso à rede (NAC) ou IDS/IPS sem fios.
Qualquer métodos são utilizados, têm de ser suficientes para detetar e identificar os dispositivos autorizados e não autorizados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Azure não permitir ou permitir que as ligações sem fios no ambiente de rede do Azure. As equipas de segurança interna verifica regularmente a existência de sinais de sem fios rogue numa base trimestral e sinais de rogue são investigados e removidos. Os clientes não são permitidos para implementar a tecnologia sem fios no ambiente do Azure. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Redes sem fios e SNMP não são implementados na solução.|



### <a name="pci-dss-requirement-1111"></a>Requisito de PCI DSS 11.1.1

**11.1.1** manter um inventário dos pontos de acesso sem fios autorizado, incluindo uma justificação de negócio documentado.

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Redes sem fios e SNMP não são implementados na solução.|



### <a name="pci-dss-requirement-1112"></a>Requisito de PCI DSS 11.1.2

**11.1.2** implementar procedimentos de resposta a incidentes, no caso de pontos de acesso não autorizado são detetados.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 11.1](#pci-dss-requirement-11-1). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Redes sem fios e SNMP não são implementados na solução.|



## <a name="pci-dss-requirement-112"></a>Requisito de PCI DSS 11.2

**11.2** executar interno e análises de vulnerabilidade de rede externa, pelo menos, trimestralmente e após qualquer alteração significativa na rede (por exemplo, novas instalações de componente de sistema, as alterações na topologia de rede, as modificações de regra de firewall, produto atualizações). 

> [!NOTE]
> Podem ser combinados vários relatórios de análise para o processo de análise trimestral mostrar que todos os sistemas que foram analisados e todas as vulnerabilidades aplicáveis tem sido resolvidas. Documentação adicional poderá ser necessário para verificar a vulnerabilidades remediado não estão a ser resolvidos.
> Conformidade de PCI DSS inicial, não é necessário que os quatro trimestres de passar análises ser concluída se a assessor verifica 1) o resultado da verificação mais recente foi uma análise de transmissão, 2) a entidade tem de ser documentada políticas e procedimentos que requerem a análise trimestral e 3) tem foi resolvidas vulnerabilidades que anotou nos resultados da análise, conforme mostrado num re-scan(s). Durante anos subsequentes depois de rever iniciais PCI DSS, quatro trimestres de passar análises tem ocorreram.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Azure efetua análises de vulnerabilidade trimestral de internos e externos. São efetuadas verificações técnico qualificado. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi caneta testadas e vulnerabilidade analisados num esforço 'como está". Os resultados do teste de caneta podem ser duplicados através da utilização de ferramentas comuns, tais como nmap ou pentest tools.com. Os resultados do teste caneta irão fornecer a superfície de ataque inconclusivos, não existem itens exploitable. Além disso, [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecem informações de vulnerabilidade e remediação.|



### <a name="pci-dss-requirement-1121"></a>Requisito de PCI DSS 11.2.1

**11.2.1** efetuar análises de vulnerabilidade interno trimestral. Endereço vulnerabilidades e efetuar rescans para verificar que todas as vulnerabilidades de "alto risco" são resolvidas em conformidade com a vulnerabilidade da entidade classificação (por requisito 6.1). Tem de ser executadas verificações por parte do pessoal qualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure efetua análises de vulnerabilidades na infraestrutura subjacente no âmbito. Microsoft Azure implementa vulnerabilidade de análise em sistemas operativos de servidor, bases de dados e dispositivos de rede com as ferramentas de análise de vulnerabilidade adequado. As aplicações web do Azure são analisadas com adequado da indústria de soluções de análise. Análises de vulnerabilidade são efetuadas numa base trimestral.<br /><br />Rescans são executadas conforme necessário em relação a todos os sistemas, até que todas as vulnerabilidades "de alto risco" (conforme identificado no requisito 6.1) sejam resolvidas. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi caneta testadas e vulnerabilidade analisados num esforço 'como está". Os resultados do teste de caneta podem ser duplicados através da utilização de ferramentas comuns, tais como nmap ou pentest tools.com. Os resultados do teste caneta irão fornecer a superfície de ataque inconclusivos, não existem itens exploitable. Além disso, [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecem informações de vulnerabilidade e remediação.|



### <a name="pci-dss-requirement-1122"></a>Requisito de PCI DSS 11.2.2

**11.2.2** efetuar análises de vulnerabilidade externo trimestral, através de um aprovados análise fornecedor (ASV) aprovadas pela pagamento cartão da indústria segurança normas Council (PCI SSC). Efetue rescans conforme necessário, até que as análises de transmissão são alcançados. 

> [!NOTE]
> Análises de vulnerabilidade externo trimestral devem ser executados por um aprovados análise fornecedor (ASV), aprovadas pela pagamento cartão da indústria segurança normas Council (PCI SSC).
> Consulte o manual de programa ASV publicada no Web site da SSC de PCI para análise responsabilidades do cliente, a preparação de análise, etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure efetua análises externos para vulnerabilidades na infraestrutura subjacente no âmbito que seja acessível externamente. Análises são efetuadas por um aprovados analisar fornecedor (ASV).<br /><br />Microsoft Azure subscreve notificações de patch mensais MSRC/OSSC e verifica a existência de vulnerabilidades, pelo menos, trimestral. Vulnerabilidades identificadas são avaliadas e remediadas por linha cronológica estabelecida com base no nível de risco.<br /><br />Cada vulnerabilidade de segurança completa do trimestre direcionado análise contra componentes prioritários do ambiente do Microsoft Azure é efetuada para identificar vulnerabilidades de segurança. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Quando implementar o Contoso Webstore, os clientes de demonstração de responsáveis por efetuar trimestral vulnerabilidade externa analisa e rescans conforme necessário em todas as instâncias de PaaS no respetivo ambiente de dados de cardholder (CDE), utilizando um aprovados análise fornecedor (ASV) aprovadas pela Council de normas de segurança da indústria de cartão de pagamento.<br /><br />|



### <a name="pci-dss-requirement-1123"></a>Requisito de PCI DSS 11.2.3

**11.2.3** efetuar análises internos e externos e rescans conforme necessário, após qualquer alteração significativa. Tem de ser executadas verificações por parte do pessoal qualificado.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Os resultados são reportados intervenientes e remediação é controlada pela equipa de segurança do Azure através de fecho. Resultados do teste do Azure podem ser partilhados com clientes em contrato de confidencialidade. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os clientes são responsáveis por efetuar análises de vulnerabilidade internos e externos trimestral e rescans conforme necessário em todas as instâncias de PaaS no respetivo CDE. Análises devem ser efetuadas após alterações significativas no ambiente no âmbito.<br /><br />Análises tem de ser efetuadas por um ASV ou pessoal com independência organizacional.|



## <a name="pci-dss-requirement-113"></a>Requisito de PCI DSS 11.3

**11.3** implementar uma metodologia para testar a penetração que inclui o seguinte:
- Baseia-se no penetração aceite o setor abordagens (por exemplo, NIST SP800-115) de teste
- Inclui a cobertura de perímetro CDE todo e sistemas críticos
- Inclui o teste a partir de ambos os dentro e fora da rede
- Inclui o teste validar qualquer segmentação e controlos de redução de âmbito
- Define testes penetração de camada de aplicação para incluir, no mínimo, as vulnerabilidades listados no requisito 6.5
- Define a camada de rede penetração testes para incluir os componentes que suportam as funções de rede, bem como em sistemas operativos
- Inclui revisão e tendo em conta ameaças e vulnerabilidades teve nos últimos 12 meses
- Especifica a retenção de penetração testar os resultados e resultados de atividades de correção

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure valida serviços com terceiros penetração teste com base no OWASP (Abra o projeto Web aplicação segurança) principais dez testers CREST certificado a utilizar. Os resultados dos testes são controlados através de um registo de risco, que é auditado e consultar regularmente para garantir a conformidade para práticas de segurança. <br /><br />A Microsoft também utiliza agrupamento Red contra infraestrutura gerida pela Microsoft, serviços e aplicações. Não existem dados de cliente do fim é deliberadamente visados durante a vermelho agrupamento e o teste de penetração de site em direto. Os testes são em relação a infraestrutura do Microsoft Azure e plataformas, bem como aplicações da Microsoft e os dados. Nunca são direcionados para os inquilinos do cliente, aplicações e dados alojados no Azure.<br /><br />Microsoft Azure foi utilizada uma assessor independente para desenvolver um plano de avaliação do sistema e realize uma avaliação de controlos. As avaliações de controlos são efetuadas anual e os resultados são reportados as partes relevantes. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi caneta testadas e vulnerabilidade analisados num esforço 'como está". Os resultados do teste de caneta podem ser duplicados através da utilização de ferramentas comuns, tais como nmap ou pentest tools.com. Os resultados do teste caneta irão fornecer a superfície de ataque inconclusivos, não existem itens exploitable. Além disso, [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecem informações de vulnerabilidade e remediação.|



### <a name="pci-dss-requirement-1131"></a>Requisito de PCI DSS 11.3.1

**11.3.1** executar *externo* penetração anual, pelo menos e depois de qualquer infraestrutura significativa ou a atualização da aplicação ou a modificação de teste (tais como uma atualização do sistema operativo, uma subtipo de rede adicionado à ambiente, ou um servidor web adicionado ao ambiente).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 11.3](#pci-dss-requirement-11-3). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi caneta testadas e vulnerabilidade analisados num esforço 'como está". Os resultados do teste de caneta podem ser duplicados através da utilização de ferramentas comuns, tais como nmap ou pentest tools.com. Os resultados do teste caneta irão fornecer a superfície de ataque inconclusivos, não existem itens exploitable. Além disso, [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecem informações de vulnerabilidade e remediação.|



### <a name="pci-dss-requirement-1132"></a>Requisito de PCI DSS 11.3.2

**11.3.2** executar *interno* penetração anual, pelo menos e depois de qualquer infraestrutura significativa ou a atualização da aplicação ou a modificação de teste (tais como uma atualização do sistema operativo, uma subtipo de rede adicionado à ambiente, ou um servidor web adicionado ao ambiente).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure contratos com assessors independentes para efetuar testes penetração do limite do Microsoft Azure. Equipa Red exercícios regularmente também são executados e resultados utilizada para efetuar melhoramentos de segurança. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore foi caneta testadas e vulnerabilidade analisados num esforço 'como está". Os resultados do teste de caneta podem ser duplicados através da utilização de ferramentas comuns, tais como nmap ou pentest tools.com. Os resultados do teste caneta irão fornecer a superfície de ataque inconclusivos, não existem itens exploitable. Além disso, [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecem informações de vulnerabilidade e remediação.|



### <a name="pci-dss-requirement-1133"></a>Requisito de PCI DSS 11.3.3

**11.3.3** vulnerabilidades exploitable encontradas durante o teste de penetração são corrigidas e testar é repetida para verificar as correções.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Foram estabelecidos procedimentos para monitorizar os componentes de plataforma do Microsoft Azure para vulnerabilidades de segurança conhecidos. <br /><br /><br /><br />Cada vulnerabilidade de segurança completa do trimestre direcionado análise contra componentes prioritários do ambiente de produção do Azure é efetuada para identificar vulnerabilidades de segurança. Os resultados são reportados intervenientes e remediação é controlada pela equipa do através de fecho. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), que fornecem informações de vulnerabilidade e remediação, foram utilizadas para se certificar de que todos os problemas pendentes foram remediados para a demonstração de Contoso Webstore CDE.|



### <a name="pci-dss-requirement-1134"></a>Requisito de PCI DSS 11.3.4

**11.3.4** se segmentação é utilizada para isolar o CDE outras redes, efetuar testes de penetração anual, pelo menos e depois quaisquer alterações aos controlos/métodos de segmentação para verificar se os métodos de segmentação estão operacionais e eficaz, e Isole todos os sistemas de fora do âmbito de sistemas no CDE.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Foram estabelecidos procedimentos para monitorizar os componentes de plataforma do Microsoft Azure para vulnerabilidades de segurança conhecidos. <br /><br /><br /><br />Cada vulnerabilidade de segurança completa do trimestre direcionado análise contra componentes prioritários do ambiente de produção do Azure é efetuada para identificar vulnerabilidades de segurança. Os resultados são reportados intervenientes e remediação é controlada pela equipa do através de fecho. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), que fornecem informações de vulnerabilidade e remediação, foram utilizadas para se certificar de que todos os problemas pendentes foram remediados para a demonstração de Contoso Webstore CDE.|



### <a name="pci-dss-requirement-11341"></a>Requisito de PCI DSS 11.3.4.1

**11.3.4.1** *requisitos adicionais para fornecedores de serviços só:* se for utilizada a segmentação, confirme âmbito de PCI DSS efetuando penetração testar no segmentação controla pelo menos a cada seis meses e depois das alterações controlos/métodos de segmentação.

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 11.3.4](#pci-dss-requirement-11-3-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations), que fornecem informações de vulnerabilidade e remediação, foram utilizadas para se certificar de que todos os problemas pendentes foram remediados para a demonstração de Contoso Webstore CDE.|



## <a name="pci-dss-requirement-114"></a>Requisito de PCI DSS 11.4

**11.4** utilize técnicas de deteção de intrusão e/ou de prevenção de intrusões para detetar e/ou impedir intrusions na rede. Monitorizar todo o tráfego no perímetro do ambiente de dados cardholder, bem como em pontos de crítico no ambiente de dados de cardholder e alerta pessoal para os compromissos de suspeito.
Manter todos os deteção de intrusão e motores de prevenção, linhas de base e assinaturas atualizados.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure efetua a análise em tempo real de eventos dentro do respetivo ambiente operacional e sistemas de IDS geram quase em tempo real alertas sobre eventos que potencialmente podem comprometer o sistema. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore é um serviço de PaaS e deteção de intrusão de rede e a prevenção de fazer referência a responsabilidade do Azure. [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecer intrusões alertas e remediação.|



## <a name="pci-dss-requirement-115"></a>Requisito de PCI DSS 11.5

**11.5** implementar um mecanismo de deteção de alteração (por exemplo, integridade de ficheiro ferramentas de monitorização) ao pessoal de alerta para modificação não autorizada (incluindo adições, modificações e eliminações) de ficheiros de sistema críticos, de ficheiros de configuração ou de conteúdo ficheiros; e configure o software para efetuar comparações de ficheiro crítica, pelo menos, semanalmente. 

> [!NOTE]
> Para efeitos de deteção de alteração, a ficheiros críticos, normalmente, são aqueles que não altere regularmente, mas a modificação das quais pode indicar um compromisso do sistema ou o risco de compromisso. Mecanismos de deteção de alteração, tais como produtos de monitorização de integridade de ficheiro, geralmente, são fornecidos previamente configurados com ficheiros críticos para o sistema operativo relacionado. Outros ficheiros críticos, como os de aplicações personalizadas, tem de ser avaliados e definidos pela entidade (ou seja, o fornecedor de serviço ou merchant).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure mantém e notifica os clientes de potenciais eventos que podem ter impacto na segurança e as alterações ou a disponibilidade dos serviços através de um Dashboard de serviço online. As alterações para os compromissos de segurança e obrigações de segurança dos clientes do Microsoft Azure são atualizadas no Web site do Microsoft Azure de uma forma atempada.<br /><br />Instalação ou alterações ao software no Microsoft Azure, ambiente de produção é restringido aos autorizado o técnico de administração e procedimentos de gestão de alteração de forma. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Demonstração de contoso Webstore é um serviço de PaaS e deteção de alteração foi implementada com o OMS. Para obter mais informações, consulte [orientações de PCI - Pre-Installed OMS soluções](payment-processing-blueprint.md#oms-solutions).<br /><br />|



### <a name="pci-dss-requirement-1151"></a>Requisito de PCI DSS 11.5.1

**11.5.1** implemente um processo para responder a todos os alertas gerados pela solução de deteção de alteração.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Regras de eventos de monitorização do Azure fornecem um maior nível de monitorização para operações de alto risco e de recursos. Dispositivos de rede gerida do Azure são monitorizados para conformidade com as normas de segurança estabelecida. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os alertas de Contoso Webstore para as alterações são fornecidos pela implementação OMS. Para obter mais informações, consulte [orientações de PCI - Pre-Installed OMS soluções](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|



## <a name="pci-dss-requirement-116"></a>Requisito de PCI DSS 11.6

**11.6** Certifique-se de que as políticas de segurança e de procedimentos operacionais para a monitorização de segurança e testar estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Os alertas de Contoso Webstore para as alterações são fornecidos pela implementação OMS. Para obter mais informações, consulte [orientações de PCI - Pre-Installed OMS soluções](payment-processing-blueprint.md#oms-solutions).<br /><br /><br /><br />|




