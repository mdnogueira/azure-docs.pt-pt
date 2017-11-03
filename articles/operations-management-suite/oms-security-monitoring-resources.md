---
title: "A monitorização de recursos no Operations Management Suite segurança e a solução de auditoria | Microsoft Docs"
description: "Este documento ajuda-o a utilizar a segurança do OMS e capacidades para monitorizar os recursos e identificar problemas de segurança de auditoria."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitorizar os recursos no Operations Management Suite segurança e a solução de auditoria
Este documento ajuda-o a utilizar capacidades de auditoria e segurança do OMS para monitorizar os recursos e identificar problemas de segurança.

## <a name="what-is-oms"></a>O que é o OMS?
Microsoft Operations Management Suite (OMS) é a solução de gestão de IT que ajuda a gerir e proteger no local e a infraestrutura de nuvem de baseada na nuvem da Microsoft. Para mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Monitorização de recursos
Sempre que possível, irá querer impedir que os incidentes de segurança em primeiro lugar a acontecer. No entanto, é impossível impedir que todos os incidentes de segurança. Quando um incidente de segurança ocorrem, terá de se certificar de que o seu impacto é minimizado.  Existem três recomendações críticas que podem ser utilizadas para minimizar o número e o impacto de incidentes de segurança:

* Avalie regularmente vulnerabilidades no seu ambiente.
* Verificar regularmente a todos os sistemas de computadores e dispositivos de rede para garantir que têm todas as correções mais recentes instaladas.
* Verificar regularmente a todos os registos e mecanismos de registo, incluindo os registos de eventos do sistema operativo, registos específicos da aplicação e os registos de sistema de deteção de intrusões.

Segurança do OMS e auditoria solução permite a TI ativamente monitorizar todos os recursos, o que podem ajudar a minimiza o impacto de incidentes de segurança. Auditoria e segurança do OMS tem domínios de segurança que podem ser utilizados para monitorizar os recursos. Os domínios de segurança fornece acesso rápido a uma opções para a monitorização de segurança seguintes domínios vai ser abordados mais detalhes:

* Avaliação de malware
* Avaliação de atualização
* Identidade e Acesso

> [!NOTE]
> Para obter uma descrição geral de todas estas opções, leia o artigo [introdução ao Operations Management Suite segurança e a solução de auditoria](oms-security-getting-started.md).
> 
> 

### <a name="monitoring-system-protection"></a>Proteção do sistema de monitorização
Uma defesa numa abordagem de profundidade, cada camada de proteção é importante para o estado geral de segurança do seu elemento. Computadores com ameaças detetadas e os computadores com proteção insuficiente são apresentados no mosaico de avaliação de Malware em domínios de segurança. Ao utilizar as informações sobre a avaliação de Malware, pode identificar um plano para aplicar a proteção para os servidores que precisem dele. Para aceder a esta opção, siga os passos abaixo:

1. No dashboard principal do **Microsoft Operations Management Suite** , clique em mosaico de **Segurança e Auditoria**.
   
    ![Auditoria e segurança](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. No **auditoria e segurança** dashboard, clique em **Antimalware avaliação** em **segurança domínios**. O **Antimalware avaliação** dashboard aparece conforme mostrado abaixo:

![Avaliação de malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Pode utilizar o **avaliação de Malware** dashboard para identificar os seguintes problemas de segurança:

* **Ameaças ativas**: computadores comprometidas e que tenham ameaças ativas no sistema.
* **Remediado ameaças**: foram remediadas, mas as ameaças de computadores que foram comprometidos.
* **Assinatura desatualizada**: computadores que têm a proteção de software maligno ativada mas a assinatura está desatualizada.
* **Sem proteção em tempo real**: computadores que não tenham antimalware instalado.

### <a name="monitoring-updates"></a>monitorização de atualizações
Aplicar as atualizações mais recentes de segurança é a melhor prática de segurança e deve ser incorporado na sua estratégia de gestão de atualização. Serviço Microsoft Monitoring Agent (HealthService.exe) lê as informações de atualização de computadores monitorizados e, em seguida, envia estas informações atualizadas para o serviço do OMS na nuvem para processamento. O serviço Microsoft Monitoring Agent está configurado como um serviço automático e que deve ser sempre em execução no computador de destino.

![monitorização de atualizações](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Lógica é aplicada aos dados de atualização e o serviço em nuvem regista os dados. Se forem encontradas atualizações em falta, estas são apresentadas as **atualizações** dashboard. Pode utilizar o **atualizações** dashboard para trabalhar com atualizações em falta e desenvolver um plano para aplicá-las para os servidores que precisam delas. Siga os passos abaixo para aceder a **atualizações** dashboard:

1. No dashboard principal do **Microsoft Operations Management Suite** , clique em mosaico de **Segurança e Auditoria**.
2. No **auditoria e segurança** dashboard, clique em **avaliação de atualização** em **segurança domínios**. O dashboard de atualização é apresentado como mostrado abaixo:

![avaliação de atualização](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Neste dashboard pode efetuar uma avaliação de atualização para compreender o estado atual dos seus computadores e resolver as ameaças mais críticas. Utilizando o **críticas ou de atualizações de segurança** mosaico, os administradores de TI vão conseguir aceder a informações detalhadas sobre as atualizações que estão em falta conforme mostrado abaixo:

![resultado da pesquisa](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este relatório incluem informações críticas que podem ser utilizadas para identificar o tipo de ameaça este sistema é vulnerável a, que inclui os artigos da Microsoft KB associados com a atualização de segurança e a Bulletin MS que tenha mais detalhes sobre a vulnerabilidade.

### <a name="monitoring-identity-and-access"></a>Identidade e acesso de monitorização
Com os utilizadores trabalharem a partir de qualquer lugar, utilizando vários dispositivos e aceder a uma grande quantidade de aplicações na nuvem e no local, é imperativo que as credenciais estão protegidas. Ataques de roubo de credenciais são aquelas em que um atacante obtiver inicialmente acesso às credenciais de um utilizador normal para aceder a um sistema dentro da rede. Muitas vezes, este ataque inicial é apenas uma forma de obter acesso à rede, o objetivo único detetar contas de privilégio. 

Os atacantes e irão permanecer na rede, utilizando as ferramentas para extrair as credenciais de sessões de outras contas de início de sessão iniciada livremente disponíveis. Dependendo da configuração do sistema, estas credenciais podem ser extraídas sob a forma de hashes, permissões ou palavras-passe do mesmo texto simples.  

> [!NOTE]
> as máquinas que são expostas diretamente à Internet verá várias tentativas falhadas que tenta iniciar sessão utilizando todos os tipos de conhecidos nomes de utilizador (por exemplo, administrador). Na maioria dos casos trata OK se os nomes de utilizador bem conhecidos não são utilizados e a palavra-passe é suficientemente segura.
> 
> 

É possível identificar estes intrusos antes de poderem comprometer uma conta de privilégios. Pode tirar partido **solução de auditoria e de segurança do OMS** monitor identidade e acesso. Siga os passos abaixo para aceder a **identidades e acessos** dashboard:

1. No **Microsoft Operations Management Suite** clique em dashboard principal de segurança e auditoria mosaico.
2. No **auditoria e segurança** dashboard, clique em **identidades e acessos** em **segurança domínios**. O **identidades e acessos** dashboard aparece conforme mostrado abaixo:

![identidade e acesso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Como parte da sua estratégia de monitorização regular, tem de incluir a monitorização de identidade. Administrador de TI deve ter um aspeto se existir um nome de utilizador válido específico que tenha várias tentativas. Isto poderá indicar um atacante que obteve o nome de utilizador real e tente força bruta ou de uma ferramenta automática que utiliza hard-coded palavra-passe que expirou.

Permitir este dashboard que as TI identificem rapidamente potenciais ameaças relacionadas com a identidade e acesso a recursos da empresa. É determinado importante também identificar tendências potenciais, por exemplo no mosaico de inícios de sessão ao longo do tempo, pode ver ao longo do período de tempo quantas vezes foi efetuada uma tentativa de início de sessão falhadas. Neste caso o computador **servidor de ficheiros** recebido 35 tentativas de início de sessão. Pode explorar mais detalhes sobre este computador ao clicar no mesmo. 

![obter mais detalhes](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

O relatório gerado para este computador proporciona detalhes importantes sobre este padrão. Reparado que o **conta** coluna dá-lhe a conta de utilizador que foi utilizada para tentar aceder ao sistema, o **TIMEGENERATED** coluna dá-lhe o intervalo de tempo qual foi efetuada a tentativa e de **LOGONTYPENAME** coluna dá-lhe a localização onde foi efetuada esta tentativa. Se estes tentativas foram executadas localmente no sistema por um programa, o **processo** coluna seria que mostra o nome do processo. Em cenários em que a tentativa de início de sessão é proveniente de um programa, já tem o nome de processo disponível e agora pode efetuar mais investigação no sistema de destino.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a utilizar a segurança do OMS e auditar a solução para monitorizar os seus recursos. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Introdução ao Operations Management Suite segurança e a solução de auditoria](oms-security-getting-started.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)

