---
title: "Monitorizar o desempenho de um a multi-inquilino SQL database do Azure numa aplicação SaaS multi-inquilino | Microsoft Docs"
description: "Monitorizar e gerir o desempenho da multi-inquilino SQL database do Azure numa aplicação SaaS multi-inquilino"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: sstein
ms.openlocfilehash: 3e97f0635a856256dd08c29d33d8058be9c8d8b4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorizar e gerir o desempenho da multi-inquilino SQL database do Azure numa aplicação SaaS multi-inquilino

Neste tutorial, são explorou vários cenários de gestão de chave de desempenho utilizados em aplicações SaaS. Utilizar um gerador de carga para simular atividade entre a bases de dados do multi-inquilinos, incorporada de monitorização e alertas funcionalidades da base de dados do SQL Server são demonstrados.

A aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino utiliza um modelo de dados de multi-inquilino fragmentados, onde os dados de venue (inquilino) são distribuídos por ID de inquilino por potencialmente várias bases de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão de utilização de base de dados típicas, bases de dados podem ser escalados para cima e para baixo para otimizar o custo de uma solução. Com este tipo de padrão, é importante monitorizar a utilização de recursos de base de dados para se certificar de que carrega razoavelmente é equilibradas entre potencialmente várias bases de dados. Também tem de garantir que as bases de dados individuais tem recursos adequados e não são atingir os seus [DTU](sql-database-what-is-a-dtu.md) limites. Este tutorial explicar formas de monitorizar e gerir bases de dados e como tomar uma ação corretiva em resposta a variações numa carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Simular a utilização de uma base de dados em partição horizontal do multi-inquilino através da execução de um gerador de carga fornecido
> * Monitorizar a base de dados como responder ao aumento de carga
> * Dimensionar a base de dados em resposta a carga de aumento da base de dados
> * Aprovisionar um inquilino para uma base de dados de inquilino único

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. 

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar a necessidade que monitorizar o desempenho manualmente, é mais eficaz **definir alertas que é acionam quando as bases de dados stray fora do normais intervalos**.
* Para responder a curto prazo flutuações do nível de desempenho da base de dados, o **nível DTU pode ser escalado para cima ou para baixo**. Se esta flutuação ocorre de forma normal ou previsível, **dimensionar a base de dados pode ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a duração mais longa flutuações ou alterações de inquilinos, **individuais inquilinos podem ser movidos para outra base de dados**.
* Para responder a aumentos de curta duração de *individuais* carga de inquilino, **inquilinos individuais podem ser efetuados fora de uma base de dados e atribuídos um nível de desempenho individuais**. Assim que a carga é reduzida, o inquilino, em seguida, pode ser reencaminhado para a base de dados do multi-inquilino. Quando isto é conhecido seguinte com antecedência, inquilinos podem ser movidos de pre-emptively Certifique-se de que a base de dados tem sempre os recursos que necessita e para evitar o impacto nos outros inquilinos na base de dados de multi-inquilino. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Para a base de dados do SQL Server, monitorização e alertas estão disponível em bases de dados. Esta monitorização incorporada e os alertas são recursos específicos, é conveniente utilizar pequenas quantidades de recursos, mas não for conveniente ao trabalhar com muitos recursos.

Para cenários de volume elevado, onde está a trabalhar com muitos recursos, [análise de registos (OMS)](https://azure.microsoft.com/services/log-analytics/) pode ser utilizado. Este é um serviço Azure separado que fornece análises através de registos de diagnóstico emitidos e telemetria recolhidos numa área de trabalho de análise de registo. Análise de registos pode recolher a telemetria de vários serviços e ser utilizado para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código de origem da aplicação de base de dados do Wingtip bilhetes SaaS multi-inquilino e os scripts

Os scripts de base de dados do Wingtip bilhetes SaaS multi-inquilino e o código fonte da aplicação, estão disponíveis no [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub. Veja o [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip SaaS de pedidos de desbloqueio.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Para compreender como funciona a gestão e monitorização do desempenho em escala, este tutorial requer que tenha vários inquilinos na base de dados do multi-inquilino.

Se já aprovisionou um lote de inquilinos um tutorial anterior, avance para o [simular utilização em todas as bases de dados do inquilino](#simulate-usage-on-all-tenant-databases) secção.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\monitorização de desempenho e gestão\\*demonstração PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, _Aprovisionar um lote de inquilinos_
1. Prima **F5** para executar o script.

O script implementa 17 inquilinos na base de dados de multi-inquilino dentro de alguns minutos. 

O *New-TenantBatch* script cria novos inquilinos com inquilino exclusivo chaves na base de dados em partição horizontal do multi-inquilino e inicializa-los com o tipo de nome e venue de inquilino. Isto é consistente com a forma como a aplicação aprovisiona um novo inquilino. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *demonstração PerformanceMonitoringAndManagement.ps1* script é fornecida que simula uma carga de trabalho em execução na base de dados do multi-inquilino. A carga é gerada utilizando uma das situações de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar a carga normal intensidade (aprox 30 DTU) |
| 3 | Gerar carga com mais bursts por inquilino|
| 4 | Gerar carga com superiores DTU bursts por inquilino (aprox 70 DTU)|
| 5 | Gerar uma elevado intensidade (aprox 90 DTU) num único inquilino plus uma intensidade normal carregar em todos os outros inquilinos |

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em DTUs), a duração e o intervalos são diversificados em todas as bases de dados, simulando atividade inquilino imprevisível.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\monitorização de desempenho e gestão\\*demonstração PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, _carga normal intensidade de gerar_
1. Prima **F5** para aplicar uma carga para todos os seus inquilinos.

Base de dados do Wingtip pedidos de suporte de SaaS multi-inquilino é uma aplicação SaaS e a carga do mundo real numa aplicação SaaS é normalmente esporádicas e imprevisíveis. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários alguns minutos para o padrão de carga ideia, por isso, execute o gerador de carga durante 3 a 5 minutos antes de tentar monitorizar a carga das secções seguintes.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas numa nova janela do PowerShell. Se fechar a sessão, deixa o gerador de carga. O gerador de carga permanece num *invocar a tarefa* Estado onde gera carga de quaisquer novos inquilinos aprovisionados depois do gerador é iniciado. Utilize *Ctrl-C* parar invocar novas tarefas e sair o script. O gerador de carga irá continuar a executar, mas apenas nos inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar a utilização de recursos no portal do Azure

Para monitorizar a utilização de recursos que resulta da carga a ser aplicada, abra o portal para a base de dados do multi-inquilino, **tenants1**, que contém os inquilinos:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o servidor *tenants1-mt -&lt;utilizador&gt;*.
1. Desloque para baixo e localizar as bases de dados e clique em **tenants1**. Esta base de dados do multi-inquilino em partição horizontal contém todos os inquilinos criados até ao momento.

![gráfico de base de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observar o **DTU** gráfico.

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho na base de dados

Definir um alerta na base de dados que aciona no \>75% de utilização da seguinte forma:

1. Abra o *tenants1* base de dados (no *tenants1-mt -&lt;utilizador&gt;*  servidor) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de DTU**
   * **Condição = superior a**
   * **Limiar = 75**.
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de e-mail para o *administrador adicionais email(s)* caixa e clique em **OK**.

   ![alerta de definição](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Dimensionar uma base de dados ocupado

Se aumentar o nível de carga numa base de dados para o ponto que maxes fora da base de dados e atingir a utilização DTU de 100%, em seguida, o desempenho de base de dados é afetado, potencialmente abrandamento tempos de resposta das consultas.

**A curto prazo**, considere como aumentar verticalmente a base de dados para fornecer recursos adicionais, ou a remoção de inquilinos da base de dados do multi-inquilino (movê-los fora da base de dados do multi-inquilino para uma base de dados autónomo).

**Termo mais**, considere a possibilidade de otimizar as consultas ou índice de utilização para melhorar o desempenho de base de dados. Dependendo de sensibilidade da aplicação para desempenho emite a melhor prática para dimensionar uma base de dados antes de atingir utilização 100%. Utilize um alerta para avisá-lo antecipadamente.

Pode simular uma base de dados ocupado através do aumento da carga produzida pelo gerador de dimensões. Fazendo com que os inquilinos a impulsar mais frequentemente e para já, aumentar a carga sobre a base de dados do multi-inquilino sem alterar os requisitos de inquilinos individuais. Como aumentar verticalmente a base de dados facilmente é efetuada no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Definir *$DemoScenario* = **3**, _carga gerar com maiores e mais frequentes bursts por base de dados_ para aumentar a intensidade da carga de agregação no base de dados sem alterar o pico de carga necessário para cada inquilino.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.
1. Vá para o **tenants1** base de dados no portal do Azure.

Monitorize a base de dados maior utilização da DTU no gráfico superior. Demora alguns minutos para uma carga maior nova iniciar sessão, mas deve ver rapidamente a base de dados começam a utilização máxima de acessos e, como a carga steadies para o novo padrão, sobrecarrega rapidamente a base de dados.

1. Para aumentar verticalmente a base de dados, clique em **escalão de preço (dimensionamento DTUs)** no painel Definições.
1. Ajustar o **DTU** definição **100**. 
1. Clique em **aplicar** para submeter o pedido de dimensionamento da base de dados.

Volte ao **tenants1** > **descrição geral** para ver os gráficos de monitorização. Monitorize o efeito de fornecer a base de dados com mais recursos (embora com algumas inquilinos e uma carga aleatório não seja sempre seja mais fácil ver conclusively até ser executado há algum tempo). Enquanto está a visualizar o gráficos, tenha em consideração que 100% gráfico superior agora representa 100 DTUs, enquanto no gráfico inferior 100% ainda é 50 DTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. Código da aplicação sempre deve ser escrito para repetir ignoradas ligações e, por isso, irá voltar a ligar à base de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprovisionar um novo inquilino na sua própria base de dados 

O modelo de multi-inquilino em partição horizontal permite-lhe escolher se pretende aprovisionar um novo inquilino na base de dados do multi-inquilino juntamente com outros inquilinos ou para aprovisionar o inquilino na base de dados do seu próprio. Por um inquilino na sua própria base de dados de aprovisionamento, beneficia do isolamento inerente na base de dados separada, permitindo-lhe gerir o desempenho desse inquilino independentemente de outras pessoas, restaurar esse inquilino independentemente de outras pessoas, etc. Por exemplo, pode optar por colocar os clientes de avaliação gratuita ou regulares numa base de dados multi-inquilino e os clientes de premium em bases de dados individuais.  Se as bases de dados de inquilino único isolados são criados, podem ainda ser geridos coletivamente num conjunto elástico para otimizar os custos de recursos.

Se já aprovisionado um novo inquilino na sua própria base de dados, ignore os próximos passos.

1. No **ISE do PowerShell**, abra... \\Learning módulos\\ProvisionTenants\\*demonstração ProvisionTenants.ps1*. 
1. Modificar **$TenantName = "Salix Salsa"** e **$VenueType = "dance"**
1. Definir **$Scenario** = **2**, _aprovisionar um inquilino na nova base de dados do inquilino único_
1. Prima **F5** para executar o script.

O script irá aprovisionar este inquilino na base de dados separada, registar a base de dados e de inquilino com o catálogo e, em seguida, abra a página de eventos do inquilino no browser. Atualize a página de Hub de eventos e verá "Salix Salsa" foi adicionado como um venue.

## <a name="manage-performance-of-a-single-database"></a>Gerir o desempenho de uma base de dados

Se um único inquilino dentro de uma base de dados do multi-inquilino sofre uma elevada carga constante, poderá confundir dominam os recursos de base de dados e afetar outros inquilinos na mesma base de dados. Se a atividade é provável que continuam durante algum tempo, o inquilino pode ser movido temporariamente fora da base de dados e na sua própria base de dados único inquilino. Isto permite que o inquilino para que os recursos adicionais que necessita e totalmente isola-lo a partir de outros inquilinos.

Neste exercício simula o efeito da Salix Salsa experienciar uma elevada carga quando acede bilhetes de venda para um evento popular.

1. Abra a... \\ *Demonstração PerformanceMonitoringAndManagement.ps1* script.
1. Definir **$DemoScenario = 5**, _gerar uma carga normal plus uma elevada carga num único inquilino (aprox 90 DTU)._
1. Definir **$SingleTenantName = Salix Salsa**
1. Execute o script com **F5**.

Aceda ao portal e navegue para **salixsalsa** > **descrição geral** para ver os gráficos de monitorização. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Dimensionamento de self-service de inquilino**

Porque o dimensionamento é uma tarefa facilmente chamado através de gestão de API, pode facilmente criar a capacidade de dimensionar bases de dados do inquilino na sua aplicação destinado ao inquilino e oferecem-la como uma funcionalidade do seu serviço de SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Dimensionar uma base de dados cima e baixo com base numa agenda para fazer corresponder os padrões de utilização**

Em que a utilização de agregação inquilino segue padrões de utilização previsíveis, pode utilizar o automatização do Azure para dimensionar uma base de dados cima e baixo com base numa agenda. Por exemplo, dimensionar uma base de dados para baixo após 18: 00 e cópia de segurança novamente antes de 6: 00 em dias da semana quando sabe que existe uma redução no requisitos de recursos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização de uma base de dados em partição horizontal do multi-inquilino através da execução de um gerador de carga fornecido
> * Monitorizar a base de dados como responder ao aumento de carga
> * Dimensionar a base de dados em resposta a carga de aumento da base de dados
> * Aprovisionar um inquilino para uma base de dados de inquilino único

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automatização do Azure](../automation/automation-intro.md)