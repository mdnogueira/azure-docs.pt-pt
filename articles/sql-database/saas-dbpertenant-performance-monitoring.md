---
title: "Monitorizar o desempenho de muitas bases de dados SQL do Azure numa aplicação SaaS multi-inquilino | Microsoft Docs"
description: "Monitorizar e gerir o desempenho de bases de dados SQL do Azure e agrupamentos numa aplicação SaaS multi-inquilino"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: sstein
ms.openlocfilehash: 289f1f99b1661e499fa7132887e2f65e086ad689
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorizar e gerir o desempenho de bases de dados SQL do Azure e agrupamentos numa aplicação SaaS multi-inquilino

Neste tutorial, são explorou vários cenários de gestão de chave de desempenho utilizados em aplicações SaaS. Utilizar um gerador de carga para simular a atividade em todas as bases de dados do inquilino, incorporada de monitorização e alertas funcionalidades da base de dados SQL e conjuntos elásticos são demonstrados.

A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino utiliza um modelo de dados de inquilino único, onde cada venue (inquilino) tem a sua própria base de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão de utilização típica da base de dados, as bases de dados de inquilinos são implementadas em conjunto de bases de dados elásticas. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](sql-database-what-is-a-dtu.md). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de Wingtip bilhetes SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao alojar vários inquilinos, os Conjunto de bases de dados elásticas são uma forma económica de fornecer e gerir os recursos de um grupo de bases de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto.

![Diagrama de aplicação](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Os conjuntos e as bases de dados em conjuntos, devem ser monitorizados para se certificar de que permanecem nos intervalos aceitáveis de desempenho. Otimize a configuração do agrupamento para satisfazer as necessidades da carga de trabalho agregada de todas as bases de dados, assegurar que os eDTUs de conjunto estão adequadas para a carga de trabalho geral. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar a necessidade que monitorizar o desempenho manualmente, é mais eficaz **definir alertas acionam quando ou agrupamentos de bases de dados stray fora do normais intervalos**.
* Para responder a flutuações de curta duração no nível de desempenho agregado de um conjunto, o **nível de eDTU do conjunto pode ser aumentado ou reduzido verticalmente**. Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos de curta duração de *individuais* carga de base de dados **bases de dados individuais podem ser efetuadas fora de um conjunto e atribuídos um nível de desempenho individuais**. Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Quando isto é conhecido seguinte com antecedência, bases de dados podem ser movidas de pre-emptively para assegurar que a base de dados sempre os recursos que necessita de e para evitar o impacto nas outras bases de dados no conjunto. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Esta monitorização incorporada e os alertas são recursos específicos, é conveniente utilizar pequenas quantidades de recursos, mas não é muito conveniente ao trabalhar com muitos recursos.

Para cenários de volume elevado, onde está a trabalhar com muitos recursos, [análise de registos (OMS)](saas-dbpertenant-log-analytics.md) pode ser utilizado. Este é um serviço Azure separado que fornece análises através de registos de diagnóstico emitidos e telemetria recolhidos numa área de trabalho de análise de registo. Análise de registos pode recolher a telemetria de vários serviços e ser utilizado para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicação Wingtip bilhetes SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se já um lote de inquilinos que aprovisionou um tutorial anterior, avance para o [simular utilização em todas as bases de dados do inquilino](#simulate-usage-on-all-tenant-databases) secção.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\monitorização de desempenho e gestão\\*demonstração PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Aprovisionar um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O *New-TenantBatch* script utiliza um conjunto aninhado ou ligado de [Resource Manager](../azure-resource-manager/index.md) modelos que criam um lote de inquilinos, que, por predefinição, copia a base de dados **basetenantdb**no servidor de catálogo para criar o novo inquilino bases de dados, em seguida, regista estes no catálogo e, finalmente, inicializa-los com o tipo de nome e venue de inquilino. Isto é consistente com a forma como a aplicação aprovisiona um novo inquilino. As alterações efetuadas ao *basetenantdb* são aplicadas a qualquer novos inquilinos aprovisionados após essa data. Consulte o [tutorial de gestão de esquema](saas-tenancy-schema-management.md) para ver como efetuar alterações de esquema para *existente* inquilino bases de dados (incluindo o *basetenantdb* base de dados).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *demonstração PerformanceMonitoringAndManagement.ps1* script é fornecida que simula uma carga de trabalho em execução em todas as bases de dados do inquilino. A carga é gerada utilizando uma das situações de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aprox. 40 DTUs) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com picos de DTU mais altos por base de dados (aprox. 80 DTUs)|
| 5 | Gerar uma carga normal + uma carga elevada num inquilino individual (aprox. 95 DTUs)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\monitorização de desempenho e gestão\\*demonstração PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, *carga normal intensidade de gerar*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

Wingtip pedidos de suporte de SaaS da base de dados por inquilino é uma aplicação SaaS e a carga do mundo real numa aplicação SaaS é normalmente esporádicas e imprevisíveis. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários alguns minutos para o padrão de carga ideia, por isso, execute o gerador de carga durante 3 a 5 minutos antes de tentar monitorizar a carga das secções seguintes.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido. O gerador de carga permanece num *invocar a tarefa* Estado onde gera carga de quaisquer novos inquilinos aprovisionados depois do gerador é iniciado. Utilize *Ctrl-C* parar invocar novas tarefas e sair o script. O gerador de carga irá continuar a executar, mas apenas nos inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar a utilização de recursos no portal do Azure

Para monitorizar a utilização de recursos que resulta da carga a ser aplicada, abra o portal para o conjunto que contêm as bases de dados do inquilino:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o *tenants1-dpt -&lt;utilizador&gt;*  servidor.
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.

Observar o **monitorização do conjunto elástico** e **monitorização de base de dados elástica** gráficos.

A utilização de recursos do conjunto é a utilização de agregação da base de dados para todas as bases de dados no conjunto. O gráfico de base de dados mostra as bases de dados hottest cinco:

![gráfico de base de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Porque existem bases de dados adicionais no agrupamento para além dos cinco superior, a utilização de agrupamento mostra atividade que não será refletida no gráfico de bases de dados de cinco superior. Para obter mais detalhes, clique em **utilização de recursos de base de dados**:

![utilização de recursos de base de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Definir um alerta no conjunto que aciona no \>75% de utilização da seguinte forma:

1. Abra *Pool1* (no *tenants1-dpt -\<utilizador\>*  servidor) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = superior a**
   * **Limiar = 75**
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de e-mail para o *administrador adicionais email(s)* caixa e clique em **OK**.

   ![alerta de definição](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

**Curta duração**, considere como aumentar verticalmente o conjunto para fornecer recursos adicionais, ou remover bases de dados do conjunto (movê-los para outros conjuntos ou fora do conjunto para uma camada de serviço autónomo).

**Termo mais**, considere a possibilidade de otimizar as consultas ou índice de utilização para melhorar o desempenho de base de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que as bases de dados impulsar mais frequentemente e para já, aumentar a carga no conjunto de agregação sem alterar os requisitos das bases de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais demorados e mais frequentes por base de dados_ para aumentar a intensidade da carga agregada no conjunto sem alterar a carga de pico necessária para cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

1. Aceda a **Pool1** no portal do Azure.

Monitorize a utilização de eDTU do conjunto de aumento no gráfico superior. Demora alguns minutos para uma carga maior nova iniciar sessão, mas deve ver rapidamente o conjunto de começar a utilização máxima de acessos e como a carga steadies para o novo padrão, rapidamente sobrecarrega o conjunto.

1. Para aumentar verticalmente o conjunto, clique em **configurar conjunto** na parte superior do **Pool1** página.
1. Ajustar o **eDTU do conjunto** definição **100**. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Pode ver as definições de por base de dados no lado direito do **configurar conjunto** página.
1. Clique em **guardar** para submeter o pedido de conjunto de dimensionamento.

Volte ao **Pool1** > **descrição geral** para ver os gráficos de monitorização. Monitorize o efeito de fornecer o conjunto com mais recursos (embora com algumas bases de dados e uma carga aleatório não seja sempre seja mais fácil ver conclusively até ser executado há algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="load-balance-between-pools"></a>Balanceamento de carga entre conjuntos

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. No [portal do Azure](https://portal.azure.com), abra o **tenants1-dpt -&lt;utilizador&gt;**  servidor.
1. Clique em **+ novo conjunto** para criar um agrupamento no servidor atual.
1. No **conjunto de bases de dados elásticas** modelo:

    1. Definir **nome** para *Pool2*.
    1. Deixe o escalão de preço como **Conjunto Padrão**.
    1. Clique em **Configurar conjunto**.
    1. Definir **eDTU do conjunto** para *50 eDTU*.
    1. Clique em **adicionar bases de dados** para ver uma lista de bases de dados no servidor que pode ser adicionado a *Pool2*.
    1. Selecione qualquer 10 bases de dados para mover estes para o novo agrupamento e, em seguida, clique em **selecione**. Se tiver sido a executar o gerador de carga, o serviço já sabe que o seu perfil de desempenho necessita de um conjunto maior do que o tamanho predefinido da eDTU 50 e recomenda a começar com uma definição de eDTU 100.

    ![Recomendação](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Para este tutorial, deixe a predefinição com 50 eDTUs e clique em **selecione** novamente.
    1. Selecione **OK** para criar o novo agrupamento e para mover as bases de dados selecionadas para a mesma.

Criar o conjunto e mover as bases de dados demora alguns minutos. Como as bases de dados são movidos permanecem online e acessível a totalmente até que o último muito pouco, altura em que todas as ligações abertas são fechadas. Desde que tenham algumas lógica de repetição, os clientes, em seguida, irão ligar à base de dados do novo conjunto.

Navegue até à **Pool2** (no *tenants1-dpt -\<utilizador\>*  servidor) para abrir o conjunto e monitorizar o desempenho dele. Se não o vir, aguarde que o aprovisionamento do novo conjunto a concluir.

Verá então que a utilização de recursos no *Pool1* interrompeu e que *Pool2* da mesma forma já foi carregadas.

## <a name="manage-performance-of-a-single-database"></a>Gerir o desempenho de uma base de dados

Se uma base de dados individual num conjunto tiver uma carga elevada constante, consoante a configuração do conjunto, essa base de dados poderá ter tendência para dominar os recursos no conjunto e afetar as outras bases de dados. Se a atividade é provável que continuam durante algum tempo, a base de dados pode ser movido temporariamente fora do conjunto. Isto permite que a base de dados para que os recursos adicionais que necessita e isola-lo a partir de outras bases de dados.

Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. No **ISE do PowerShell**, abra a... \\ *Demonstração PerformanceMonitoringAndManagement.ps1* script.
1. Defina **$DemoScenario = 5, Gerar uma carga normal mais uma carga elevada num inquilino individual (aprox. 95 DTUs).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.


1. No [portal do Azure](https://portal.azure.com), navegue para a lista de bases de dados no *tenants1-dpt -\<utilizador\>*  servidor. 
1. Clique em de **contosoconcerthall** base de dados.
1. Clique no conjunto que **contosoconcerthall** está a ser. Localize o conjunto no **conjunto de bases de dados elásticas** secção.

1. Inspecione o **monitorização do conjunto elástico** gráfico e procure a utilização de eDTU do conjunto maior. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
2. Inspecione o **monitorização de base de dados elástica** apresentar, que mostra as bases de dados hottest na última hora. O *contosoconcerthall* base de dados em breve deve aparecer como uma das bases de dados hottest cinco.
3. **Clique em monitorização de base de dados elástica** **gráfico** e é aberto o **utilização de recursos de base de dados** página onde é possível monitorizar qualquer uma das bases de dados. Isto permite-lhe isolar a apresentar para o *contosoconcerthall* base de dados.
4. Na lista de bases de dados, clique em **contosoconcerthall**.
5. Clique em **escalão de preço (dimensionamento DTUs)** para abrir o **Configurar desempenho** página onde pode definir um nível de desempenho autónomo para a base de dados.
6. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
7. Faça deslizar a **controlo de deslize DTU** para a direita para selecionar **100** DTUs. Note que este corresponde ao objetivo de serviço, **S3**.
8. Clique em **aplicar** para mover a base de dados fora do conjunto e torná-lo um *Standard S3* base de dados.
9. Depois de concluída a dimensionamento, monitorize o efeito na base de dados contosoconcerthall e Pool1 nos painéis de base de dados e agrupamento elásticos.

Assim que a carga elevada na base de dados contosoconcerthall subsides retomadas rapidamente o deve regressar ao agrupamento para reduzir o custo. Se não é claro quando que irá acontecer pode configurar um alerta na base de dados que é acionado quando a utilização da DTU descerem abaixo o por base de dados máximo no conjunto. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Dimensionamento preventivas** no exercício acima onde, explorou como dimensionar uma base de dados isolado, souberem base de dados para procurar. Se a gestão de Contoso Concert Hall tinha informado Wingtips de venda permissão iminente, a base de dados foi terem sido movido fora do conjunto pre-emptively. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

**Dimensionamento de self-service do inquilino** – Porque o dimensionamento é uma tarefa chamada facilmente através de API de gestão, pode criar facilmente a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Dimensionamento de um conjunto de cima e baixo com base numa agenda para fazer corresponder os padrões de utilização**

Quando a utilização agregada de inquilinos segue padrões de utilização previsível, pode utilizar a Automatização do Azure para aumentar ou reduzir verticalmente um conjunto com base numa agenda. Por exemplo, reduza verticalmente um conjunto após as 18:00 e aumente-o verticalmente antes das 06:00 nos dias da semana quando sabe que existe uma quebra nos requisitos de recursos.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados

[Tutorial Restaurar um inquilino individual](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais tirar partido de implementação de aplicação Wingtip bilhetes SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Conjuntos elásticos SQL](sql-database-elastic-pool.md)
* [Automatização do Azure](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) – Tutorial de configuração e utilização do Log Analytics
