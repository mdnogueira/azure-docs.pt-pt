---
title: "Dashboard do Power BI para o estado de funcionamento vehicle e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
ms.openlocfilehash: 39be936520d62cb1c1c28de9bd72f8f489166082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instruções de configuração ao Dashboard do Power BI do modelo de solução vehicle telemetria análise
Isto **menu** ligações para capítulos neste manual de comunicação social. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A solução de análise de telemetria Vehicle showcases como dealerships carro, fabricantes automóveis e as empresas insurance podem tirar partido das capacidades do Cortana Intelligence para obterem em tempo real e conhecimentos aprofundados preditivos acerca do Estado de funcionamento vehicle e ocasionar hábitos para melhoramentos de unidade na área de cliente experiência R & D e campanhas de marketing. Este documento contém instruções passo a passo sobre como configurar os relatórios do Power BI e o dashboard assim que a solução for implementada na sua subscrição. 

## <a name="prerequisites"></a>Pré-requisitos
1. Implementar o [análise de telemetria](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) solução  
2. [Instalar o ambiente de trabalho do Microsoft Power BI](http://www.microsoft.com/download/details.aspx?id=45331)
3. Um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/). Se não tiver uma subscrição do Azure, comece subscrição gratuita do Azure
4. Conta do Microsoft Power BI

## <a name="cortana-intelligence-suite-components"></a>Componentes do Cortana Intelligence Suite
Como parte do modelo de solução de análise de telemetria Vehicle, os seguintes serviços do Cortana Intelligence são implementados na sua subscrição.

* **Hub de eventos** para ingestão de milhões de eventos de telemetria vehicle relacionadas no Azure.
* **Stream Analytics** para obter informações em tempo real no estado de funcionamento vehicle e persistir os dados para armazenamento a longo prazo para a análise de lote mais rico.
* **Machine Learning** para deteção de anomalias em tempo real e processamento em lote para obter informações acerca de preditiva.
* **HDInsight** é utilizado para transformar dados à escala
* **Fábrica de dados** processa orchestration, agendamento, de gestão de recursos e monitorizar o pipeline de processamento em lote.

**Power BI** fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva. 

A solução utiliza duas origens de dados diferentes: **Simulated vehicle sinais e conjunto de dados de diagnóstico** e **catálogo vehicle**.

Um simulador de telematics vehicle é incluído como parte desta solução. Este emite informações de diagnóstico e sinalizar correspondente para o estado do veículo e ocasionar padrão num determinado ponto no tempo. 

O catálogo de Vehicle é um conjunto de dados de referência que contêm VIN para mapeamento de modelo

## <a name="power-bi-dashboard-preparation"></a>Preparação do Power BI Dashboard
### <a name="setup-power-bi-real-time-dashboard"></a>Configurar o Dashboard do Power BI em tempo real

**Iniciar a aplicação de dashboard em tempo real** depois de concluída a implementação, deve seguir as instruções de operação de Manual

* Transferir a aplicação de dashboard em tempo real RealtimeDashboardApp.zip e deszipe-lo.
*  Na pasta deszipada, abra o ficheiro de configuração de aplicação 'RealtimeDashboardApp.exe.config' appSettings de substituição para ligações de serviço de Eventhub, armazenamento de BLOBs e ML com os valores nas instruções de operação Manual e guarde as alterações.
* Execute a aplicação RealtimeDashboardApp.exe. Uma janela de início de sessão irá aparecer, forneça as credenciais do PowerBI válidas e clique em de **aceitar** botão. Em seguida, a aplicação irá começar a executar.

   ![Início de sessão no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
   ![Permissões do Power BI Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

* Inicie sessão no Web site do Power BI e criar o dashboard em tempo real.

Agora, está pronto para configurar o dashboard do Power BI com visualizações otimizadas para obterem em tempo real e conhecimentos aprofundados preditivos acerca do Estado de funcionamento vehicle e ocasionar hábitos. Demora cerca de 45 minutos para uma hora para criar todos os relatórios e configurar o dashboard. 

### <a name="configure-power-bi-reports"></a>Configurar os relatórios do Power BI
Os relatórios em tempo real e o dashboard demorar cerca de 30 45 minutos a concluir. Navegue até à [http://powerbi.com](http://powerbi.com) e início de sessão.

![Início de sessão no Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

É gerado um novo conjunto de dados no Power BI. Clique em de **ConnectedCarsRealtime** conjunto de dados.

![Conjunto de dados em tempo real do carros Selecte ligado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Guardar o relatório em branco utilizando **Ctrl + s**.

![Guardar o relatório em branco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Forneça o nome do relatório *Vehicle telemetria análise em tempo real - relatórios*.

![Forneça o nome do relatório](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Relatórios em tempo real
Nesta solução, existem três relatórios em tempo real:

1. Veículos na operação
2. Veículos necessidade de manutenção
3. Estatísticas de estado de funcionamento de veículos

Pode optar por configurar todos os relatórios em tempo real três ou parou após qualquer fase e avance para a secção seguinte para configurar os relatórios de batch. Recomendamos a criação de todos os três relatórios para visualizar as informações do caminho em tempo real da solução completas.  

### <a name="1-vehicles-in-operation"></a>1. Veículos na operação
Faça duplo clique em **página 1** e renomeie-o para "Veículos na operação"  
    ![Veículos na operação de carros - ligados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Selecione **vin** campo do **campos** e escolha o tipo de visualização como **"Card"**.  

Visualização do cartão é criada, conforme mostrado na figura.  
    ![Carros ligados - selecione vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Clique na área em branco para adicionar a nova visualização.  

Selecione **Cidade** e **vin** de campos. Alterar a visualização para **"Mapa"**. Arraste **vin** na área de valores. Arraste **Cidade** de campos para **legenda** área.   
    ![Ligado carros - visualização do cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

Selecione **formato** secção **visualizações**, clique em **título** e altere o **texto** para **"veículos na operação por cidade"**.  
    ![Veículos na operação por cidade de carros - ligados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Visualização final procura conforme mostrado na figura.    
    ![Carros ligados - Final visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Clique na área em branco para adicionar a nova visualização.  

Selecione **Cidade** e **vin**, altere o tipo de visualização para **gráfico de colunas agrupadas**. Certifique-se **Cidade** campo no **área eixo** e **vin** no **valor área**  

Gráfico de ordenar por **"Contagem de vin"**  
    ![Carros ligados - contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Gráfico de alteração **título** para **"Veículos na operação por cidade"**  

Clique no **formato** secção, em seguida, selecione **dados cores**, clique em de **"Em"** para **Mostrar tudo**  
    ![Ligado carros - Mostrar todas as cores de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Altere a cor da cidade individuais clicando no ícone de cor.  
    ![Ligado carros - cores de alteração](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione **gráfico de colunas agrupadas** visualização de visualizações, arraste **Cidade** campo no **eixo** área, **modelo** no **Legenda** área e **vin** no **valor** área.  
    ![Carros ligados - gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Carros ligados - composição](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

Reorganizar visualização de todos os nesta página, conforme mostrado na figura.  
    ![Carros ligados - visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

A configuração com êxito o relatório em tempo real "Veículos na operação". Para poder continuar para criar o relatório seguinte em tempo real ou parar aqui e configurar o dashboard. 

### <a name="2-vehicles-requiring-maintenance"></a>2. Veículos necessidade de manutenção
Clique em ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório, mude o nome para **"Veículos necessidade de manutenção"**

![Carros ligados - veículos necessidade de manutenção](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Selecione **vin** campo e altere o tipo de visualização para **cartão**.  
    ![Carros ligados - Vin cartão visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Temos um campo com o nome "MaintenanceLabel" no conjunto de dados. Este campo pode ter um valor de "0" ou "1"." Está definido pelo modelo do Azure Machine Learning aprovisionado como parte da solução e integrados com o caminho em tempo real. O valor "1" indica que um vehicle exige manutenção. 

Para adicionar um **do nível de página** filtro para mostrar dados de veículos, que são necessidade de manutenção: 

1. Arraste o **"MaintenanceLabel"** campo para **filtros de nível de página**.  
   ![Carros ligados - filtros de nível de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  
2. Clique em **filtragem básicas** presente na parte inferior do filtro de nível de página MaintenanceLabel do menu.  
   ![Ligado carros - filtragem básicas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  
3. Defina o respetivo valor de filtro **"1"**    
   ![Carros ligados - valor do filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione **gráfico de colunas agrupadas** de visualizações  
![Carros ligados - Vind cartão visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Carros ligados - gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Campo de arrastar **modelo** para **eixo** área, **Vin** para **valor** área. Em seguida, ordene visualização por **contagem de vin**.  Gráfico de alteração **título** para **"Veículos necessidade de manutenção pelo modelo"**  

Arraste **vin** campos para **saturação de cor** presente no **campos** ![campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) secção **visualização**separador  
![Carros ligados - saturação de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Alteração **dados cores** em visualizações de **formato** secção  
Alterar a cor mínima para: **F2C812**  
Alterar a cor máxima para: **FF6300**  
![Ligado carros - alterações de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Ligado carros - novo as cores de visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione **gráfico de colunas em cluster** de visualizações, arraste **vin** campo para **valor** área, arraste **Cidade** campo para **Eixo** área. Gráfico de ordenar por **"Contagem de vin"**. Gráfico de alteração **título** para **"Veículos necessidade de manutenção por cidade"**   
![Carros ligados - veículos necessidade de manutenção por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Clique na área em branco para adicionar a nova visualização.  

Selecione **linha multi cartão** visualização de visualizações, arraste **modelo** e **vin** para o **campos** área.  
![Carros ligados - linha multi cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

O relatório final reorganização todas a visualização, procura da seguinte forma:  
![Carros ligados - linha multi cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

A configuração com êxito o relatório em tempo real "Veículos necessidade de manutenção". Para poder continuar para criar o relatório seguinte em tempo real ou parar aqui e configurar o dashboard. 

### <a name="3-vehicles-health-statistics"></a>3. Estatísticas de estado de funcionamento de veículos
Clique em ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar o novo relatório, mude o nome para **"Veículos estatísticas de estado de funcionamento"**  

Selecione **medidor** visualização de visualizações, em seguida, arraste o **velocidade** campo para **valor, valor mínimo, o valor máximo** áreas.  
![Carros ligados - linha multi cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Alterar a agregação predefinida de **velocidade** no **valor área** para **médio** 

Alterar a agregação predefinida de **velocidade** no **área mínimo** para **mínimo**

Alterar a agregação predefinida de **velocidade** no **área máximo** para **máximo**

![Carros ligados - linha multi cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Mudar o nome de **medidor título** para **"Velocidade média"** 

![Carros ligados - medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Clique na área em branco para adicionar a nova visualização.  

Da mesma forma adicionar um **medidor** para **médio petróleo motor**, **médio fuel**, e **motor médio temperatura**.  

Alterar a agregação predefinida de campos em cada medidor como por acima passos **"Velocidade média"** do medidor.

![Carros ligados - medidores](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **linha e gráfico de colunas agrupadas** de visualizações, em seguida, arraste **Cidade** campo para **partilhado eixo**, arraste **velocidade**, **campos tirepressure e engineoil** para **valores da coluna** área, alterar o respetivo tipo de agregação para **médio**. 

Arraste o **engineTemperature** campo para **valores de linha** área, alterar o tipo de agregação para **médio**. 

![Carros ligados - visualizações campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Altere o gráfico **título** para **"Médio velocidade, tire pressão, petróleo motor e a temperatura de motor"**.  

![Carros ligados - visualizações campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **Treemap** visualização de visualizações, arraste o **modelo** campo para a **grupo** área e arraste o campo **MaintenanceProbability** para o **valores** área.

Altere o gráfico **título** para **"Necessidade de manutenção de modelos Vehicle"**.

![Ligado carros - título do gráfico de alteração](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **100% empilhadas barra gráfico** de visualização, arraste o **Cidade** campo para a **eixo** área e arraste o **MaintenanceProbability**, **RecallProbability** campos para o **valor** área.

![Ligado carros - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Clique em **formato**, selecione **dados cores**e defina o **MaintenanceProbability** cor para o valor **"F2C80F"**.

Alterar o **título** do gráfico para **"Probabilidade de Vehicle manutenção e recuperar por cidade"**.

![Ligado carros - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Clique na área em branco para adicionar a nova visualização.

Selecione **gráfico de área** de visualização de visualizações, arraste o **modelo** campo para a **eixo** área e arraste o **engineOil, tirepressure, velocidade e MaintenanceProbability** campos para o **valores** área. Alterar o respetivo tipo de agregação para **"Médio"**. 

![Ligado carros - alterar tipo de agregação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Alterar o título do gráfico para **"Médio petróleo motor, tire probabilidade pressão, velocidade e manutenção pelo modelo"**.

![Ligado carros - título do gráfico de alteração](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Clique na área em branco para adicionar a nova visualização:

1. Selecione **gráfico de dispersão** visualização de visualizações.
2. Arraste o **modelo** campo para a **detalhes** e **legenda** área.
3. Arraste o **fuel** campo para a **eixo x** área, altere a agregação para **médio**.
4. Arraste **engineTemparature** para **área do eixo y**, altere a agregação para **médio**
5. Arraste o **vin** campo para a **tamanho** área.

![Ligado carros - Adicionar nova visualização](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Altere o gráfico **título** para **"Médias de Fuel, temperatura motor pelo modelo"**.

![Ligado carros - título do gráfico de alteração](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

O relatório final terá um aspeto semelhante conforme mostrado abaixo.

![Ligado relatório automóveis Final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Visualizações de PIN dos relatórios para o dashboard em tempo real
Crie um dashboard em branco ao clicar no ícone de adição junto aos Dashboards. Pode atribuir o nome "Dashboard de análise de telemetria Vehicle"

![Dashboard de carros ligado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Afixe a visualização dos relatórios acima para o dashboard. 

![Dashboard de carros ligado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

O dashboard deve ter o seguinte aspeto quando os três relatórios são criados e visualizações correspondentes são afixadas ao dashboard. Se não tiver criado a todos os relatórios, o dashboard foi parecer diferente. 

![Dashboard de carros ligado](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Parabéns! O dashboard em tempo real foi criada com êxito. Como continuar a executar CarEventGenerator.exe e RealtimeDashboardApp.exe, deverá ver as atualizações em direto no dashboard. Deve demorar cerca de 10 a 15 minutos para concluir os passos seguintes.

## <a name="setup-power-bi-batch-processing-dashboard"></a>Configurar o dashboard de processamento de lote do Power BI
> [!NOTE]
> Demora cerca de duas horas (da conclusão com êxito da implementação) para o pipeline de processamento em lote de ponto a ponto concluir a execução e processar um ano, mas de dados gerados. Por isso, aguarde que o processamento de concluir antes de continuar com os passos seguintes. 
> 
> 

**Transfira o ficheiro de estruturador do Power BI**

* Um ficheiro de estruturador do Power BI previamente configurado é incluído como parte da implementação Manual operação instruções
* Procure 2. Dashboard de processamento de lote de PowerBI de configuração pode transferir o modelo de PowerBI para o dashboard de processamento em lote aqui chamado **ConnectedCarsPbiReport.pbix**.
* Guardar localmente

**Configurar os relatórios do Power BI**

* Abra o ficheiro Estruturador '**ConnectedCarsPbiReport.pbix**' com o Power BI Desktop. Se já tiver, instale o Power BI Desktop de [instalação do Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331). 
* Clique em de **editar consultas**.

![Editar a consulta do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

* Faça duplo clique o **origem**.

![Origem do Power BI de conjunto](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

* Atualize a cadeia de ligação do servidor com o servidor SQL do Azure que obteve aprovisionado como parte da implementação.  Consulte as instruções de operação Manual em 

    4. Base de Dados SQL do Azure
    
    * Servidor: somethingsrv.database.windows.net
    * Base de dados: connectedcar
    * Nome de utilizador: nome de utilizador
    * Palavra-passe: Pode gerir a palavra-passe do SQL server no portal do Azure

* Deixe **base de dados** como *connectedcar*.

![Base de dados do conjunto de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

* Clique em **OK**.
* Verá **credencial do Windows** separador selecionado por predefinição, alterá-la para **as credenciais da base de dados** ao clicar no **base de dados** separador no lado direito.
* Forneça o **Username** e **palavra-passe** da SQL Database do Azure que foi especificada durante a configuração de implementação.

![Forneça as credenciais da base de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

* Clique em **ligar**
* Repita os passos acima para cada uma das três consultas restantes presentes no painel direito e, em seguida, atualize os detalhes de ligação da origem de dados.
* Clique em **fechar e carregar**. Conjuntos de dados do Power BI Desktop ficheiro estão ligados a tabelas de base de dados do SQL do Azure.
* **Fechar** ficheiro do Power BI Desktop.

![Fechar ambiente de trabalho do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

* Clique em **guardar** botão para guardar as alterações. 

Agora que configurou todos os relatórios correspondente para o caminho de processamento de lote na solução. 

## <a name="upload-to-powerbicom"></a>Carregar para *powerbi.com*
1. Navegue para o portal web do Power BI http://powerbi.com e início de sessão.
2. Clique em **obter dados**  
3. Carregue o ficheiro do Power BI Desktop.  
4. Para carregar, clique em **obter dados -> obter ficheiros -> Ficheiro Local**  
5. Navegue para o **"**ConnectedCarsPbiReport.pbix**"**  
6. Depois do ficheiro é carregado, será direcionado novamente para o seu espaço de trabalho do Power BI.  

Um conjunto de dados, relatórios e um dashboard em branco, serão criadas por si.  

Gráficos de PIN para um novo dashboard chamado **Dashboard de análise de telemetria Vehicle** no **Power BI**. Clique em dashboard em branco criado acima e, em seguida, navegue para o **relatórios** secção clique no relatório recentemente carregado.  

![Vehicle telemetria energia BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

**Tenha em atenção de que o relatório tem seis páginas:**  
Página 1: Densidade de Vehicle  
Página 2: o estado de funcionamento vehicle em tempo real  
Página 3: Forma agressiva orientadas por veículos   
Página 4: Resgatar os veículos  
Página 5: Fuel eficientemente orientadas por veículos  
6 de página: Logótipo de Contoso  

![Ligado carros BI.com de energia](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

**Na página 3**, afixar o seguinte:  

1. Contagem de VIN  
   ![Ligado carros BI.com de energia](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 
2. Forma agressiva orientadas por veículos pelo modelo – comprador de gráfico de cascata  
   ![Vehicle telemetria - gráficos de Pin 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Na página 5**, afixar o seguinte: 

1. Contagem de vin    
   ![Vehicle telemetria - gráficos de Pin 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2. Fuel veículos eficiente ao modelo: gráfico de colunas agrupadas  
   ![Vehicle telemetria - gráficos de Pin 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Na página 4**, afixar o seguinte:  

1. Contagem de vin  
   ![Vehicle telemetria - gráficos de Pin 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 
2. Resgatar os veículos por cidade, do modelo: Treemap  
   ![Vehicle telemetria - gráficos de Pin 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**Na página 6**, afixar o seguinte:  

1. Logótipo de Motors de contoso  
   ![Vehicle telemetria - gráficos de Pin 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organizar o dashboard**  

1. Navegue para o dashboard
2. Coloque o cursor sobre cada gráfico e renomeie-com base na nomenclatura fornecido na imagem do dashboard completas abaixo. Também mova os gráficos em torno de forma a parecerem dashboard abaixo.  
   ![Vehicle telemetria - organizar Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
   ![Vehicle telemetria energia BI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3. Se tiver criado a todos os relatórios tal como mencionado neste documento, o dashboard de conclusão final deverá ser semelhante a figura seguinte. 

![Vehicle telemetria - organizar Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Parabéns! Criou com êxito os relatórios e hábitos de dashboard para obter em tempo real, preditiva e conhecimentos aprofundados acerca do Estado de funcionamento vehicle e ocasionar do batch.  
