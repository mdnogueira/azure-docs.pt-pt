---
title: Otimizar o seu ambiente do SQL Server com o Log Analytics do Azure | Microsoft Docs
description: "Análise de registos do Azure, pode utilizar a solução de verificação de estado de funcionamento do SQL para avaliar o risco e o estado de funcionamento dos ambientes num intervalo regular."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec66c322550ac3a7729dc1fddc8c026fb4ec1895
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Otimizar o seu ambiente do SQL Server com a solução de verificação de estado de funcionamento do SQL Server na análise de registos

![Símbolo de verificação de estado de funcionamento do SQL Server](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

Pode utilizar a solução de verificação do Estado de funcionamento de SQL para avaliar o risco e o estado de funcionamento dos seus ambientes de servidor num intervalo regular. Este artigo irá ajudá-lo a instalar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidor implementado. As recomendações são categorizadas em seis áreas que rapidamente a ajudar a compreender o risco e tomar uma ação corretiva.

As recomendações efetuadas baseiam-se em conhecimentos e experiência adquiridos por engenheiros de Microsoft de milhares de visitas de cliente. Cada recomendação fornece orientações sobre o motivo pelo qual um problema poderá é relevante para si e como implementar as alterações sugeridas.

Pode escolher áreas que são mais importantes para a sua organização e controlam o progresso em direção a executar um ambiente de livre e bom estado de funcionamento de risco.

Depois de acrescentar a solução e uma avaliação estiver concluída, resumo informações de áreas de foco são apresentadas no **verificação de estado de funcionamento do SQL Server** dashboard para a infraestrutura no seu ambiente. As secções seguintes descrevem como utilizar as informações sobre o **verificação de estado de funcionamento do SQL Server** dashboard, onde pode ver e, em seguida, tome as ações para a sua infraestrutura do SQL Server recomendadas.

![imagem do mosaico de verificação de estado de funcionamento do SQL Server](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![imagem do dashboard de verificação de estado de funcionamento do SQL Server](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Pré-requisitos

* A solução de verificação de estado de funcionamento do SQL Server requer uma versão suportada do .NET Framework 4 está instalado em cada computador que tenha a monitorização do agente Microsoft (MMA) instalado.  O agente MMA é utilizado pelo System Center 2016 - Operations Manager e Operations Manager 2012 R2 e o serviço de análise de registos.  
* A solução suporta SQL Server 2012, 2014 e 2016 de versão.
* Uma área de trabalho de análise de registos para adicionar a solução de verificação de estado de funcionamento do SQL do Azure marketplace no portal do Azure.  Para instalar a solução, tem de ser um administrador ou Contribuidor na subscrição do Azure. 

  > [!NOTE]
  > Depois de adicionar a solução, o ficheiro AdvisorAssessment.exe é adicionado em servidores com os agentes. Dados de configuração é lido e, em seguida, enviados para o serviço de análise de registos na nuvem para processamento. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados.
  >
  >

Para efetuar a verificação de estado de funcionamento nos seus servidores do SQL Server, necessitam de um agente e a conectividade ao Log Analytics utilizando um dos seguintes métodos suportados:

1. Instalar o [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agents.md) se o servidor já não é monitorizado pelo System Center 2016 - Operations Manager ou do Operations Manager 2012 R2.
2. Se é monitorizado com o System Center 2016 - Operations Manager ou do Operations Manager 2012 R2 e o grupo de gestão não estiver integrado com o serviço de análise de registos, o servidor pode ser multihomed com a análise de registos para recolher dados e reencaminhá-los para o serviço e ainda ser monitorizados pelo Operations Manager.  
3. Caso contrário, se o grupo de gestão do Operations Manager está integrado com o serviço, tem de adicionar controladores de domínio para recolha de dados pelo serviço de seguir os passos em [adicionar computadores geridos por agente](log-analytics-om-agents.md#connecting-operations-manager-to-oms) depois de ativar a solução na sua área de trabalho.  

O agente no seu servidor de SQL que relatórios para um grupo de gestão do Operations Manager, recolhe dados, encaminha para o respetivo servidor de gestão atribuído e, em seguida, é enviada diretamente a partir de um servidor de gestão para o serviço de análise de registos.  Os dados não são escritos para as bases de dados do Operations Manager.  

Se o SQL Server é monitorizado pelo Operations Manager, terá de configurar uma conta do Operations Manager Run As. Consulte [do Operations Manager executar como contas de análise de registos](#operations-manager-run-as-accounts-for-log-analytics) abaixo para obter mais informações.

## <a name="sql-health-check-data-collection-details"></a>Detalhes de recolha de dados de verificação de estado de funcionamento do SQL Server
Verificação de estado de funcionamento do SQL Server recolhe dados das seguintes origens com o agente que tiver ativado: 

* Windows Management Instrumentation (WMI) 
* Registo 
* Contadores de desempenho
* Resultados de vista de gestão dinâmica do SQL Server 

Dados são recolhidos no SQL Server e reencaminhados para a análise de registos a cada sete dias.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Executar como contas do Operations Manager para análise de registos
Análise de registos utiliza o grupo de gestão e agente do Operations Manager para recolher e enviar dados para o serviço de análise de registos. Compilações de análise do registo após a pacotes de gestão para cargas de trabalho fornecer valor-adicionar serviços. Cada carga de trabalho necessita de privilégios de específico da carga de trabalho para executar os pacotes de gestão num contexto de segurança diferentes, tais como uma conta de utilizador de domínio. Tem de fornecer informações de credenciais ao configurar uma conta do Operations Manager Run As.

Utilize as seguintes informações para configurar a Operations Manager conta Run As de verificação de estado de funcionamento do SQL Server.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Definir a conta Run As para a verificação de estado de funcionamento do SQL Server
 Se já estiver a utilizar o pacote de gestão do SQL Server, deve utilizar essa configuração Run As.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar a conta Run As do SQL Server na consola de operações
> [!NOTE]
> Por predefinição, o pacote de gestão de fluxos de trabalho é executado no contexto de segurança da conta de sistema Local. Se estiver a utilizar o Microsoft Monitoring Agent ligado diretamente ao serviço em vez de relatórios diretamente a um grupo de gestão do Operations Manager, ignorar os passos 1 a 5 abaixo e execute o T-SQL ou o exemplo do PowerShell, especificando NT AUTHORITY\SYSTEM como o nome de utilizador.
>
>

1. No Operations Manager, abra a consola de operações e, em seguida, clique em **administração**.
2. Em **configuração de Run As**, clique em **perfis**e abra **OMS SQL avaliação perfil Run As**.
3. No **contas Run as** página, clique em **adicionar**.
4. Selecione uma conta Run As do Windows que contém as credenciais necessárias para o SQL Server, ou clique em **novo** para criar um.

   > [!NOTE]
   > O tipo de conta Run As tem de ser Windows. A conta Run As tem de ser também parte do grupo de administradores Local em todos os servidores do Windows que aloja instâncias do SQL Server.
   >
   >
5. Clique em **Guardar**.
6. Modificar e, em seguida, execute o seguinte exemplo de T-SQL em cada instância do SQL Server para conceder permissões mínimas necessárias para a conta Run As efetuar a verificação de estado de funcionamento. No entanto, não precisa de efetuar este procedimento se uma conta Run As já faz parte da função de servidor sysadmin em instâncias do SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar a conta Run As do SQL Server com o Windows PowerShell
Abra uma janela do PowerShell e execute o seguinte script depois atualizou-lo com as suas informações:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são prioritários
Cada recomendação efetuada é atribuída um valor de peso que identifica a importância relativa da recomendação. São apresentadas apenas as dez mais importantes recomendações.

### <a name="how-weights-are-calculated"></a>Como são calculadas ponderações
As ponderações são valores de agregação com base nas três principais fatores:

* O *probabilidade* que um problema identificado causará problemas. Uma probabilidade superior equivale a uma maior pontuação geral para a recomendação.
* O *impacto* do problema na sua organização se provocar um problema. Um impacto superior equivale a uma maior pontuação geral para a recomendação.
* O *esforço* necessário para implementar a recomendação. Um maior esforço equivale a uma pontuação geral menor para a recomendação.

O peso para cada recomendação é expresso como uma percentagem da classificação total disponível de cada área de foco. Por exemplo, se uma recomendação na área de foco de segurança e conformidade tem uma pontuação de 5%, implementar dessa recomendação irá aumentar a sua geral de segurança e conformidade pontuação por 5%.

### <a name="focus-areas"></a>Áreas de foco
**Segurança e conformidade** -esta área de foco mostra recomendações para potenciais ameaças de segurança e falhas, políticas empresariais e requisitos de conformidade técnicos, jurídicos e regulamentares.

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da sua infraestrutura e a proteção do negócio.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para o ajudar a sua organização infraestrutura de TI aumentar, certifique-se de que o ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder a necessidades de infraestrutura a alteração.

**Atualizar, implementação e migração** -esta área de foco mostra recomendações para o ajudar a atualizar, migrar e implemente o SQL Server para a sua infraestrutura existente.

**Operações e monitorização** -esta área de foco mostra recomendações para o ajudar a simplificar as operações de TI, implementar manutenção preventiva e maximizar o desempenho.

**Gestão de configuração e alteração** -esta área de foco mostra recomendações para ajudar a proteger as operações diárias, certifique-se de que as alterações não afetam a sua infraestrutura, estabelecer procedimentos de controlo de alterações, negativamente e para controlar e auditar configurações do sistema.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve procure para pontuar 100% cada área de foco?
Não necessariamente. As recomendações baseiam-se em conhecimentos e experiências adquiridas por engenheiros Microsoft entre milhares de visitas de cliente. No entanto, nenhum infraestruturas de dois servidor são os mesmos e ver as recomendações específicas podem ser maior ou menor relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem a recolha de dados ad hoc de baixa prioridade e relatórios. Os problemas que são importantes para uma empresa madura poderão ser menos importantes para um arranque. Poderá pretender identificar áreas que são as prioridades e, em seguida, observe como a sua pontuações alteram ao longo do tempo.

Cada recomendação inclui orientações sobre por que motivo é importante. Deve utilizar esta orientação para avaliar se implementar a recomendação é adequada para si, dada a natureza do seu serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Utilize as recomendações de área de foco verificação do Estado de funcionamento
Antes de poder utilizar uma solução de avaliação na análise de registos, tem de ter a solução instalada.  Depois de ser instalado, pode ver o resumo de recomendações, utilizando o mosaico de verificação de estado de funcionamento do SQL Server da página de solução no portal do Azure.

Ver as avaliações de conformidade resumidos para a sua infraestrutura e, em seguida, desagregação em recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. No painel de subscrições de análise de registos, selecione uma área de trabalho e, em seguida, clique em de **Portal do OMS** mosaico.  
4. No **descrição geral** página, clique em de **verificação de estado de funcionamento do SQL Server** mosaico. 
5. No **verificação do Estado de funcionamento** página, reveja as informações de resumo de uma das seguintes painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, pode ver as recomendações prioritários efetuadas para o seu ambiente. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre o motivo pelo qual a recomendação é feita.<br><br> ![imagem de verificação de estado de funcionamento do SQL Server recomendações](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Pode executar ações corretivas sugeridas na **as ações sugeridas**. Quando o item tem sido resolvido, irão registar avaliações posteriores que ações foram executadas e o modelo de pontuação de conformidade aumentam recomendadas. Itens corrigidas aparecem como **transmitido objetos**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações
Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que OMS irá utilizar para impedir que as recomendações a apresentação nos resultados da avaliação.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Para identificar as recomendações que irão ignorar
1. No portal do Azure na página da área de trabalho de análise de registos para a sua área de trabalho selecionada, clique em de **pesquisa registo** mosaico.
2. Utilize a seguinte consulta para recomendações de lista que falharam para os computadores no seu ambiente.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registo:<br><br> ![recomendações falhadas](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Escolha as recomendações que pretende ignorar. Irá utilizar os valores para RecommendationId no procedimento seguinte.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt
1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Colar ou escreva cada RecommendationId para cada recomendação que pretende que a análise de registos para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que a análise de registos para ignorar recomendações.
   * Em computadores com o Microsoft Monitoring Agent (ligado diretamente ou através do Operations Manager) - *SystemDrive*: \Programas\Microsoft Agent\Agent de monitorização
   * No servidor de gestão do Operations Manager - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations \ Operations Manager\Server
   * No servidor de gestão do Operations Manager 2016 - *SystemDrive*: \Programas\Microsoft System Center 2016\Operations \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas
1. Depois da próxima agendada execuções de avaliação, por predefinição, 7 dias, as recomendações especificadas estão marcadas ignoradas e não serão apresentados no dashboard de avaliação.
2. Pode utilizar as seguintes consultas de pesquisa de registo para listar todas as recomendações ignoradas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se, posteriormente, decida se pretende ver as recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="sql-health-check-solution-faq"></a>Verificação de estado de funcionamento do SQL Server solução FAQ
*Frequência executa uma verificação de estado de funcionamento?*

* A verificação é executada a cada sete dias.

*Existe uma forma de configurar a frequência de verificação é executada?*

* Neste momento, não.

*Se for detetado o outro servidor após posso adicionou a solução de verificação de estado de funcionamento do SQL Server, será-verificado?*

* Sim, quando é detetado que é verificado em seguida, no, a cada sete dias.

*Se um servidor é encerrado, quando irá-ser removido a verificação de estado de funcionamento?*

* Se um servidor não submeter dados durante 3 semanas, este é removido.

*O que é o nome do processo que a recolha de dados?*

* AdvisorAssessment.exe

*Quanto tempo demora para dados a serem recolhidos?*

* A recolha de dados real no servidor demora cerca de 1 hora. Pode demorar mais em servidores que tenham um grande número de instâncias do SQL Server ou bases de dados.

*Que tipo de dados é recolhido?*

* Os seguintes tipos de dados são recolhidos:
  * WMI
  * Registo
  * Contadores de desempenho
  * Vistas de gestão dinâmica do SQL Server (DMV).

*Existe uma forma de configurar quando são recolhidos os dados?*

* Neste momento, não.

*Por que motivo é necessário configurar uma conta Run As?*

* Para o SQL Server, um pequeno número de consultas SQL é executado. Ordem que sejam executadas, uma conta Run As com permissões de estado de vista do servidor para o SQL Server tem de ser utilizada.  Além disso, para consultar o WMI, as credenciais de administrador local são necessárias.

*Por que motivo apresentar apenas as recomendações de 10 principais?*

* Em vez de dando-lhe uma lista exaustiva muito confuso das tarefas, recomendamos que lhe concentrar-se no endereçamento as recomendações prioritários primeiro. Depois de resolvê-los, recomendações adicionais deixará de estar disponíveis. Se preferir ver a lista de detalhado, pode ver todas as recomendações utilizando a pesquisa de registos de análise de registos.

*Existe alguma forma para ignorar uma recomendação?*

* Sim, consulte [ignorar recomendações](#ignore-recommendations) secção acima.

## <a name="next-steps"></a>Passos seguintes
* [Pesquisar registos](log-analytics-log-searches.md) para saber como analisar dados de verificação do Estado de funcionamento de SQL detalhados e recomendações.
