---
title: "Integrar soluções de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Saiba como é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 42cbc442d03cdca04d380d05d9e904355476099e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. As vantagens incluem:

- **Implementação simplificada**: o Centro de Segurança oferece aprovisionamento integrado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidades, o Centro de Segurança pode aprovisionar o agente necessário nas suas máquinas virtuais e para aplicações de firewall, o Centro de Segurança pode assegurar grande parte da configuração de rede necessária.
- **Deteções integradas**: os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Gestão e monitorização do estado de funcionamento unificadas**: os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções de parceiros rapidamente. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem:

- Proteção de ponto final ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Windows Defender e System Center Endpoint Protection [SCEP])
- Firewall de aplicações Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) e [Gateway de Aplicação do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Soluções de firewall de próxima geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Avaliação de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

A experiência de integração da proteção de ponto final pode variar de acordo com a solução. A tabela seguinte inclui mais detalhes sobre a experiência de cada solução:

| Endpoint Protection               | Plataformas                             | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft Antimalware)                  | Windows Server 2016                   | Não, Incorporado no SO           | Sim                       |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 | Através de Extensão                | Sim                       |
| Trend Micro – Todas as versões         | Família Windows Server                 | Através de Extensão                | Sim                       |
| Symantec v12.1.1100+                     | Família Windows Server                 | Não                           | Sim                        |
| MacAfee                           | Família Windows Server                 | Não                           | Não                        |
| Kaspersky                         | Família Windows Server                 | Não                           | Não                        |
| Sophos                            | Família Windows Server                 | Não                           | Não                        |



## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Também pode ligar outras origens de dados de segurança, incluindo:

- Azure AD Identity Protection
- Computadores em execução no local ou noutras clouds
- Solução de segurança que suporte o Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

Após a implementação, pode ver informações sobre o estado de funcionamento da solução de segurança do Azure integrada e efetuar tarefas de gestão básicas. Também pode ligar outros tipos de origens de dados de segurança, tais como alertas do Azure Active Directory Identity Protection e registos de firewall no Common Event Format (CEF). No dashboard do Centro de Segurança, selecione Soluções de segurança.

### <a name="connected-solutions"></a>Soluções ligadas

A secção **Soluções ligadas** inclui soluções de segurança que estão ligadas atualmente ao Centro de Segurança e informações sobre o estado de funcionamento de cada solução.  

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>Soluções detetadas

A secção **Soluções detetadas** mostram todas as soluções que foram adicionadas através do Azure. Também apresenta todas as soluções que o Centro de Segurança lhe sugere que ligue ao mesmo.

![Soluções detetadas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

O Centro de Segurança deteta automaticamente outras soluções de segurança em execução no Azure. Isto inclui soluções do Azure, como o [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), bem como soluções de parceiros em execução no Azure. Para integrar estas soluções no Centro de Segurança, selecione **LIGAR**.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de Segurança](security-center-planning-and-operations-guide.md)
* [Ligar o Microsoft Advanced Threat Analytics ao Centro de Segurança do Azure](security-center-ata-integration.md)
* [Ligar o Azure Active Directory Identity Protection ao Centro de Segurança do Azure](security-center-aadip-integration.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Monitor partner solutions with Security Center](security-center-partner-solutions.md) (Monitorizar soluções de parceiros com o Centro de Segurança). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue Azure Security](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
