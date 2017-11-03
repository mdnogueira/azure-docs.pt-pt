---
title: Otimizar o seu ambiente do System Center Operations Manager com o Log Analytics do Azure | Microsoft Docs
description: "Pode utilizar a solução de System Center Operations Manager estado de funcionamento da verificação do sistema para avaliar o risco e o estado de funcionamento dos ambientes num intervalo regular."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte;banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a66cc13d05c81de571e2710519ad9474304d656
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Otimizar o seu ambiente com a solução de System Center Operations Manager estado de funcionamento da verificação do sistema (pré-visualização)

![Símbolo de verificação de estado de funcionamento do System Center Operations Manager do sistema](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

Pode utilizar a solução de System Center Operations Manager estado de funcionamento da verificação do sistema para avaliar o risco e o estado de funcionamento do seu grupo de gestão do System Center Operations Manager num intervalo regular. Este artigo ajuda-o a instalar, configurar e utilizar a solução para que possa tomar medidas corretivas para potenciais problemas.

Esta solução fornece uma lista prioritária de recomendações específicas para a sua infraestrutura de servidor implementado. As recomendações são categorizadas em quatro áreas de foco, que rapidamente a ajudar a compreender o risco e tomar uma ação corretiva.

As recomendações efetuadas baseiam-se em conhecimentos e experiência adquiridos por engenheiros de Microsoft de milhares de visitas de cliente. Cada recomendação fornece orientações sobre o motivo pelo qual um problema poderá é relevante para si e como implementar as alterações sugeridas.

Pode escolher áreas que são mais importantes para a sua organização e controlam o progresso em direção a executar um ambiente de livre e bom estado de funcionamento de risco.

Depois de acrescentar a solução e uma avaliação é efetuada, resumo informações de áreas de foco são apresentadas no **Center Operations Manager estado de funcionamento da verificação do sistema** dashboard para a sua infraestrutura. As secções seguintes descrevem como utilizar as informações sobre o **Center Operations Manager estado de funcionamento da verificação do sistema** dashboard, onde pode ver e, em seguida, tome as ações para o seu ambiente do Operations Manager recomendadas.

![Mosaico de solução do System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Dashboard de verificação de estado de funcionamento do System Center Operations Manager do sistema](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

A solução funciona com o sistema do Microsoft Operations Manager 2012 Service Pack (SP) 1 e 2012 R2.

Utilize as seguintes informações para instalar e configurar a solução.

 - Antes de poder utilizar a solução de verificação do Estado de funcionamento na análise de registos, tem de ter a solução instalada. Instalar a solução de [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Depois de adicionar a solução para a área de trabalho, o **Center Operations Manager estado de funcionamento da verificação do sistema** mosaico no dashboard mostra uma mensagem necessária configuração adicional. Clique no mosaico e siga os passos de configuração mencionados na página

 ![Mosaico do dashboard do System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Configuração do System Center Operations Manager pode ser feita através de um script, seguindo os passos mencionados na página de configuração da solução na análise de registos.

 Para configurar a avaliação através da consola de operações do Operations Manager, execute os passos abaixo pela seguinte ordem:
1. [Definir a conta Run As para o System Center Operations Manager estado de funcionamento da verificação do sistema](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Configurar a regra de System Center Operations Manager estado de funcionamento da verificação do sistema](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Detalhes de recolha de dados do System Center Operations Manager avaliação

A avaliação do System Center Operations Manager recolhe dados das seguintes origens: 

* Registo
* Windows Management Instrumentation (WMI)
* Registo de eventos
* Dados de ficheiro
* Diretamente a partir do Operations Manager através de consultas do PowerShell e o SQL Server, de um servidor de gestão que especificou.  

Dados são recolhidos no servidor de gestão e reencaminhados para a análise de registos a cada sete dias.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Executar como contas do Operations Manager para análise de registos

Compilações de análise do registo de pacotes de gestão para cargas de trabalho fornecer valor-adicionar serviços. Cada carga de trabalho necessita de privilégios de específico da carga de trabalho para executar os pacotes de gestão num contexto de segurança diferentes, tais como uma conta de utilizador de domínio. Configure uma conta do Operations Manager Run As com credenciais privilegiadas. Para obter mais informações, consulte [como criar uma conta Run As](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) na documentação do Operations Manager. 

Utilize as seguintes informações para definir a Operations Manager conta Run As para o System Center Operations Manager estado de funcionamento da verificação do sistema.

### <a name="set-the-run-as-account"></a>Definir a conta Run As

A conta Run As tem de cumprir os seguintes requisitos antes de continuar:

* Uma conta de utilizador de domínio que seja membro do grupo Administradores local em todos os servidores que suporta qualquer função do Operations Manager - servidor de gestão, SQL Server que aloja o operacional, armazém de dados e base de dados ACS, relatórios, Web consola e o servidor de Gateway.
* Função de administrador do Gestor de operação do grupo de gestão que está a ser avaliado
* Se a conta não tem direitos de administrador do sistema do SQL Server, em seguida, execute o [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) para conceder permissões granulares para a conta de cada instância do SQL Server que aloja uma ou todas as bases de dados do Operations Manager. 

1. Na consola do Operations Manager, selecione o **administração** botão de navegação.
2. Em **configuração de Run As**, clique em **contas**.
3. No **criar conta Run As** assistente, no **introdução** página clique **seguinte**.
4. No **propriedades gerais** página, selecione **Windows** no **tipo de conta Run As:** lista.
5. Escreva um nome a apresentar no **nome a apresentar** texto caixa e, opcionalmente, escreva uma descrição no **Descrição** caixa e, em seguida, clique em **seguinte**. 
6. No **segurança de distribuição** página, selecione **mais segura**.
7. Clique em **Criar**.  

Agora que a conta Run As é criada, tem de servidores de gestão de destino no grupo de gestão e associados a um perfil Run As previamente definido, pelo que os fluxos de trabalho serão executado com as credenciais.  

1. Em **configuração de Run As**, **contas**, no painel de resultados, faça duplo clique em conta que criou anteriormente.
2. No **distribuição** separador, clique em **adicionar** para o **selecionado computadores** caixa e adicione o servidor de gestão para distribuir a conta.  Clique em **OK** duas vezes para guardar as alterações.
3. Em **configuração de Run As**, clique em **perfis**. 
4. Procure o *SCOM avaliação perfil*.
5. Deve ser o nome de perfil: *Microsoft System Center Advisor SCOM avaliação perfil Run As*.
6. Com o botão direito e Atualize as respetivas propriedades e adicionar recentemente criado a conta Run as que criou anteriormente.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL para conceder permissões granulares para a conta Run As

Execute o seguinte script SQL para conceder as permissões necessárias para a conta Run As na instância do SQL Server utilizada pelo alojamento operacional do Operations Manager, armazém de dados e base de dados ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Configurar a regra de verificação do Estado de funcionamento

Pacote de gestão do System Center Operations Manager estado de funcionamento da verificação do sistema da solução inclui uma regra com o nome *Microsoft System Center Advisor SCOM avaliação executar avaliação regra do sistema*. Esta regra é responsável por executar a verificação de estado de funcionamento. Para ativar a regra e configurar a frequência, utilize os procedimentos abaixo.

Por predefinição, o Microsoft System Center Advisor SCOM avaliação executar avaliação regra está desativada. Para executar a verificação de estado de funcionamento, tem de ativar a regra num servidor de gestão. Utilize os seguintes passos.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Ativar a regra para um servidor de gestão específico

1. No **criação** área de trabalho da consola de operações do Operations Manager, procure a regra *Microsoft System Center Advisor SCOM avaliação executar avaliação regra do sistema* no **regras** painel.
2. Nos resultados da pesquisa, selecione de que inclui o texto *tipo: servidor de gestão*.
3. Clique com o botão direito a regra e, em seguida, clique em **substitui** > **para um objeto específico da classe: servidor de gestão**.
4.  Na lista de servidores de gestão disponíveis, selecione o servidor de gestão onde deve ser executada a regra.  Deve ser o mesmo servidor de gestão que configurou anteriormente para associar a conta Run As com.
5.  Certifique-se de que altera o valor de substituição para **verdadeiro** para o **ativado** valor do parâmetro.<br><br> ![parâmetro de substituição](./media/log-analytics-scom-assessment/rule.png)

    Enquanto ainda esta janela, configure a frequência de execução utilizando o procedimento seguinte.

#### <a name="configure-the-run-frequency"></a>Configurar a frequência de execução

Por predefinição, a avaliação está configurada para executar cada 10.080 minutos (ou sete dias). Pode substituir o valor para um valor mínimo de 1440 minutos (ou um dia). O valor representa o intervalo de tempo mínimo necessário entre execuções de avaliação sucessivas. Para o intervalo de substituição, utilize os passos abaixo.

1. No **criação** área de trabalho da consola do Operations Manager, procure a regra *Microsoft System Center Advisor SCOM avaliação executar avaliação regra do sistema* no **regras** secção.
2. Nos resultados da pesquisa, selecione de que inclui o texto *tipo: servidor de gestão*.
3. Clique com o botão direito a regra e, em seguida, clique em **substituir a regra** > **para todos os objetos da classe: servidor de gestão**.
4. Alterar o **intervalo** valor do parâmetro para o valor de intervalo pretendido. No exemplo abaixo, o valor é definido como 1440 minutos (dia).<br><br> ![parâmetro de intervalo](./media/log-analytics-scom-assessment/interval.png)<br>  

    Se o valor estiver definido como inferior a 1440 minutos, em seguida, a regra for executada num intervalo de um dia. Neste exemplo, a regra ignora o valor do intervalo e executa uma frequência de um dia.


## <a name="understanding-how-recommendations-are-prioritized"></a>Compreender a forma como as recomendações são prioritários

Cada recomendação efetuada é atribuída um valor de peso que identifica a importância relativa da recomendação. As 10 recomendações mais importantes são apresentadas.

### <a name="how-weights-are-calculated"></a>Como são calculadas ponderações

As ponderações são valores de agregação com base nas três principais fatores:

- O *probabilidade* que um problema identificado causará problemas. Uma probabilidade superior equivale a uma maior pontuação geral para a recomendação.
- O *impacto* do problema na sua organização se provocar um problema. Um impacto superior equivale a uma maior pontuação geral para a recomendação.
- O *esforço* necessário para implementar a recomendação. Um maior esforço equivale a uma pontuação geral menor para a recomendação.

O peso para cada recomendação é expresso como uma percentagem da classificação total disponível de cada área de foco. Por exemplo, se uma recomendação na área de foco de disponibilidade e continuidade do negócio tem uma pontuação de 5%, implementar dessa recomendação aumenta o geral disponibilidade e continuidade do negócio pontuação por 5%.

### <a name="focus-areas"></a>Áreas de foco

**Disponibilidade e continuidade do negócio** -esta área de foco mostra recomendações para a disponibilidade do serviço, a resiliência da sua infraestrutura e a proteção do negócio.

**Desempenho e escalabilidade** -esta área de foco mostra recomendações para o ajudar a sua organização infraestrutura de TI aumentar, certifique-se de que o ambiente de TI cumpre os requisitos de desempenho atuais e é capaz de responder a necessidades de infraestrutura a alteração.

**A atualização, a migração e a implementação** -esta área de foco mostra recomendações para o ajudar a atualizar, migrar e implemente o SQL Server para a sua infraestrutura existente.

**Operações e monitorização** -esta área de foco mostra recomendações para o ajudar a simplificar as operações de TI, implementar manutenção preventiva e maximizar o desempenho.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Deve procure para pontuar 100% cada área de foco?

Não necessariamente. As recomendações baseiam-se em conhecimentos e experiências adquiridas por engenheiros Microsoft entre milhares de visitas de cliente. No entanto, nenhum infraestruturas de dois servidor são os mesmos e ver as recomendações específicas podem ser maior ou menor relevantes para si. Por exemplo, algumas recomendações de segurança poderão ser menos relevantes se as máquinas virtuais não são expostas à Internet. Algumas recomendações de disponibilidade podem ser menos relevantes para os serviços que fornecem a recolha de dados ad hoc de baixa prioridade e relatórios. Os problemas que são importantes para uma empresa madura poderão ser menos importantes para um arranque. Poderá pretender identificar áreas que são as prioridades e, em seguida, observe como a sua pontuações alteram ao longo do tempo.

Cada recomendação inclui orientações sobre por que motivo é importante. Utilize esta orientação para avaliar se implementar a recomendação é adequada para si, dada a natureza do seu serviços de TI e as necessidades de negócio da sua organização.

## <a name="use-health-check-focus-area-recommendations"></a>Recomendações da área de foco de verificação do Estado de funcionamento de utilização

Antes de poder utilizar uma solução de verificação do Estado de funcionamento na análise de registos, tem de ter a solução instalada. Para ler mais sobre a instalação de soluções, consulte [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md). Depois de ser instalado, pode ver o resumo de recomendações através da utilização de mosaico do System Center Operations Manager estado de funcionamento da verificação do sistema na página de descrição geral do portal do OMS.

Ver as avaliações de conformidade resumidos para a sua infraestrutura e, em seguida, desagregação em recomendações.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver as recomendações para uma área de foco e tomar medidas corretivas
1. Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 
2. No portal do Azure, clique em **mais serviços** encontrado no canto inferior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Iniciar análise**.
3. No painel de subscrições de análise de registos, selecione uma área de trabalho e, em seguida, clique em de **Portal do OMS** mosaico.  
4. No **descrição geral** página, clique em de **Center Operations Manager estado de funcionamento da verificação do sistema** mosaico.
5. No **Center Operations Manager estado de funcionamento da verificação do sistema** página, reveja as informações de resumo de uma das seguintes painéis de área de foco e, em seguida, clique num para ver as recomendações para essa área de foco.
6. Em qualquer uma das páginas da área de foco, pode ver as recomendações prioritários efetuadas para o seu ambiente. Clique numa recomendação em **Objetos afetados** para ver detalhes sobre o motivo pelo qual a recomendação é feita.<br><br> ![área de foco](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Pode executar ações corretivas sugeridas na **as ações sugeridas**. Quando o item tem sido resolvido, irão registar avaliações posteriores que ações foram executadas e o modelo de pontuação de conformidade aumentam recomendadas. Itens corrigidas aparecem como **transmitido objetos**.

## <a name="ignore-recommendations"></a>Ignorar as recomendações

Se tiver recomendações que pretende ignorar, pode criar um ficheiro de texto que utiliza a análise de registos para impedir que as recomendações a apresentação nos resultados da avaliação.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Para identificar as recomendações que pretende ignorar
1. No portal do Azure na página da área de trabalho de análise de registos para a sua área de trabalho selecionada, clique em de **pesquisa registo** mosaico.
2. Utilize a seguinte consulta para recomendações de lista que falharam para os computadores no seu ambiente.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Eis uma captura de ecrã que mostra a consulta de pesquisa de registo:<br><br> ![pesquisa de registos](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Escolha as recomendações que pretende ignorar. Irá utilizar os valores para RecommendationId no procedimento seguinte.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Para criar e utilizar um ficheiro de texto IgnoreRecommendations.txt

1. Crie um ficheiro denominado IgnoreRecommendations.txt.
2. Colar ou escreva cada RecommendationId para cada recomendação que pretende que a análise de registos para ignorar numa linha separada e, em seguida, guarde e feche o ficheiro.
3. Coloque o ficheiro na seguinte pasta em cada computador onde pretende que a análise de registos para ignorar recomendações.
4. No servidor de gestão do Operations Manager - *SystemDrive*: \Programas\Microsoft System Center 2012 R2\Operations \ Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Para verificar se as recomendações são ignoradas

1. Depois da próxima agendada execuções de avaliação, por predefinição a cada sete dias, as recomendações especificadas estão marcadas ignoradas e não serão apresentados no dashboard de verificação do Estado de funcionamento.
2. Pode utilizar as seguintes consultas de pesquisa de registo para listar todas as recomendações ignoradas.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Se a sua área de trabalho tiver sido atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, a consulta acima alteraria o seguinte.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Se, posteriormente, decida se pretende ver as recomendações ignoradas, remova todos os ficheiros IgnoreRecommendations.txt ou pode remover RecommendationIDs dos mesmos.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Solução de verificação de estado de funcionamento do System Center Operations Manager do sistema FAQ

*Posso adicionar a solução de verificação do Estado de funcionamento a minha área de trabalho de análise de registos. Mas não consigo ver as recomendações. Por que motivo não?* Depois de adicionar a solução, utilize a vista de passos seguintes as recomendações no dashboard de análise de registos.  

- [Definir a conta Run As para o System Center Operations Manager estado de funcionamento da verificação do sistema](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurar a regra de System Center Operations Manager estado de funcionamento da verificação do sistema](#configure-the-health-check-rule)


*Existe uma forma de configurar a frequência de verificação é executada?* Sim. Consulte [configurar a frequência de execução](#configure-the-run-frequency).

*Se for detetado o outro servidor após posso adicionou a solução de avaliação do System Center Operations Manager, será-verificado?* Sim, após a deteção que é verificado de ora em diante, por predefinição a cada sete dias.

*O que é o nome do processo que a recolha de dados?* AdvisorAssessment.exe

*Em que executa o processo de AdvisorAssessment.exe?* AdvisorAssessment.exe é executado o processo de serviço de integridade do servidor de gestão em que a regra de verificação de estado de funcionamento está ativada. Utilizar esse processo, a deteção de todo o seu ambiente é conseguida através da recolha de dados remota.

*Quanto tempo demora para recolha de dados?* Recolha de dados no servidor demora cerca de uma hora. Pode demorar mais em ambientes que tenham várias instâncias do Operations Manager ou bases de dados.

*E se definir o intervalo da avaliação para menos de 1440 minutos?* A avaliação está pré-configurada para ser executada no máximo uma vez por dia. Se substituir o valor do intervalo para um valor inferior a 1440 minutos, em seguida, a avaliação utiliza 1440 minutos como o valor do intervalo.

*Como saber se existem falhas de pré-requisitos?* Se a verificação de estado de funcionamento foi executada e não consegue ver os resultados, em seguida, é provável que alguns dos pré-requisitos para a verificação falharam. Pode executar consultas: `Operation Solution=SCOMAssessment` e `SCOMAssessmentRecommendation FocusArea=Prerequisites` na pesquisa de registo para ver os pré-requisitos falhados.

*Não existe um `Failed to connect to the SQL Instance (….).` mensagem em falhas de pré-requisitos. O que é o problema?* AdvisorAssessment.exe, o processo que recolhe dados, é executado sob o processo de serviço de integridade no servidor de gestão. Como parte da verificação de estado de funcionamento, o processo tenta estabelecer ligação ao SQL Server onde a base de dados do Operations Manager está presente. Este erro pode ocorrer quando as regras de firewall bloquear a ligação à instância do SQL Server.

*Que tipo de dados é recolhido?* Os seguintes tipos de dados são recolhidos: - dados WMI - registo - dados de registo de eventos - Operations Manager dados através do Windows PowerShell, as consultas SQL e ficheiro de recoletor de informações.

*Por que motivo é necessário configurar uma conta Run As?* Com o Operations Manager, são executadas várias consultas SQL. Para os mesmos ser executada, tem de utilizar uma conta Run As com as permissões necessárias. Além disso, as credenciais de administrador local são necessárias para a consulta WMI.

*Por que motivo apresentar apenas as recomendações de 10 principais?* Em vez de dando-lhe obter uma lista exaustiva, muito confuso de tarefas, recomendamos que lhe concentrar-se no endereçamento as recomendações prioritários primeiro. Depois de resolvê-los, recomendações adicionais deixará de estar disponíveis. Se preferir ver a lista de detalhado, pode ver todas as recomendações com pesquisa de registo.

*Existe alguma forma para ignorar uma recomendação?* Sim, consulte o [ignorar recomendações](#Ignore-recommendations).


## <a name="next-steps"></a>Passos seguintes

- [Pesquisar registos](log-analytics-log-searches.md) para saber como analisar dados detalhados Center Operations Manager estado de funcionamento da verificação do sistema e as recomendações.
