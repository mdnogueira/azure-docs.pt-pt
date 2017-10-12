---
title: "Segurança de Dados da Solução de Segurança e Auditoria do Operations Management Suite | Microsoft Docs"
description: "Este documento explica como os dados são geridos e guardados de forma segura no Operations Management Suite Security e na Solução de Segurança e Auditoria."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Segurança de dados da solução de Segurança e Auditoria do Operations Management Suite
Para ajudar os clientes prevenir, a detetar e a responder a ameaças, a [Solução de Segurança e Auditoria do Operations Management Suite  (OMS)](operations-management-suite-overview.md) recolhe e processa dados sobre os recursos, incluindo:

* Registo de eventos de segurança
* Eventos de Rastreio de Eventos para o Windows (ETW)
* Eventos de auditoria do AppLocker
* Registo de Firewall do Windows
* Eventos de Análise de Ameaças Avançada
* Resultados da avaliação de linha de base
* Resultados da avaliação de antimalware
* Resultados da avaliação de atualização/correção
* Fluxos Syslogs explicitamente ativos no agente

Estamos extremamente empenhados em proteger a privacidade e segurança destes dados. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.
Este artigo explica como os dados são geridos e guardados de forma segura na Solução de Segurança e Auditoria do OMS.

## <a name="data-sources"></a>Origens de dados
A Solução de Segurança e Auditoria do OMS analisa os dados das Máquinas Virtuais e computadores físicos onde o Agente do OMS está instalado. A Solução de Segurança e Auditoria do OMS pode recolher informações de configuração sobre eventos de segurança, tais como eventos do Windows, registos de auditoria, registos do IIS e mensagens syslog. Os exemplos destes dados incluem: tipo e versão do sistema operativo, processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino.  

## <a name="data-protection"></a>Proteção de dados
**Segregação de dados**: os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. 

**Acesso a dados**: para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, o pessoal técnico da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços. Respeitamos os [Termos de Serviço Online da Microsoft](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), os quais estipulam que a Microsoft não utilizará Dados do Cliente ou derivará informações dos mesmos para qualquer fim de publicidade ou comercial semelhante. para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, o pessoal técnico da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os próprios dados.

**Utilização de dados**: a Microsoft utiliza os padrões e as informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção. Fazemo-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de Privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [!NOTE]
> A localização de dados é configurada ao nível da área de trabalho do OMS, durante a criação da área de trabalho, que faz parte do processo de configuração inicial de Segurança e Auditoria do OMS.
> 
> 

## <a name="see-also"></a>Consultar também
Através deste documento aprendeu como os dados são geridos e salvaguardados no OMS. Para obter mais informações sobre a solução de segurança e auditoria do OMS, consulte:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)

