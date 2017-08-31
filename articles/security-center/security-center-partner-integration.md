---
title: "Integração de parceiros no Centro de Segurança do Azure | Microsoft Docs"
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
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: pt-pt
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Integração de parceiros no Centro de Segurança do Azure

Neste artigo, descrevemos de que forma é que o Centro de Segurança do Azure se integra com parceiros, para o ajudar a melhorar a segurança geral. O Centro de Segurança oferece uma experiência integrada no Azure e tira partido do Azure Marketplace para certificação de parceiros e faturação.

> [!NOTE] 
> A partir de junho de 2017, o Centro de Segurança vai passar a utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, veja [Azure Security Center platform migration](security-center-platform-migration.md) (Migração da Plataforma do Centro de Segurança do Azure). As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Porquê implementar soluções de parceiros do Centro de Segurança?

Os quatro principais motivos para aproveitar a integração de parceiros no Centro de Segurança são:

- **Facilidade de implementação**. é muito mais fácil implementar uma solução de parceiros ao seguir a recomendação do Centro de Segurança. O processo de implementação pode ser totalmente automatizado através de uma configuração e de uma topologia de rede predefinidas. Em alternativa, os clientes podem escolher uma opção semiautomatizada para obter mais flexibilidade e personalização.
- **Deteções integradas**. Os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Monitorização de estado de funcionamento e gestão unificadas**. Os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções do parceiro de forma rápida. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.
- **Exportar para SIEM**. Os clientes podem exportar todos os alertas do Centro de Segurança e dos parceiros no Common Event Format (CEF) para sistemas de Informações de Segurança e Gestão de Eventos (SIEM) no local através da integração de registos do Azure (pré-visualização).


## <a name="partners-that-integrate-with-security-center"></a>Parceiros que se integram com o Centro de Segurança

Atualmente, o Centro de Segurança pode ser integrado com estas soluções:

- Proteção de ponto final ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec e [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](https://docs.microsoft.com/azure/security/azure-security-antimalware) [Microsoft Antimalware para Serviços Cloud e Máquinas Virtuais do Azure]) 
- Firewall de aplicações Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) e [Gateway de Aplicação do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Soluções de firewall de próxima geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) e [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Avaliação de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Ao longo do tempo, o Centro de Segurança irá expandir o número de parceiros dentro destas categorias e adicionar outras novas. 

## <a name="deploy-a-partner-solution"></a>Implementar uma solução de parceiro

Com base na configuração do seu ambiente do Azure e a política de segurança que definiu, o Centro de Segurança poderá recomendar-lhe a implementação de uma solução de parceiro. Este recomendação guia-o ao longo do processo de seleção e instalação de uma solução de parceiro. A experiência de implementação geral pode ser diferente, consoante o tipo de solução e de parceiro que escolher. Para obter mais informações, veja os artigos seguintes:

- [Install endpoint protection](security-center-install-endpoint-protection.md) (Instalar a proteção de ponto final)
- [Adicionar uma firewall de aplicação Web](security-center-add-web-application-firewall.md)
- [Add a next-generation firewall](security-center-add-next-generation-firewall.md) (Adicionar uma firewall de próxima geração)
- [Avaliação de vulnerabilidades não instalada](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Gerir soluções de parceiros

Após a implementação, para ver informações sobre o estado de funcionamento da solução e realizar tarefas de gestão básicas, no painel **Centro de Segurança**, selecione a opção **Soluções de parceiros**. Para obter mais informações sobre como gerir soluções de parceiros no Centro de Segurança, veja [Monitoring partner solutions with Azure Security Center (Monitorizar soluções de parceiros com o Centro de Segurança do Azure)](security-center-partner-solutions.md).

![Integração de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> O suporte à proteção de ponto final da Symantec está limitado à deteção. Não estão disponíveis alertas de estado de funcionamento.
>

## <a name="see-also"></a>Consultar também

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança do Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de Segurança](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
* [Alertas de segurança por tipo no Centro de Segurança](security-center-alerts-type.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Monitoring partner solutions with Security Center](security-center-partner-solutions.md) (Monitorizar soluções de parceiros com o Centro de Segurança). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue Azure Security](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

