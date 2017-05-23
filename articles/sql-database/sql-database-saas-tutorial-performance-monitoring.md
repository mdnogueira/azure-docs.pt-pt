---
title: "Monitorizar o desempenho de uma aplicação SaaS da Base de Dados SQL | Microsoft Docs"
description: "Monitorizar e gerir o desempenho da aplicação Wingtip Tickets (WTP) de exemplo da Base de Dados SQL do Azure"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: af9511978718af10c97bee6af3a2835c9d2c1ff4
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Monitorizar o desempenho da aplicação SaaS WTP de exemplo

Neste tutorial, são demonstradas as funcionalidades incorporadas de monitorização e de alertas da Base de Dados SQL e dos conjuntos elásticos e, em seguida, serão explorados vários cenários-chave de gestão do desempenho utilizados nas aplicações SaaS.

A aplicação Wingtip Tickets utiliza um modelo de dados de inquilino individual, onde cada local (inquilino) tem a sua própria base de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão de utilização típica da base de dados, as bases de dados de inquilinos são implementadas em conjunto de bases de dados elásticas. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](sql-database-what-is-a-dtu.md). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação WTP está implementada. Para implementar em menos de cinco minutos, veja [Implementar e explorar a aplicação SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos Padrões de Gestão de Desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao alojar vários inquilinos, os Conjunto de bases de dados elásticas são uma forma económica de fornecer e gerir os recursos de um grupo de bases de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto. Um conjunto não partilha apenas o custo dos recursos como também pode eliminar a necessidade de monitorizar e controlar constantemente as bases de dados individuais.

![suporte de dados](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

Os conjuntos, bem como as bases de dados nos conjuntos, continuam a ter de ser monitorizados para garantir que os mesmos permanecem dentro dos intervalos de desempenho aceitáveis. Otimize a configuração dos conjuntos para satisfazer as necessidades da carga de trabalho agregada ao garantir que as eDTUs de conjunto são adequadas para a carga de trabalho geral. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que serão acionados se os conjuntos ou as bases de dados se afastarem dos intervalos normais**.
* Para responder a flutuações de curta duração no nível de desempenho agregado de um conjunto, o **nível de eDTU do conjunto pode ser aumentado ou reduzido verticalmente**. Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos de curto prazo na carga das bases de dados *individuais*, **as bases de dados individuais podem ser retiradas do conjunto e atribuídas um nível de desempenho individual** durante um período. Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Sempre que este processo é conhecido antecipadamente, as bases de dados podem ser movidas previamente para garantir que a base de dados tem sempre os recursos de que necessita e para evitar o impacto nas outras bases de dados do conjunto. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Estes alertas e monitorização incorporados são específicos do recurso; por isso, é útil para utilização em pequenos números de recursos, mas não é muito útil quando se trabalha com muitos recursos.

Para cenários de volume elevado, pode ser utilizado o Log Analytics (também conhecido como OMS). Este é um serviço do Azure separado que fornece análises sobre os registos de diagnóstico emitidos e a telemetria recolhida na área de trabalho do Log Analytics, que pode recolher telemetria de muitos serviços e ser utilizado para consultar e definir alertas.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se já tiver aprovisionado um lote de inquilinos num tutorial anterior, poderá avançar para a secção [Simular utilização em todas as bases de dados de inquilinos](#simulate-usage-on-all-tenant-databases).

1. Abra ...\\Módulos de Aprendizagem\\Aprovisionamento e Catalogação\\*Demo-PerformanceMonitoringAndManagement.ps1* no **ISE do PowerShell**. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Aprovisionar um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O script *New-TenantBatch* utiliza um conjunto de modelos do [Resource Manager](../azure-resource-manager/index.md) ligado ou aninhado para criar um lote de inquilinos, que, por predefinição, copia a base de dados **baseTenantDb** no servidor de catálogo para criar as novas bases de dados de inquilinos, em seguida, regista-as no catálogo e, por fim, inicializa-as com o nome de inquilino e o tipo de local. Este procedimento é consistente com a forma como a aplicação WTP aprovisiona um novo inquilino. Quaisquer alterações efetuadas à *baseTenantDB* serão aplicadas a quaisquer novos inquilinos aprovisionados a partir desse momento. Veja o [Tutorial de Gestão de Esquemas](sql-database-saas-tutorial-schema-management.md) para ver como realizar as alterações dos esquemas nas bases de dados de inquilinos *existentes* (incluindo a base de dados *dourada*).

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Simular diferentes padrões de utilização através da geração de diferentes tipos de carga

O script *Demo-PerformanceMonitoringAndManagement.ps1* inicia o gerador de carga com um dos *tipos de carga* disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aprox. 40 DTUs) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com picos de DTU mais altos por base de dados (aprox. 80 DTUs)|
| 5 | Gerar uma carga normal + uma carga elevada num inquilino individual (aprox. 95 DTUs)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. Defina **$DemoScenario** = **2**, *Gerar uma carga de intensidade normal*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

Devido à natureza esporádica da carga, permita que o gerador trabalhe durante 10 a 20 minutos para que a atividade possa atingir um estado estável e criar um padrão regular de atividade.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido.

## <a name="monitor-resource-usage-using-the-portal"></a>Monitorizar a utilização de recursos através do portal

Para monitorizar a utilização de recursos resultantes da carga que está a ser aplicada, abra o portal para o conjunto que contém as bases de dados de inquilinos.

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até ao servidor tenants1 -&lt;UTILIZADOR&gt;.
1. Deverá ver a lista de bases de dados de inquilinos, incluindo o novo lote de bases de dados.
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.
1. Expanda o painel de conjunto apresentado e observe o gráfico de utilização do conjunto e o gráfico de maior utilização da base de dados.

A utilização do conjunto é essencialmente o agregado da utilização da base de dados para todas as bases de dados no conjunto. O gráfico de utilização das bases de dados mostra as 5 bases de dados mais utilizadas:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Uma vez que existem outras bases de dados no conjunto além das 5 mais utilizadas, a utilização do conjunto mostra a atividade que não é refletida no gráfico das cinco bases mais utilizadas. Para ver alguns detalhes adicionais, clique em **Utilização de Recursos de Bases de Dados**:

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Defina um alerta no conjunto acionado ao atingir uma utilização constante \>75% durante cinco minutos, da seguinte forma:

1. Abra *Pool1* (no servidor *tenants1-\<utilizador\>*) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**, 
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = maior que**.
   * **Limiar = 75**.
   * **Período = Nos últimos 30 minutos**.

   ![alerta de definição](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Pode receber notificações no seu e-mail da conta do Azure e, opcionalmente, em outros endereços de e-mail adicionais (recomendamos não definir esta opção, a menos que seja o proprietário da subscrição que está a ser utilizada).

> [!NOTE]
> Como o alerta será acionado apenas se o limiar for excedido durante os últimos 30 minutos, o gerador de carga tem de estar em execução durante mais de 30 minutos para testar o alerta.


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

A curto prazo, considere aumentar verticalmente o conjunto para fornecer recursos adicionais ou remover bases de dados do conjunto (movê-las para outros conjuntos ou para fora do conjunto para uma camada de serviço autónomo).

A longo prazo, considere a otimização das consultas ou a utilização de índices para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazer com que as bases de dados tenham picos com mais frequência e durante mais tempo aumenta a carga agregada no conjunto sem alterar os requisitos das bases de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais demorados e mais frequentes por base de dados_ para aumentar a intensidade da carga agregada no conjunto sem alterar a carga de pico necessária para cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.


1. **Abra o painel do conjunto** **para tenants1/Pool1**.


1. Monitorize a maior utilização da DTU do conjunto no gráfico superior. Leva alguns minutos para a nova carga superior surgir, mas pode ver rapidamente o conjunto a começar a alcançar uma utilização de 100% à medida que a carga estabiliza num novo padrão, que rapidamente sobrecarrega o conjunto.


1. Para aumentar verticalmente o conjunto, clique em **Configurar conjunto**
1. Ajuste o controlo de deslize de **eDTU do conjunto** para 100 (recomendamos que não utilize um valor superior para limitar os custos). Observe como o armazenamento agregado disponível para todas as bases de dados no conjunto, indicado por **GB do conjunto**, está associado à definição de eDTU e também aumenta. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Pode ver as definições por base de dados no lado direito no painel **Configurar conjunto**.
1. Clique em **Guardar** para submeter o pedido. A alteração demorará, normalmente, 3 a 5 minutos para um conjunto Padrão.

Regresse a **Pool1** > **Descrição Geral** para ver os gráficos de monitorização. Monitorize o efeito da adição de mais recursos ao conjunto (embora com algumas bases de dados e uma carga aleatória não seja sempre fácil ver as consequências). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Criar um segundo conjunto e balancear a carga das bases de dados para processar o aumento da carga agregada

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. Abra o **painel de servidor para customers1-&lt;UTILIZADOR&gt;**. Se tiver um painel de base de dados ou de conjunto, pode clicar no menu pendente do controlo Essentials e selecionar o nome do servidor como atalho.
1. Clique em **+ Novo conjunto** para criar um conjunto no servidor atual
1. No novo modelo Conjunto de bases de dados elásticas:

    1. Defina **Nome = Pool2**.
    1. Deixe o escalão de preço como **Conjunto Padrão**.
    1. Clique em **Configurar conjunto**.
    1. No painel Configurar Conjunto apresentado, defina **eDTU do Conjunto = 50 DTUs**.
    1. Clique no comando **Adicionar bases de dados** para ver uma lista das bases de dados neste servidor que não estão no conjunto atual.
    1. Na lista, **marque** metade das bases de dados (10 em 20) para movê-las para o novo conjunto e, em seguida, clique em **Selecionar**.
    1. Clique em **Selecionar** novamente para aceitar as alterações à configuração. Tenha em atenção a estimativa de custos para um mês de utilização com as opções selecionadas.
    1. Selecione **OK** para criar o novo conjunto com a nova configuração e para mover as bases de dados.

Criar o conjunto e mover as bases de dados para o conjunto demora alguns minutos. Cada uma das bases de dados a ser movida mantém-se online e totalmente acessível até ao último momento, altura em que todas as ligações abertas são fechadas. Quando um cliente tenta novamente a ligação, irá ligar-se à base de dados no novo conjunto.

Quando o conjunto tiver sido criado, este será apresentado no painel de servidor customers1. Clique no nome do conjunto para abrir o painel de conjunto e monitorizar o seu desempenho.

Deverá ver que a utilização de recursos em Pool1 caiu e que Pool2 tem uma carga idêntica.

## <a name="manage-an-increased-load-on-a-single-database"></a>Gerir um aumento de carga numa base de dados individual

Se uma base de dados individual num conjunto tiver uma carga elevada constante, consoante a configuração do conjunto, essa base de dados poderá ter tendência para dominar os recursos no conjunto e afetar as outras bases de dados. Se for previsível que a atividade continue durante algum tempo, a base de dados poderá ser movida temporariamente para fora do conjunto. Este procedimento permite que a base de dados receba mais recursos do que as outras no conjunto e isola-a das outras bases de dados. Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. No script ...\\ **Demo-PerformanceManagementAndMonitoring**.ps1
1. Defina **$DemoScenario = 5, Gerar uma carga normal mais uma carga elevada num inquilino individual (aprox. 95 DTUs).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.
1. **Abra o painel de conjunto** **para Customers1/Pool1**.
1. Observe a **Monitorização do conjunto elástico**, mostrada na parte superior do painel e repare no aumento de utilização da DTU do conjunto. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
1. Monitorize também o que está em **Monitorização das bases de dados elásticas**, que mostra as bases de dados mais utilizadas na hora anterior. A base de dados contosoconcerthall deverá ser logo apresentada como uma das 5 bases de dados mais utilizadas.
1. Clique no **gráfico** **da Monitorização das bases de dados elásticas** para ver um painel **Utilização de Recursos de Bases de Dados**, no qual pode monitorizar de forma seletiva qualquer uma das bases de dados. Este procedimento permite-lhe isolar a apresentação da base de dados contosoconcerthall.
1. Na lista de bases de dados, **clique em contosoconcerthall** para ver o painel da base de dados.
1. Clique em **Escalão de Preço (DTUs de escalamento)**, no menu de contexto, para abrir o painel **Configurar o desempenho**, no qual pode definir um nível de desempenho isolado para a base de dados.
1. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
1. Mova o **controlo de deslize da DTU** para a direita para selecionar 100 DTUs. Tenha em atenção que este corresponde ao objetivo de serviço, **S3,** mostrado entre parênteses retos entre a DTU e os Contadores de tamanho de armazenamento.
1. Clique em **Aplicar** para mover a base de dados sem o conjunto e torná-la numa base de dados S3 Padrão.
1. Assim que a implementação estiver concluída, monitorize as consequências na base de dados contosoconcerthall e o conjunto que foi removido nos painéis de conjunto elástico e de base de dados.

Depois do aumento da carga normal na base de dados contosoconcerthall ter diminuído, deverá poder devolvê-la ao conjunto para reduzir o seu custo. Se não tiver a certeza de quando tal irá acontecer, poderá configurar um alerta na base de dados que será acionado quando a sua utilização de DTUs descer abaixo do máximo por base de dados no conjunto. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros Padrões de Gestão do Desempenho

**Dimensionamento preventivo** – No exercício 6, onde explorou como dimensionar uma base de dados isolada quando se conhece a base de dados a procurar. Se a gestão de Contoso Concert Hall tivesse informa a WTP da venda de bilhetes iminente, a base de dados poderia ter sido movida preventivamente para fora do conjunto. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

**Dimensionamento de self-service do inquilino** – Porque o dimensionamento é uma tarefa chamada facilmente através de API de gestão, pode criar facilmente a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Aumentar e reduzir verticalmente um conjunto com base numa agenda para corresponder aos padrões de utilização

Quando a utilização agregada de inquilinos segue padrões de utilização previsível, pode utilizar a Automatização do Azure para aumentar ou reduzir verticalmente um conjunto com base numa agenda. Por exemplo, reduza verticalmente um conjunto após as 18:00 e aumente-o verticalmente antes das 06:00 nos dias da semana quando sabe que existe uma quebra nos requisitos de recursos.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados

[Tutorial Restaurar um inquilino individual](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Conjuntos elásticos SQL](sql-database-elastic-pool.md)
* [Automatização do Azure](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) – Tutorial de configuração e utilização do Log Analytics
