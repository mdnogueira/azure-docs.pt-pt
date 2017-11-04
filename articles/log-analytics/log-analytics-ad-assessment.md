---
title: Otimizar o seu ambiente do Active Directory com o Log Analytics do Azure | Microsoft Docs
description: "Pode utilizar a solução de verificação de estado de funcionamento do Active Directory para avaliar o risco e o estado de funcionamento dos ambientes num intervalo regular."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e78ca1da8cafe93e76d640c0e6d5ad5309655c1b
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Otimizar o seu ambiente do Active Directory com a solução de verificação de estado de funcionamento do Active Directory na análise de registos

![Símbolo de verificação do Estado de funcionamento do AD](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

Pode utilizar a solução de verificação de estado de funcionamento do Active Directory para avaliar o risco e o estado de funcionamento dos seus ambientes de servidor num intervalo regular. Este artigo ajuda-o a instalar e utilizar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidor implementado. As recomendações são categorizadas em quatro áreas de foco, que rapidamente a ajudar a compreender o risco e tomar medidas.

As recomendações baseiam-se em conhecimentos e experiência adquiridos por engenheiros de Microsoft de milhares de visitas de cliente. Cada recomendação fornece orientações sobre o motivo pelo qual um problema poderá é relevante para si e como implementar as alterações sugeridas.

Pode escolher áreas que são mais importantes para a sua organização e controlam o progresso em direção a executar um ambiente de livre e bom estado de funcionamento de risco.

Depois de acrescentar a solução e uma verificação estiver concluída, resumo informações de áreas de foco são apresentadas no **verificação do Estado de funcionamento de AD** dashboard para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações sobre o **verificação do Estado de funcionamento de AD** dashboard, onde pode ver e, em seguida, tome as ações para a sua infraestrutura de servidor do Active Directory recomendadas.  

![imagem de verificação do Estado de funcionamento de AD mosaico](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![imagem do dashboard do AD de verificação de estado de funcionamento](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de estado de funcionamento do Active Directory requer uma versão suportada do .NET Framework 4.5.2 ou superior instalado em cada computador que tenha a monitorização do agente Microsoft (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Operations Manager e Operations Manager 2012 R2 e o serviço de análise de registos. 
* A solução suporta controladores de domínio que executam o Windows Server 2008 e 2008 R2, Windows Server 2012 e 2012 R2 e Windows Server 2016.
* Uma área de trabalho de análise de registos para adicionar a solução de verificação de estado de funcionamento do Active Directory do Azure marketplace no portal do Azure.  Não há nenhuma configuração adicional.

  > [!NOTE]
  > Depois de adicionar a solução, o ficheiro AdvisorAssessment.exe é adicionado em servidores com os agentes. Dados de configuração é lido e, em seguida, enviados para o serviço de análise de registos na nuvem para processamento. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados.
  >
  >

Para efetuar a verificação de estado de funcionamento contra os controladores de domínio que são membros do domínio a ser avaliada, necessitam de um agente e a conectividade ao Log Analytics utilizando um dos seguintes métodos suportados:

1. Instalar o [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agents.md) se o controlador de domínio já não é monitorizado pelo System Center 2016 - Operations Manager ou do Operations Manager 2012 R2.
2. Se é monitorizado com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2 e o grupo de gestão não estiver integrado com o serviço de análise de registos, o controlador de domínio pode ser multihomed com a análise de registos para recolher dados e reencaminhá-los para o serviço e ainda ser monitorizados pelo Operations Manager.  
3. Caso contrário, se o grupo de gestão do Operations Manager está integrado com o serviço, tem de adicionar controladores de domínio para recolha de dados pelo serviço de seguir os passos em [adicionar computadores geridos por agente](log-analytics-om-agents.md#connecting-operations-manager-to-oms) depois de ativar a solução na sua área de trabalho.  

O agente no seu controlador de domínio que relatórios para um grupo de gestão do Operations Manager, recolhe dados, encaminha para o respetivo servidor de gestão atribuído e, em seguida, é enviada diretamente a partir de um servidor de gestão para o serviço de análise de registos.  Os dados não são escritos para as bases de dados do Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Ativos a verificação de estado de funcionamento do coleção os detalhes de dados

Verificação de estado de funcionamento do Active Directory recolhe dados das seguintes origens com o agente que tiver ativado:

- Registo 
- LDAP 
- .NET framework
- Registo de eventos 
- Interfaces de serviço do Active Directory (ADSI)
- Windows PowerShell
- Dados de ficheiro 
- Windows Management Instrumentation (WMI)
- Ferramenta DCDIAG API
- API de serviço (NTFRS) de replicação de ficheiros
- Código personalizado c#

Dados são recolhidos no controlador de domínio e reencaminhados para a análise de registos a cada sete dias.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são prioritários
Cada recomendação efetuada é atribuída um valor de peso que identifica a importância relativa da recomendação. As 10 recomendações mais importantes são apresentadas.

### <a name="how-weights-are-calculated"></a>Como são calculadas ponderações
As ponderações são valores de agregação com base nas três principais fatores:

* O *probabilidade* que um problema identificado provoca problemas. Uma probabilidade superior equivale a uma maior pontuação geral para a recomendação.
* O *impacto* do problema na sua organização se provocar um problema. Um impacto superior equivale a uma maior pontuação geral para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um maior esforço equivale a uma pontuação geral menor para a recomendação.

O peso para cada recomendação é expresso como uma percentagem da classificação total disponível de cada área de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tem uma pontuação de 5%, implementar dessa recomendação aumenta o geral de segurança e conformidade pontuação por 5%.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** -esta área de foco mostra recomendações para potenciais ameaças de segurança e falhas, políticas empresariais e requisitos de conformidade técnicos, jurídicos e regulamentares.

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da sua infraestrutura e a proteção do negócio.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para o ajudar a sua organização infraestrutura de TI aumentar, certifique-se de que o ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder a necessidades de infraestrutura a alteração.

**Atualizar, implementação e migração** -esta área de foco mostra recomendações para o ajudar a atualizar, implementar e migrar do Active Directory para a sua infraestrutura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve procure para pontuar 100% cada área de foco?
Não necessariamente. As recomendações baseiam-se em conhecimentos e experiências adquiridas por engenheiros Microsoft entre milhares de visitas de cliente. No entanto, nenhum infraestruturas de dois servidor são os mesmos e ver as recomendações específicas podem ser maior ou menor relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem a recolha de dados ad hoc de baixa prioridade e relatórios. Os problemas que são importantes para uma empresa madura poderão ser menos importantes para um arranque. Poderá pretender identificar áreas que são as prioridades e, em seguida, observe como a sua pontuações alteram ao longo do tempo.

Cada recomendação inclui orientações sobre por que motivo é importante. Deve utilizar esta orientação para avaliar se implementar a recomendação é adequada para si, dada a natureza do seu serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Recomendações da área de foco de verificação do Estado de funcionamento de utilização
Depois de ser instalado, pode ver o resumo de recomendações, utilizando o mosaico de verificação do Estado de funcionamento da página de solução no portal do Azure.

Ver as avaliações de conformidade resumidos para a sua infraestrutura e, em seguida, desagregação em recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. No painel de subscrições de análise de registos, selecione uma área de trabalho e, em seguida, clique em de **Portal do OMS** mosaico.  
4. No **descrição geral** página, clique em de **verificação do Estado de funcionamento de AD** mosaico. 
5. No **verificação do Estado de funcionamento** página, reveja as informações de resumo de uma das seguintes painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, pode ver as recomendações prioritários efetuadas para o seu ambiente. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre o motivo pelo qual a recomendação é feita.<br><br> ![imagem de verificação do Estado de funcionamento recomendações](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Pode executar ações corretivas sugeridas na **as ações sugeridas**. Quando o item tem sido resolvido, registos de avaliações posteriores que as ações recomendadas foram executados e irá aumentar a sua pontuação de conformidade. Itens corrigidas aparecem como **transmitido objetos**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações
Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que irá utilizar análise de registos para impedir que as recomendações a apresentação nos resultados da avaliação.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que irão ignorar
1. No portal do Azure na página da área de trabalho de análise de registos para a sua área de trabalho selecionada, clique em de **pesquisa registo** mosaico.
2. Utilize a seguinte consulta para recomendações de lista que falharam para os computadores no seu ambiente.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```
    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registo:<br><br> ![recomendações falhadas](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Escolha as recomendações que pretende ignorar. Irá utilizar os valores para RecommendationId no procedimento seguinte.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Colar ou escreva cada RecommendationId para cada recomendação que pretende que a análise de registos para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que a análise de registos para ignorar recomendações.
   * Em computadores com o Microsoft Monitoring Agent (ligado diretamente ou através do Operations Manager) - *SystemDrive*: \Programas\Microsoft Agent\Agent de monitorização
   * No servidor de gestão do Operations Manager 2012 R2 - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations \ Operations Manager\Server 
   * No servidor de gestão do Operations Manager 2016 - *SystemDrive*: \Programas\Microsoft System Center 2016\Operations \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
Depois da próxima agendada é executada a verificação de estado de funcionamento, por predefinição a cada sete dias, as recomendações especificadas estão marcadas *ignoradas* e não serão apresentados no dashboard.

1. Pode utilizar as seguintes consultas de pesquisa de registo para listar todas as recomendações ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

2. Se, posteriormente, decida se pretende ver as recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="ad-health-check-solutions-faq"></a>Perguntas mais frequentes sobre soluções de verificação do Estado de funcionamento do AD
*Frequência executa uma verificação de estado de funcionamento?*

* A verificação é executada a cada sete dias.

*Existe uma forma de configurar a frequência executa a verificação de estado de funcionamento?*

* Neste momento, não.

*Se o outro servidor para é detetado depois posso adicionou uma solução de verificação de estado de funcionamento irá ser verificado*

* Sim, quando é detetado que é verificado em seguida, no, a cada sete dias.

*Se um servidor é encerrado, quando irá-ser removido a verificação de estado de funcionamento?*

* Se um servidor não submeter dados durante 3 semanas, este é removido.

*O que é o nome do processo que a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora para dados a serem recolhidos?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais em servidores que tenham um grande número de servidores do Active Directory.

*Existe uma forma de configurar quando são recolhidos os dados?*

* Neste momento, não.

*Por que motivo apresentar apenas as recomendações de 10 principais?*

* Em vez de dando-lhe uma lista exaustiva muito confuso das tarefas, recomendamos que lhe concentrar-se no endereçamento as recomendações prioritários primeiro. Depois de resolvê-los, recomendações adicionais deixará de estar disponíveis. Se preferir ver a lista de detalhado, pode ver todas as recomendações com pesquisa de registo.

*Existe alguma forma para ignorar uma recomendação?*

* Sim, consulte [ignorar recomendações](#ignore-recommendations) secção acima.

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo na análise de registos](log-analytics-log-searches.md) para saber como analisar dados de verificação do Estado de funcionamento de AD e recomendações detalhadas.
