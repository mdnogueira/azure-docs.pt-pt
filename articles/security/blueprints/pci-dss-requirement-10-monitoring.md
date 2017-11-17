---
title: "Azure Blueprint de processamento do pagamento - requisitos de monitorização"
description: Requisito de PCI DSS 10
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 5fa1d17e68ce04b1f67081479518279be6cca099
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Requisitos de monitorização para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-10"></a>Requisito de PCI DSS 10

**Controlar e monitorizar o acesso de todos os recursos de rede e a dados de cardholder**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Mecanismos de registo e a capacidade para controlar as atividades dos utilizadores são fundamental impedir, detetar ou minimizar o impacto de um compromisso de dados. A presença de registos em todos os ambientes permite controlo detalhado, alertas e análise quando algo correrem mal. Determinar a causa de um compromisso é muito difícil, se não impossível, sem os registos de atividade do sistema.

## <a name="pci-dss-requirement-101"></a>Requisito de PCI DSS 10.1

**10.1** registos de auditoria de implementar para ligar todos os acessos a componentes do sistema para cada utilizador individual.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure restringe o acesso a ferramentas administrativas e diagnóstico para a empregados autorizados com responsabilidade tarefa relevantes. Microsoft Azure restringe o acesso privilegiado para as ferramentas utilizadas no ambiente de produção com base em, pelo menos, princípios de privilégio. Microsoft Azure regista e mantém um registo de acesso de utilizador individuais todos os componentes de sistema do Microsoft Azure no ambiente de plataforma.<br /><br />Componentes de plataforma do Microsoft Azure (incluindo o SO, CloudNet, recursos de infraestrutura e assim sucessivamente) estão configurados para iniciar sessão e recolher eventos de segurança. Atividade de administrador na plataforma Microsoft Azure é registada. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem registo de um vasto conjunto de todos os sistema e a atividade do utilizador (registo, incluindo CHD). Para obter mais informações, consulte [orientações de PCI - registo](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>Requisito de PCI DSS 10.2

**10.2** automatizadas de implementar os registos de auditoria para todos os componentes de sistema para reconstrua os seguintes eventos:
- **10.2.1** todos os acessos de utilizador individuais aos dados cardholder
- **10.2.2** todas as ações executadas por qualquer pessoa com privilégios administrativos de raiz ou
- **10.2.3** acesso a todos os registos de auditoria
- **10.2.4** tentativas de acesso lógico inválido
- **10.2.5** utilização e as alterações a mecanismos de identificação e autenticação — incluindo, mas não limitado a criação de novas contas e elevação de privilégios — e todas as alterações, adições ou eliminações para contas com raiz ou administrativas privilégios
- **10.2.6** inicialização, parar, ou colocar em pausa dos registos de auditoria
- **10.2.7** criação e eliminação de objetos de nível do sistema

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure restringe o acesso a ferramentas administrativas e diagnóstico para a empregados autorizados com responsabilidade tarefa relevantes. Microsoft Azure restringe o acesso privilegiado para as ferramentas utilizadas no ambiente de produção com base em, pelo menos, princípios de privilégio. Microsoft Azure regista e mantém um registo de acesso de utilizador individuais todos os componentes de sistema do Microsoft Azure no ambiente de plataforma.<br /><br />Componentes de plataforma do Microsoft Azure (incluindo o SO, CloudNet, recursos de infraestrutura e assim sucessivamente) estão configurados para iniciar sessão e recolher eventos de segurança. Atividade de administrador na plataforma Microsoft Azure é registada. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem registo de um vasto conjunto de todas as atividades de utilizador e de sistema, incluindo registo CHD. Para obter mais informações, consulte [orientações de PCI - registo](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>Requisito de PCI DSS 10.3

**10.3** registar, pelo menos, as seguintes entradas de registo de auditoria para todos os componentes de sistema para cada evento:
- **10.3.1** identificação do utilizador
- **10.3.2** tipo de evento
- **10.3.3** data e hora
- **10.3.4** indicação êxito ou falha
- **10.3.5** origem do evento
- **10.3.6** identidade ou nome de dados afetados, o componente do sistema ou o recurso

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure estabelecida procedimentos para sincronizar os servidores e dispositivos de rede no ambiente do Microsoft Azure com os servidores de hora NTP Stratum 1 sincronizadas para Global posicionamento System (GPS) satélites. Sincronização, é automaticamente executada a cada cinco minutos. Microsoft Azure é responsável por assegurar que os anfitriões corretamente hora de sincronização. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Regista o Contoso Webstore identificação do utilizador, tipo de evento, carimbo de data hora, eventos de falha de êxito, a origem de evento e o nome do recurso conforme exigido pelo controlo 10.3.|



## <a name="pci-dss-requirement-104"></a>Requisito de PCI DSS 10.4

**10.4** utilizando a tecnologia de sincronização de hora, sincronizar todos os relógios de sistema críticos e as horas e certifique-se de que o seguinte está implementado para adquirir, a distribuição e o armazenamento de tempo. 
> [!NOTE]
> Um exemplo de tecnologia de sincronização de hora é o protocolo NTP (Network Time).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure estabelecida procedimentos para sincronizar os servidores e dispositivos de rede no ambiente do Microsoft Azure com os servidores de hora NTP Stratum 1 sincronizadas para Global posicionamento System (GPS) satélites. Sincronização, é automaticamente executada a cada cinco minutos. Microsoft Azure é responsável por assegurar que os anfitriões corretamente hora de sincronização. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Sincronização de hora para o serviço de PaaS é efetuada pelo Azure.|



### <a name="pci-dss-requirement-1041"></a>Requisito de PCI DSS 10.4.1

**10.4.1** sistemas críticos tem a hora correta e consistente.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Sincronização de hora para o serviço de PaaS é efetuada pelo Azure.|



### <a name="pci-dss-requirement-1042"></a>Requisito de PCI DSS 10.4.2

**10.4.2** tempo os dados estão protegidos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Sincronização de hora para o serviço de PaaS é efetuada pelo Azure.|



### <a name="pci-dss-requirement-1043"></a>Requisito de PCI DSS 10.4.3

**10.4.3** as definições de hora são recebidas a partir de origens de hora aceites por da indústria.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.4](#pci-dss-requirement-10-4). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Sincronização de hora para o serviço de PaaS é efetuada pelo Azure.|



## <a name="pci-dss-requirement-105"></a>Requisito de PCI DSS 10.5

**10.5** registos de auditoria segura, de modo que não podem ser alteradas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Ferramentas de IDS e de FIM são implementadas no ambiente do Microsoft Azure. Microsoft Azure utiliza EWS para suportar a análise em tempo real de eventos dentro do respetivo ambiente operacional. MAs e OBJETIVOS geram quase em tempo real alertas sobre eventos que potencialmente podem comprometer o sistema. <br /><br />O registo de eventos de segurança, de utilizador e de serviço (registos de servidores web, os registos do servidor FTP e assim sucessivamente) está ativado e mantido centralmente. Azure restringe o acesso aos registos de auditoria para a empregados autorizados com base nas responsabilidades de trabalho. Registos de eventos estão arquivados na infraestrutura de arquivo segura do Azure e são mantidos durante 180 dias. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>Requisito de PCI DSS 10.5.1

**10.5.1** os registos de visualização de limite de auditoria com necessidade relacionadas com a tarefa.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>Requisito de PCI DSS 10.5.2

**10.5.2** proteger ficheiros de registo de auditoria de alterações não autorizadas.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>Requisito de PCI DSS 10.5.3

**10.5.3** retomadas rapidamente fazer cópias de segurança os ficheiros de registo de auditoria para um servidor de registo centralizado ou suportes de dados que é difícil alter.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>Requisito de PCI DSS 10.5.4

**10.5.4** escrever os registos para tecnologias de acesso externo para um dispositivo de servidor ou o suporte de dados de registo seguro, centralizada, interno.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>Requisito de PCI DSS 10.5.5

**10.5.5** utilizar software de monitorização ou deteção de alteração de integridade de ficheiro nos registos para garantir que os dados de registo existentes não podem ser alterados sem gerar alertas (embora novos dados a serem adicionados não devem causar um alerta).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.5](#pci-dss-requirement-10-5). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece para auditoria de todos os elementos à OMS. Fazer uma cópia até uma origem externa pode ser efetuada pela [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>Requisito de PCI DSS 10.6

**10.6** rever registos e eventos de segurança de todos os componentes de sistema identificar anomalias ou uma atividade suspeita.
 
> [!NOTE]
> Registo de recolha, analisar e alertas ferramentas podem ser utilizadas para cumprir este requisito.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Ferramentas de IDS e de FIM são implementadas no ambiente do Microsoft Azure. Microsoft Azure utiliza EWS para suportar a análise em tempo real de eventos dentro do respetivo ambiente operacional. MAs e OBJETIVOS geram quase em tempo real alertas sobre eventos que potencialmente podem comprometer o sistema. <br /><br />O registo de eventos de segurança, de utilizador e de serviço (registos de servidores web, os registos do servidor FTP e assim sucessivamente) está ativado e mantido centralmente. Azure restringe o acesso aos registos de auditoria para a empregados autorizados com base nas responsabilidades de trabalho. Registos de eventos estão arquivados na infraestrutura de arquivo segura do Azure e são mantidos durante 180 dias. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorizar, comunicar e impedir anomalias. [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornece uma vista consolidada consistente de recomendações para todos os seus recursos do Azure.|



### <a name="pci-dss-requirement-1061"></a>Requisito de PCI DSS 10.6.1

**10.6.1** diariamente, pelo menos, reveja o seguinte:
- Todos os eventos de segurança
- Registos de todos os componentes de sistema que armazenarem, processam ou transmitem CHD e/ou SAD
- Registos de todos os componentes de sistema críticos
- Registos de todos os componentes do sistema que efetuam funções de segurança (por exemplo, firewalls, sistemas de intrusões/sistemas-prevenção de deteção de intrusão (IDS/IPS), servidores de autenticação, servidores de redirecionamento de comércio eletrónico e assim sucessivamente) e servidores.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorizar, comunicar e impedir anomalias. [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornece uma vista consolidada consistente de recomendações para todos os seus recursos do Azure.|



### <a name="pci-dss-requirement-1062"></a>Requisito de PCI DSS 10.6.2

**10.6.2** rever os registos de todos os outros componentes do sistema em periodicamente a estratégia da organização risco e políticas de gestão, conforme determinado pela anuais avaliação de risco da organização.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" para [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorizar, comunicar e impedir anomalias. [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornece uma vista consolidada consistente de recomendações para todos os seus recursos do Azure.|



### <a name="pci-dss-requirement-1063"></a>Requisito de PCI DSS 10.6.3

**10.6.3** siga exceções e anomalias identificadas durante o processo de revisão.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Consulte a secção "Microsoft Azure" [requisito 10.6](#pci-dss-requirement-10-6). |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) para monitorizar, comunicar e impedir anomalias. [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornece uma vista consolidada consistente de recomendações para todos os seus recursos do Azure.|



## <a name="pci-dss-requirement-107"></a>Requisito de PCI DSS 10.7

**10.7** Reter histórico do registo de auditoria para, pelo menos, um ano, com um mínimo de três meses imediatamente disponíveis para análise (por exemplo, online, arquivado ou que possam ser restaurado da cópia de segurança).

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure mantém os registos de auditoria durante um ano, com os mais recentes 3 meses imediatamente acessíveis através do respetivo portal interno. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem registo de um vasto conjunto de todos os sistema e a atividade do utilizador (registo, incluindo CHD). Para obter mais informações, consulte [orientações de PCI - registo](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>Requisito de PCI DSS 10.8

**10.8** **requisitos adicionais para fornecedores de serviços só:** implemente um processo para uma deteção atempada e relatórios de falhas de sistemas de controlo de segurança críticas, incluindo mas não limitado a falha de:
- Firewalls
- OS IDS/IPS
- FIM
- Software antivírus
- Controlos de acesso físico
- Controlos de acesso lógico
- Mecanismos de registo de auditoria
- Controlos de segmentação (se utilizado) 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.



**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utiliza EWS para suportar a análise em tempo real de eventos dentro do respetivo ambiente operacional. MAs e OBJETIVOS geram quase em tempo real alertas sobre eventos que potencialmente podem comprometer o sistema. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem registo de um vasto conjunto de todos os sistema e a atividade do utilizador (registo, incluindo CHD). Para obter mais informações, consulte [orientações de PCI - registo](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>Requisito de PCI DSS 10.8.1

**10.8.1** **requisitos adicionais para fornecedores de serviços só:** responder a falhas de quaisquer controlos de segurança críticas em tempo útil. Os processos para responder a falhas em controlos de segurança tem de incluir:
- Funções de segurança a restaurar
- Identificar e documentar a duração (data e hora de início terminar) da falha de segurança
- Identificar e documentar cause(s) de falha, incluindo a causa raiz e documentar remediação necessária para a causa de raiz de endereço
- Identificar e corrigir quaisquer problemas de segurança que arose durante a falha
- Efetuar uma avaliação de risco para determinar se são necessárias devido a falha de segurança mais ações
- A implementação de controlos para impedir a causa da falha de ocorrer - a retomar a monitorização dos controlos de segurança 

> [!NOTE]
> Este requisito é uma melhor prática até 31 de Janeiro de 2018, após o qual torna-se um requisito.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utiliza EWS para suportar a análise em tempo real de eventos dentro do respetivo ambiente operacional. MAs e OBJETIVOS geram quase em tempo real alertas sobre eventos que potencialmente podem comprometer o sistema. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore tem registo de um vasto conjunto de todos os sistema e a atividade do utilizador (registo, incluindo CHD). Para obter mais informações, consulte [orientações de PCI - registo](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>Requisito de PCI DSS 10.9

**10.9** Certifique-se de que as políticas de segurança e de procedimentos operacionais para a monitorização de todo o acesso a recursos de rede e dados cardholder estão documentados em utilização e conhecidos para todas as partes afetadas.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece um caso de utilização e uma descrição sobre como o CHD é gerido e protegido.|




