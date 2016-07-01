<properties
   pageTitle="Guia de Introdução ao Centro de Segurança do Azure | Microsoft Azure"
   description="Este documento ajuda-o a começar rapidamente a utilizar o Centro de Segurança do Azure, ao servir de orientação pelos componentes de monitorização de segurança e de gestão de políticas e ligá-lo aos passos seguintes."
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
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Guia de introdução ao Centro de Segurança do Azure

Este documento ajuda-o a começar rapidamente a utilizar o Centro de Segurança do Azure, ao servir de orientação pelos componentes de monitorização de segurança e de gestão de políticas e ligá-lo aos passos seguintes.

> [AZURE.NOTE] As informações neste documento aplicam-se ao pré-lançamento do Centro de Segurança do Azure. Este documento apresenta o serviço ao utilizar um exemplo de implementação. Não se trata de um guia passo-a-passo.

## O que é o Centro de Segurança do Azure?
 O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## Pré-requisitos

Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. O Centro de Segurança está ativado com a sua subscrição. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

 O acesso ao Centro de Segurança é efetuado a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). Consulte a [documentação do portal](https://azure.microsoft.com/documentation/services/azure-portal/) para saber mais.


## Aceder ao Centro de Segurança

No portal, siga estes passos para aceder ao Centro de Segurança:

1. Selecione **Procurar** e, em seguida, desloque-se para a opção **Centro de Segurança**.
![Aceder ao Centro de Segurança do Azure no portal][1]

2. Selecione **Centro de Segurança**. Esta ação abre o painel **Centro de Segurança**.
3. Para facilitar o acesso ao painel **Centro de Segurança** no futuro, selecione a opção **Afixar painel ao dashboard** (canto superior direito).
![Opção Afixar painel ao dashboard][2]

## Utilizar o Centro de Segurança

Pode configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure. Vamos configurar uma **política** de segurança para a sua subscrição:

1. Selecione o mosaico **política** no painel **Centro de Segurança**.
![Centro de Segurança][3]

2. No painel **Política de segurança-Definir política por subscrição ou grupo de recursos**, selecione uma subscrição.
![O painel Política de segurança no Centro de Segurança do Azure][4]

3. No painel **Política de segurança**, ative **Recolha de dados** para recolher registos automaticamente. Ativar **Recolha de dados** também aprovisionará a extensão de monitorização em todas as VMs atuais e novas na subscrição.
4. Selecione **Escolher uma conta do Storage por região**. Para cada região na qual tenha máquinas virtuais em execução, selecione a conta do Storage onde os dados recolhidos dessas máquinas virtuais estão armazenados. Se não escolher uma conta do Storage para cada região, será criada por si. Os dados que são recolhidos são logicamente isolados a partir dos dados de outros clientes, por motivos de segurança.

     > [AZURE.NOTE] Recomendamos que comece por ativar a recolha de dados e que escolha uma conta do Storage ao nível da subscrição.  As políticas de segurança podem ser definidas ao nível de subscrição do Azure e ao nível de grupo de recursos, mas a configuração da recolha de dados e da conta do Storage apenas ocorre ao nível da subscrição.

5. Ative as **Recomendações** que pretende ver como parte da sua política de segurança. Exemplos:

 - Ativar **Atualizações do sistema** irá analisar todas as máquinas virtuais suportadas quanto a atualizações do SO em falta.
 - Ativar **Regras de linha de base** irá analisar todas as máquinas virtuais suportadas para identificar quaisquer configurações de SO que possam tornar a máquina virtual mais vulnerável a ataques.

Abordar **Recomendações**:

1. Volte ao painel **Centro de Segurança** e selecione o mosaico **Recomendações**. O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando são identificadas potenciais vulnerabilidades de segurança, é mostrada aqui uma recomendação.
2.  Selecione cada recomendação para visualizar mais informações e/ou para tomar medidas de resolução do problema.
![Recomendações no Centro de Segurança do Azure][5]

Ver o estado de funcionamento e segurança dos seus recursos através de **Estado de funcionamento de segurança de recursos**:

1.  Volte ao painel **Centro de Segurança**.
2.  O mosaico **Estado de funcionamento de segurança de recursos** contém indicadores do estado de segurança para **Virtual Machines**, **Redes**, **SQL** e **Aplicações**.
3.  Selecione **Virtual Machines** para ver mais informações.
4.  O painel **Virtual Machines** apresenta um resumo de estado que mostra o estado de programas antimalware, atualizações do sistema, reinícios e as regras de linha de base das suas máquinas virtuais.
5.  Selecione um item em **RECOMENDAÇÕES DE MÁQUINA VIRTUAL** para visualizar mais informações e/ou para tomar medidas para configurar os controlos necessários.
6.  Pesquise para baixo para ver informações adicionais acerca de máquinas virtuais específicas.
![O mosaico Estado de funcionamento de recursos no Centro de Segurança do Azure][6]

Abordar **Alertas de segurança**:

1.  Volte ao painel **Centro de Segurança** e selecione o mosaico **Alertas de segurança**. No painel **Alertas de segurança** é apresentada uma lista dos alertas. Os alertas são gerados pela análise do Centro de Segurança dos seus registos de segurança e de atividade de rede. Os alertas de soluções de parceiros integradas também são incluídos.
![Alertas de segurança no Centro de Segurança do Azure][7]

2.  Selecione um alerta para ver informações adicionais.
![Detalhes de alertas de segurança no Centro de Segurança do Azure][8]

Ver o estado de funcionamento das suas **Soluções de parceiros**:

1. Volte ao painel **Centro de Segurança**. O mosaico **Soluções de parceiros** permite-lhe monitorizar rapidamente o estado de funcionamento das suas soluções de parceiros integradas na sua subscrição do Azure.
2. Selecione o mosaico **Soluções de parceiros**. Abre-se um painel a apresentar uma lista das suas soluções de parceiros ligadas ao Centro de Segurança.
![Soluções de parceiros][9]

3. Selecione uma solução de parceiros. Neste exemplo, selecionamos a solução **F5-WAF2**.  Abre-se um painel que mostra o estado da solução de parceiros e os recursos associados da solução. Selecione **Consola de soluções** para abrir a experiência de gestão de parceiros desta solução.
![Detalhe de solução de parceiros][10]

## Passos seguintes
Neste documento, foram-lhe apresentados os componentes de monitorização de segurança e de gestão de políticas no Centro de Segurança. Para saber mais, consulte o seguinte:

- [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
- [Gerir recomendações de segurança no Centro de Segurança do Azure](security-center-recommendations.md) – Saiba como as recomendações o ajudam a proteger os seus recursos do Azure.
- [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
- [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
- [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
- [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png



<!--HONumber=Jun16_HO2-->


