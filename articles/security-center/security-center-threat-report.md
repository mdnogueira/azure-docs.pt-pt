---
title: "Relatórios de informações sobre Ameaças do Centro de Segurança do Azure| Microsoft Docs"
description: "Este documento ajuda-o a utilizar os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure durante uma investigação para obter mais informações sobre um alerta de segurança."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: b4310cf4e6849c67031b3ec8b1fd5957e35f7ea6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de Informações Sobre Ameaças do Centro de Segurança do Azure
Este documento explica como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudá-lo a obter mais informações sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de informações sobre ameaças?
A deteção de ameaças do Centro de Segurança funciona através da monitorização de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Este processo faz parte das [capacidades de deteção](security-center-detection-capabilities.md) do Centro de Segurança.

Quando o Centro de Segurança identifica uma ameaça, irá acionar um [alerta de segurança](security-center-managing-and-responding-alerts.md) com informações detalhadas sobre um determinado evento, incluindo sugestões de remediação. Para ajudar as equipas de resposta a incidentes a investigar e remediar ameaças, o Centro de Segurança inclui um relatório de informações sobre ameaças com dados sobre a ameaça detetada, incluindo informações como:

* A identidade do atacante ou associações (se esta informação estiver disponível)
* Os objetivos dos atacantes
* As campanhas de ataque atuais e históricas (se esta informação estiver disponível)
* As táticas, ferramentas e procedimentos dos atacantes
* Os indicadores de comprometimento (IoC) associados, como URLs e hashes de ficheiros
* A vitimologia, a prevalência do setor e geográfica para ajudá-lo a determinar se os recursos do Azure estão em risco
* Informações de mitigação e remediação

> [!NOTE]
> A quantidade de informações em qualquer relatório específico irá variar; o nível de detalhe baseia-se na atividade e na prevalência de software maligno.
>
>

O Centro de Segurança tem três tipos de relatórios de ameaças, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório do Grupo de Atividade**: fornece informações detalhadas sobre os atacantes, os seus objetivos e táticas.
* **Relatório da Campanha**: centra-se nos detalhes sobre campanhas de ataque específicas.
* **Relatório de Resumo da Ameaça**: abrange todos os itens nos dois relatórios anteriores.

Este tipo de informações é bastante útil durante o processo de [resposta a incidentes](security-center-incident-response.md), onde existe uma investigação em curso para compreender a origem do ataque, as motivações do atacante e o que fazer para mitigar este problema no futuro.

## <a name="how-to-access-the-threat-intelligence-report"></a>Como aceder ao relatório de informações sobre ameaças?
Pode rever os alertas atuais ao observar o mosaico **Alertas de segurança**. Abra o Portal do Azure e siga os passos abaixo para ver mais detalhes sobre cada alerta:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.
2. Clique no mosaico para abrir o painel **Alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança sobre o qual quer obter mais informações.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Neste caso, o painel **Processo suspeito executado** mostra os detalhes sobre o alerta, conforme indicado na figura abaixo:

    ![Detalhes do alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. A quantidade de informação disponível para cada alerta de segurança irá variar de acordo com o tipo de alerta. No campo **RELATÓRIOS**, existe uma ligação para o relatório de informações sobre ameaças. Clique na mesma e será apresentada outra janela do browser com o ficheiro PDF.

   ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

A partir daqui, pode transferir o PDF para este relatório e ler mais informações sobre o problema de segurança detetado e efetuar ações com base nas informações fornecidas.

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu como os Relatórios de Informações Sobre Ameaças do Centro de Segurança do Azure podem ajudar durante uma investigação sobre alertas de segurança. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Tirar Partido do Centro de Segurança do Azure para Resposta a Incidentes](security-center-incident-response.md)
* [Capacidades de deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
* [Lidar com Incidentes de Segurança no Centro de Segurança do Azure](security-center-incident.md)
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
