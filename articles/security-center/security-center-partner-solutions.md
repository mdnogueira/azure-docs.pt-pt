<properties
   pageTitle="Gerir soluções de parceiros no Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento vai ajudá-lo a compreender como o Centro de Segurança do Azure lhe permite monitorizar rapidamente o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="terrylan"/>

# Monitorizar soluções de parceiros com o Centro de Segurança do Azure

Este documento vai ajudá-lo a monitorizar o estado de funcionamento das suas soluções de parceiros no Centro de Segurança do Azure.

> [AZURE.NOTE] As informações neste documento aplicam-se ao pré-lançamento do Centro de Segurança do Azure. Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.

## O que é o Centro de Segurança?
 O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## Monitorizar soluções de parceiros

O mosaico **Soluções de parceiros** no painel **Centro de Segurança** permite-lhe monitorizar rapidamente o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure.
![Mosaico Soluções de parceiros][1]

O mosaico **Soluções de parceiros** apresenta o número de soluções de parceiros e um resumo do estado destas soluções.

O **ESTADO** de uma solução de parceiros pode ser:

- Protegida (verde) – não existe nenhum problema de estado de funcionamento.
- Mau estado de funcionamento (vermelho) – existe um problema de estado de funcionamento que exige atenção imediata.
- Relatórios parados (cor de laranja) – a solução deixou de enviar relatórios acerca do seu estado de funcionamento.
- Estado de proteção desconhecido (cor de laranja) – o estado de funcionamento da solução é desconhecido neste momento devido a um processo falhado de adição de um novo recurso à solução existente.
- Não reportada (cinzento) – a solução ainda não enviou nenhum relatório, o estado de uma solução pode ser Não reportada se tiver acabado de ser ligada e ainda está em implementação.

Se não existirem soluções integradas com a sua subscrição, o mosaico indicará que não existem soluções. Selecionar o mosaico **Soluções de parceiros** permitir-lhe-á abrir o painel **Recomendações** para implementar soluções de segurança de parceiros.
![Sem soluções de parceiros][2]

Para ver o estado de funcionamento das suas soluções de parceiros:

1. Selecione o mosaico **Soluções de parceiros**. Abre-se um painel a apresentar uma lista das suas soluções de parceiros ligadas ao Centro de Segurança.
![Soluções de parceiros][3]

2. Selecione uma solução de parceiros. Neste exemplo, selecionamos a solução **F5-WAF2**.  Abre-se um painel que mostra o estado da solução de parceiros e os recursos associados da solução. Selecione **Consola de soluções** para abrir a experiência de gestão de parceiros desta solução.
![Detalhe de solução de parceiros][4]

3. Volte ao painel **F5-WAF2** e selecione **Ligar aplicação**. O painel **Ligar Aplicações** abre-se. Aqui pode ligar recursos à solução de parceiros.
![Ligar recursos a solução de parceiros][5]

## Passos seguintes
Neste documento, foi-lhe apresentado o mosaico **Soluções de Parceiros** no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

- [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
- [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
- [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Jun16_HO2-->


