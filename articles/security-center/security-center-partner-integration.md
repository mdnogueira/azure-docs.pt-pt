---
title: "Integração de Parceiros no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento explica de que forma é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 0c946ce6a96f2e3644b9890dad5d60a35ad4bcb7
ms.openlocfilehash: 10184c52d532eb56e66212fafdea3d059b0c43e3


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

- Endpoint Protection (Trend Micro), 
- Firewall de Aplicações Web (Barracuda, F5, Imperva e, em breve, Microsoft WAF e Fortinet), 
- Soluções de Firewall da Próxima Geração (Check Point, Barracuda e, em breve, Fortinet e Cisco). 
- Soluções de Avaliação de vulnerabilidades (Qualys - pré-visualização) 

Ao longo do tempo, o Centro de Segurança irá expandir o número de parceiros dentro destas categorias atuais e adicionar novas categorias. 

## <a name="how-to-deploy-a-partner-solution"></a>Como implementar uma solução de parceiro?

As soluções de parceiros que já foram implementadas no Centro de Segurança podem ser facilmente acedidas a partir do mosaico de Solução de parceiro, no dashboard principal do Centro de Segurança:

![Integração de Parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)

Para implementar uma nova solução de parceiro com base numa recomendação do Centro de Segurança, execute os passos seguintes:

> [!NOTE]
> Os passos no exemplo seguinte partem do princípio de que tem uma carga de trabalho e quer protegê-la com uma firewall de aplicação web.

1. No dashboard do Centro de Segurança, clique no mosaico **Recomendações**.
2. No painel **Recomendações**, clique em **Adicionar firewall de aplicação Web**.
3. Clique no nome da aplicação, no painel **Adicionar uma firewall de aplicação Web**.
4. No painel **Adicionar uma Firewall de Aplicação Web**, clique em **Criar Nova**.
5. O painel **Criar uma Firewall de Aplicação Web nova** mostra uma lista dos parceiros que oferecem, atualmente, a capacidade de firewall de aplicações Web.
6. Selecione a solução de parceiro adequada e siga os passos (que podem variar, consoante o parceiro).

A experiência de implementação geral nesta altura pode variar de acordo com o parceiro. Para obter mais informações sobre como gerir soluções de parceiros no Centro de Segurança, leia [Monitorizar soluções de parceiros](security-center-partner-solutions.md) com o Centro de Segurança do Azure.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a integrar soluções de parceiros no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de Segurança no Centro de Segurança do Azure](security-center-alerts-type.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.



<!--HONumber=Nov16_HO5-->


