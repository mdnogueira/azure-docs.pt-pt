---
title: "Integração de parceiros e soluções no Centro de Segurança do Azure | Microsoft Docs"
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
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8cc44da0f61362018d2757da58ca4fb3a9a43764
ms.contentlocale: pt-pt
ms.lasthandoff: 09/14/2017

---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Integração de Parceiros e Soluções no Centro de Segurança do Azure

Neste artigo, descrevemos de que forma é que o Centro de Segurança do Azure se integra com parceiros, para o ajudar a melhorar a segurança geral. O Centro de Segurança oferece uma experiência integrada no Azure e tira partido do Azure Marketplace para certificação de parceiros e faturação.

## <a name="why-deploy-partner-solutions-from-security-center"></a>Porquê implementar soluções de parceiros do Centro de Segurança?

Os quatro principais motivos para aproveitar a integração de parceiros no Centro de Segurança são:

- **Facilidade de implementação**. é muito mais fácil implementar uma solução de parceiros ao seguir a recomendação do Centro de Segurança. O processo de implementação pode ser totalmente automatizado através de uma configuração e de uma topologia de rede predefinidas. Em alternativa, os clientes podem escolher uma opção semiautomatizada para obter mais flexibilidade e personalização.
- **Deteções integradas**. Os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Monitorização de estado de funcionamento e gestão unificadas**. Os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções do parceiro de forma rápida. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.
- **Exportar para SIEM**. Os clientes podem exportar todos os alertas do Centro de Segurança e dos parceiros no Common Event Format (CEF) para sistemas de Informações de Segurança e Gestão de Eventos (SIEM) no local através da integração de registos do Azure (pré-visualização).


## <a name="partners-that-integrate-with-security-center"></a>Parceiros que se integram com o Centro de Segurança

Atualmente, as soluções de parceiros que estão disponíveis no Azure Marketplace para integração nativa no Centro de Segurança são:

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

Após a implementação, para ver informações sobre o estado de funcionamento da solução e realizar tarefas de gestão básicas, no dashboard **Centro de Segurança**, selecione a opção **Soluções de parceiros**.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

O conteúdo que vê quando abre as Soluções de Segurança pode variar consoante a sua infraestrutura. Utilizando a imagem anterior como exemplo, esta página tem três secções:

- **Soluções ligadas**: apresenta soluções ligadas ao Centro de Segurança.
- **Soluções detetadas**: apresenta soluções que não estão ligadas ao Centro de Segurança. Pode ligar estas soluções, que aparecerão, então, nas soluções ligadas.  Se o Centro de Segurança não detetar nenhuma solução desligada, esta secção estará ocultada.
- **Adicionar origens de dados**: apresenta origens de dados do Azure e de outros serviços e que pode adicionar ao Centro de Segurança.

### <a name="connected-solutions"></a>Soluções ligadas

A secção **Soluções ligadas** mostra todas as soluções de segurança que estão atualmente ligadas ao Centro de Segurança. 

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig10.png)

As informações que vir sobre cada uma podem variar de acordo com a solução. Algumas informações disponíveis em cada mosaico podem incluir:

- Ícone da empresa do parceiro.  Se o Centro de Segurança não tiver o ícone da empresa, é apresentado o primeiro ou primeiros carateres do nome do parceiro.
- Tipo de solução.
- O nome do computador pode ser apresentado.
- Estado de funcionamento.  Se não for enviado um indicador de estado de funcionamento, o Centro de Segurança mostra a data e a hora do último evento recebido, de modo a indicar se a aplicação está a comunicar ou não. Se o Centro de Segurança não receber o indicador de estado de funcionamento de uma solução específica, o mosaico da mesma não aparece nesta secção.

> [!NOTE]
> O Centro de Segurança mostra a data e a hora do último evento recebido, de modo a indicar se a aplicação está a comunicar ou não. As soluções que não estejam a enviar indicadores de estado de funcionamento são apresentadas como ligadas caso tenham sido enviados alertas ou eventos nos últimos 14 dias.
>  

Algumas destas soluções podem ser totalmente integradas no Azure, ao passo que outras poderão ser integradas no local. Uma vez que o Centro de Segurança suporta o [Common Event Format (CEF)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef), pode ligar-se a soluções que utilizem este formato, como uma Firewall que o suporte. Quando esta solução é adicionada ao Centro de Segurança, a Firewall envia registos no formato CEF ao Centro de Segurança, que as remete para o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview). A Firewall não é um recurso do Azure e envia eventos, mas não um indicador de estado de funcionamento.  A única informação que o Centro de Segurança tem sobre o estado de funcionamento é a última vez que este dispositivo enviou um evento.  Para os recursos que não são do Azure, o Centro de Segurança apresenta, na área de estado de funcionamento do mosaico, a data e a hora em que foi recebido o último evento, o que indica que esses recursos continuam a comunicar.

### <a name="discovered-solutions"></a>Soluções detetadas

A secção **Soluções detetadas** mostra todas as soluções que foram adicionadas através do Azure e às quais o Centro de Segurança recomenda que se ligue.

![Soluções detetadas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

O Centro de Segurança pode integrar-se em soluções do Azure incorporadas, tais como o [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection). Se tiver uma licença do Azure AD Identity Protection, mas este não estiver ligado ao Centro de Segurança, o Azure AD Identity Protection será apresentado nas **Soluções detetadas**. Para integrar esta solução no Centro de Segurança, clique em **LIGAR**, no mosaico **Azure AD Identity Protection**, sendo apresentada a página seguinte:

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

Para concluir a ligação do Azure AD Identity Protection, tem de selecionar uma área de trabalho na qual os dados sejam guardados. Todos os dados do Azure AD Identity Protection irão fluir a partir da região da área de trabalho que foi selecionada neste passo.  Vai passar pelo seletor de áreas de trabalho para selecionar a área de trabalho e os dados começaram a fluir daí.

Para ligar ao Centro de Segurança, tem de ser administrador global ou administrador de segurança.  Se não tiver permissões, o botão **Ligar** estará desativado e será mostrada uma mensagem que explica porque é que o botão está desativado.

Os alertas do Azure AD Identity Protection passam pelo pipe de deteção do Centro de Segurança, o que lhe permite receber alertas do Centro de Segurança e do Azure Active Directory Identity Protection. O Centro de Segurança irá unir todos os alertas que pareçam relevantes para criar um [incidente de segurança](https://docs.microsoft.com/azure/security-center/security-center-incident). A descrição do incidente de segurança vai dar-lhe mais informações sobre atividades suspeitas.

### <a name="add-data-sources"></a>Adicionar origens de dados

Pode adicionar computadores do Azure e não Azure para integração no Centro de Segurança.  Adicionar computadores não Azure significa que pode adicionar computadores no local ou aplicações que suportem CEF. 

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig11.png)


## <a name="see-also"></a>Consultar também

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança do Azure. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de Segurança](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança](security-center-managing-and-responding-alerts.md)
* [Alertas de segurança por tipo no Centro de Segurança](security-center-alerts-type.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Monitoring partner solutions with Security Center](security-center-partner-solutions.md) (Monitorizar soluções de parceiros com o Centro de Segurança). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue Azure Security](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

