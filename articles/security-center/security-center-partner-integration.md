---
title: "Integração de Parceiros no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento explica de que forma é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure."
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
ms.date: 04/28/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8dddfc8929ab1a0c44522ed2a2596e2c82e3987d
ms.contentlocale: pt-pt
ms.lasthandoff: 05/01/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Integração de Parceiros no Centro de Segurança do Azure
Este documento explica de que forma é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral e proporcionar uma experiência integrada no Azure, tirando, ao mesmo tempo, partido do Azure Marketplace para certificação e faturação dos parceiros.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Porquê implementar soluções de parceiros do Centro de Segurança?

Existem quatro principais motivos para aproveitar a integração de parceiros no Centro de Segurança:

- **Facilidade de implementação**: é muito mais fácil implementar uma solução de parceiros ao seguir a recomendação do Centro de Segurança. O processo de implementação pode ser totalmente automatizado com uma configuração e uma topologia de rede predefinidas ou os clientes podem escolher uma opção semiautomática, que lhes dê mais flexibilidade e personalização da configuração.
- **Deteções integradas**: os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Monitorização e Gestão de Estado de Funcionamento Unificada**: os eventos de estado de funcionamento integrados permitem aos clientes monitorizar todas as soluções de parceiros rapidamente. A gestão básica está disponível com acesso fácil à configuração avançada através da solução de parceiro.
- **Exportar para SIEM**: os clientes podem, agora, exportar todos os alertas do Centro de Segurança e dos parceiros no formato CEF para sistemas SIEM no local mediante a utilização do Microsoft Azure Log Integration (pré-visualização)


## <a name="what-partners-are-integrated-with-security-center"></a>Os parceiros são integrados no Centro de Segurança?
Atualmente, o Centro de Segurança pode ser integrado com os parceiros seguintes:

- Endpoint Protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)) 
- Firewall de Aplicações Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets), [App Gateway WAF](https://azure.microsoft.com/en-us/blog/azure-web-application-firewall-waf-generally-available/)) 
- Soluções de Firewall da Próxima Geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Avaliação de Vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) - pré-visualização)  

Ao longo do tempo, o Centro de Segurança irá expandir o número de parceiros dentro destas categorias atuais e adicionar novas categorias. 

## <a name="how-to-deploy-a-partner-solution"></a>Como implementar uma solução de parceiro?

Com base na configuração do seu ambiente do Azure e a política de segurança que definiu, o Centro de Segurança poderá recomendar que uma solução de parceiro seja implementada. A recomendação irá guiá-lo ao longo do processo de seleção e instalação de uma solução de parceiro. A experiência de implementação geral nesta altura pode variar de acordo com o tipo de solução e parceiro. Veja as ligações abaixo para obter mais informações:

- [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md)
- [Adicionar uma Firewall da Próxima Geração](security-center-add-next-generation-firewall.md)
- [Instalar o Endpoint Protection](security-center-install-endpoint-protection.md)
- [Avaliação de vulnerabilidades não instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>Como gerir soluções de parceiros?

Assim que tiver sido implementada uma solução de parceiro, pode ver informações sobre o estado de funcionamento da solução e efetuar tarefas de gestão básicas a partir do mosaico de solução do Parceiro no dashboard do Centro de Segurança principal. Para obter mais informações sobre como gerir soluções de parceiros no Centro de Segurança, leia [Monitoring partner solutions with Azure Security Center (Monitorizar soluções de parceiros com o Centro de Segurança do Azure)](security-center-partner-solutions.md).

![Integração de Parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a integrar soluções de parceiros no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de Segurança no Centro de Segurança do Azure](security-center-alerts-type.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

