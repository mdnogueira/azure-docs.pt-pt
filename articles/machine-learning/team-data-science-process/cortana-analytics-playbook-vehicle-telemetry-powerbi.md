---
title: "Dashboard do Power BI para o estado de funcionamento vehicle e ocasionar hábitos - Azure | Microsoft Docs"
description: "Utilizar as capacidades do Cortana Intelligence para obter informações acerca de preditiva e em tempo real em estado de funcionamento vehicle e ocasionar hábitos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: aaeb29a5-4a13-4eab-bbf1-885690d86c56
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 626987ec0648f9e770499b4a48bc4ca2d175d2b4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-template-power-bi-dashboard-setup-instructions"></a>Instruções de configuração de dashboard do vehicle telemetria análise solução modelo Power BI
Liga menu-capítulos neste manual de comunicação social: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

A solução de análise de telemetria Vehicle showcases como dealerships carro, fabricantes automóveis e insurance empresas podem utilizar as capacidades do Cortana Intelligence. Terem insights preditivos e em tempo real no estado de funcionamento vehicle ocasionar hábitos para melhorar a experiência do cliente, a investigação e desenvolvimento e campanhas de marketing. Estas instruções passo a passo mostram como pode configurar os relatórios do Power BI e o dashboard depois de implementar a solução na sua subscrição. 

## <a name="prerequisites"></a>Pré-requisitos
* Implementar o [Vehicle telemetria análise](https://gallery.cortanaintelligence.com/Solution/5bdb23f3abb448268b7402ab8907cc90) solução. 
* [Instalar o ambiente de trabalho do Power BI](http://www.microsoft.com/download/details.aspx?id=45331).
* Obter um [subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/). Se não tiver uma subscrição do Azure, comece a subscrição gratuita do Azure.
* Abra uma conta do Power BI.

## <a name="cortana-intelligence-suite-components"></a>Componentes do Cortana Intelligence suite
Como parte do modelo de solução de análise de telemetria de Vehicle, os seguintes serviços do Cortana Intelligence são implementados na sua subscrição:

* **Os Hubs de eventos do Azure** ingere milhões de eventos de telemetria vehicle no Azure.
* **O Azure Stream Analytics** fornece informações em tempo real sobre o estado de funcionamento do vehicle e persistir os dados para armazenamento a longo prazo para a análise de lote mais rico.
* **O Azure Machine Learning** Deteta anomalias em tempo real e utiliza o processamento em lote para fornecer informações preditivos.
* **O Azure HDInsight** transforma dados à escala.
* **O Azure Data Factory** processa orchestration, agendamento, gestão de recursos e monitorizar o pipeline de processamento em lote.

**Power BI** fornece esta solução um dashboard avançado para dados em tempo real e visualizações de Análise Preditiva. 

A solução utiliza duas origens de dados diferentes:

* Vehicle simulada sinais e conjuntos de dados de diagnóstico
* Catálogo de vehicle

Um simulador de telematics vehicle é incluído como parte desta solução. -Emite informações de diagnóstico e sinais que correspondem para o estado do vehicle e padrões despertar num determinado ponto no tempo. 

O catálogo de vehicle é um conjunto de dados de referência que mapeia VINs para modelos.

## <a name="power-bi-dashboard-preparation"></a>Preparação de dashboard do Power BI
### <a name="set-up-the-power-bi-real-time-dashboard"></a>Configurar o dashboard do Power BI em tempo real

#### <a name="start-the-real-time-dashboard-application"></a>Iniciar a aplicação de dashboard em tempo real
Uma vez concluída a implementação, siga as instruções de operação manual.

1. Transferir a aplicação de dashboard em tempo real RealtimeDashboardApp.zip e deszipe-lo.

2.  Na pasta deszipada, abra o ficheiro de configuração de aplicação RealtimeDashboardApp.exe.config. Substitua appSettings para os Event Hubs, Blob storage do Azure e ligações de serviço do Azure Machine Learning com os valores nas instruções operação manual. Guarde as alterações.

3. Execute a aplicação RealtimeDashboardApp.exe. Na janela de início de sessão, introduza as suas credenciais do Power BI válidos. 

   ![Power BI início de sessão janela](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
   
4. Selecione **aceitar**. A aplicação começa a ser executada.

   ![Permissões de dashboard do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)

5. Inicie sessão no Web site do Power BI e criar um dashboard em tempo real.

Agora está pronto para configurar o dashboard do Power BI.  

### <a name="configure-power-bi-reports"></a>Configurar os relatórios do Power BI
Os relatórios em tempo real e o dashboard demorar cerca de 30 para 45 minutos a concluir. 

1. Navegue para o [Power BI](http://powerbi.com) página Web e iniciar sessão.

    ![Power BI início de sessão na página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

2. É gerado um novo conjunto de dados no Power BI. Selecione o **ConnectedCarsRealtime** conjunto de dados.

    ![Conjunto de dados de ConnectedCarsRealtime](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

3. Para guardar o relatório em branco, prima Ctrl + S.

    ![Relatório em branco](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

4. Introduza o nome do relatório **Vehicle telemetria análise em tempo real - relatórios**.

    ![Nome do relatório](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Relatórios em tempo real
São três relatórios em tempo real nesta solução:

* Veículos na operação
* Veículos necessidade de manutenção
* Vehicle estatísticas de estado de funcionamento

Pode configurar três dos relatórios em tempo real ou pode parar após qualquer fase. Em seguida, pode avançar para a secção sobre como configurar relatórios de lote seguinte. Recomendamos que crie três todos os relatórios para visualizar as informações do caminho em tempo real da solução completas.  

### <a name="vehicles-in-operation-report"></a>Veículos no relatório de operação
1. Faça duplo clique em **página 1**e renomeie- **veículos na operação**.

    ![Veículos na operação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

2. No **campos** separador, selecione **vin**. No **visualizações** separador, selecione o **cartão** visualização.  

    O **cartão** visualização é criada, conforme mostrado na figura seguinte:

    ![Selecione vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

3. Selecione a área em branco para adicionar uma nova visualização.  

4. No **campos** separador, selecione **Cidade** e **vin**. No **visualizações** separador, selecione o **mapa** visualização. Arraste **vin** para o **valores** área. Arraste **Cidade** para o **legenda** área. 

    ![Visualização do cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)

5. No **visualizações** separador, selecione o **formato** secção. Selecione **título**e alterar **texto** para **veículos na operação por cidade**.

    ![Veículos na operação por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

    A visualização final aspeto semelhante ao seguinte exemplo:

    ![Visualização final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

6. Selecione a área em branco para adicionar uma nova visualização.  

7. No **campos** separador, selecione **Cidade** e **vin**. No **visualizações** separador, selecione o **gráfico de colunas agrupadas** visualização. Arraste **Cidade** para o **eixo** área. Arraste **vin** para o **valor** área.

8. Ordenar o gráfico por **contagem de vin**.

    ![Contagem de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

9. Altere o gráfico **título** para **veículos na operação por cidade**. 

10. Selecione o **formato** secção e, em seguida, selecione **dados cores**. Alteração **Mostrar tudo** para **no**.

    ![Cores de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

11. Altere a cor de uma cidade individual, selecionando o símbolo de cor.

    ![Alteração da cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

12. Selecione a área em branco para adicionar uma nova visualização.  

13. No **visualizações** separador, selecione o **gráfico de colunas agrupadas** visualização. No **campos** separador, arraste **Cidade** para o **eixo** área. Arraste **modelo** para o **legenda** área. Arraste **vin** para o **valor** área.

    ![Gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)

    O gráfico assemelha-se a imagem seguinte:

    ![Composição](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)

14. Reorganizar todas as visualizações para que a página procura semelhante ao seguinte exemplo:

    ![Dashboard com visualizações](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Se configurou com êxito o relatório em tempo real "Veículos na operação". Pode criar o relatório em tempo real seguinte ou pode parar aqui e configurar o dashboard. 

### <a name="vehicles-requiring-maintenance-report"></a>Relatório de manutenção que requerem veículos

1. Selecione ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório. Mude o nome **veículos necessidade de manutenção**.

    ![Veículos necessidade de manutenção](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

2. No **campos** separador, selecione **vin**. No **visualizações** separador, selecione o **cartão** visualização.

    ![Visualização Vin cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

    O conjunto de dados contém um campo com o nome **MaintenanceLabel**. Este campo pode ter um valor de "0" ou "1". O valor é definido pelo modelo do machine learning que está aprovisionado como parte da solução. Está integrado com o caminho em tempo real. O valor "1" indica que um vehicle exige manutenção. 

3. Para adicionar um **filtro de nível de página** para mostrar dados para os veículos que necessitam de manutenção: 

   a. Arraste o **MaintenanceLabel** campo para **filtros de nível de página**.
  
      ![Filtros de nível de página](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)

    b. Na parte inferior **MaintenanceLabel de filtros de nível de página**, selecione **filtragem básicas**.

      ![Filtragem básicas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png) 

    c. Defina o valor de filtro para **1**.

      ![Valor do filtro](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  

4. Selecione a área em branco para adicionar uma nova visualização.  

5. No **visualizações** separador, selecione o **gráfico de colunas agrupadas** visualização. 

    ![Vin cartão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)

    ![Gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

6. No **campos** separador, arraste **modelo** para o **eixo** área. Arraste **vin** para o **valor** área. Em seguida, ordene a visualização por **contagem de vin**. Altere o gráfico **título** para **veículos necessidade de manutenção por modelo**. 

7. No **campos** ![campos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) secção o **visualizações** separador, arraste **vin** para **saturação de cor**.

    ![Saturação de cor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

8. No **formato** secção, altere **dados cores** na visualização: 

    a. Alterar o **mínimo** cor a **F2C812**.

    b. Alterar o **máximo** cor a **FF6300**.

    ![Novo cores de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)

    As cores de visualização novo ter um aspeto semelhante ao seguinte exemplo:

    ![Cores de visualização novo](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

9. Selecione a área em branco para adicionar uma nova visualização.  

10. No **visualizações** separador, selecione **gráfico de colunas agrupadas**. Arraste **vin** para o **valor** área. Arraste **Cidade** para o **eixo** área. Ordenar o gráfico por **contagem de vin**. Altere o gráfico **título** para **veículos necessidade de manutenção por cidade**.

    ![Veículos necessidade de manutenção por cidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

11. Selecione a área em branco para adicionar uma nova visualização.  

12. No **visualizações** separador, selecione o **linha multi cartão** visualização. Arraste **modelo** e **vin** para o **campos** área.

    ![Cartão multi linha](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

13. Reorganizar todas as visualizações para que o relatório final aspeto semelhante ao seguinte exemplo: 

    ![Reorganizar final relatório](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Se configurou com êxito o relatório em tempo real "Veículos necessidade de manutenção". Pode criar o relatório em tempo real seguinte ou pode parar aqui e configurar o dashboard. 

### <a name="vehicle-health-statistics-report"></a>Relatório de estatísticas de estado de funcionamento de vehicle

1. Selecione ![adicionar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) para adicionar um novo relatório. Mude o nome **estatísticas de estado de funcionamento de veículos**. 

2. No **visualizações** separador, selecione o **medidor** visualização. Arraste **velocidade** para o **valor**, **valor mínimo**, e **valor máximo** áreas.

   ![Estatísticas de estado de funcionamento de veículos](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

3. No **valor** área, altere a agregação predefinida de **velocidade** para **médio**.

4. No **valor mínimo** área, altere a agregação predefinida de **velocidade** para **mínimo**.

5. No **valor máximo** área, altere a agregação predefinida de **velocidade** para **máximo**.

   ![Valores de velocidade](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

6. Mudar o nome de **medidor título** para **médio velocidade**.

   ![Medidor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

7. Selecione a área em branco para adicionar uma nova visualização.  

    Da mesma forma, adicione um **medidor** para **médio petróleo motor**, **médio fuel**, e **médio de temperatura de motor**.  

8. Alterar a agregação predefinida de campos em cada medidor como fez nos passos anteriores no **médio velocidade** do medidor.

    ![Medidores adicionais](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

9. Selecione a área em branco para adicionar uma nova visualização.

10. No **visualizações** separador, selecione o **linha e gráfico de colunas agrupadas** visualização. Arraste **Cidade** para **partilhado eixo**. Arraste **tirepressure**, **engineoil**, e **velocidade** para o **valores da coluna** área. Alterar o respetivo tipo de agregação para **médio**. 

11. Arraste **engineTemperature** para o **valores de linha** área. Alterar o tipo de agregação para **médio**. 

    ![Valores de linha e coluna](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

12. Altere o gráfico **título** para **médio velocidade, tire pressão, petróleo motor e a temperatura de motor**.  

    ![Título da linha e gráfico de colunas agrupadas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

13. Selecione a área em branco para adicionar uma nova visualização.

14. No **visualizações** separador, selecione o **Treemap** visualização. Arraste **modelo** para o **grupo** área. Arraste **MaintenanceProbability** para o **valores** área.

15. Altere o gráfico **título** para **modelos Vehicle necessidade de manutenção**.

    ![Título de Treemap](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

16. Selecione a área em branco para adicionar uma nova visualização.

17. No **visualizações** separador, selecione o **100% empilhadas barra gráfico** visualização. Arraste **Cidade** para o **eixo** área. Arraste **MaintenanceProbability** e **RecallProbability** para o **valor** área.

    ![Áreas de eixo e valor](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

18. No **formato** secção, selecione **dados cores**. Definir o **MaintenanceProbability** cor para o valor **F2C80F**.

19. Altere o gráfico **título** para **probabilidade de manutenção Vehicle & devolução de chamada por cidade**.

    ![Título do gráfico de barras empilhadas de 100%](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

20. Selecione a área em branco para adicionar uma nova visualização.

21. No **visualizações** separador, selecione o **gráfico de área** visualização. Arraste **modelo** para o **eixo** área. Arraste **engineOil**, **tirepressure**, **velocidade**, e **MaintenanceProbability** para o **valores** área. Alterar o respetivo tipo de agregação para **médio**. 

    ![Tipo de agregação](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

22. Altere o gráfico **título** para **médio petróleo motor, tire probabilidade pressão, velocidade e manutenção pelo modelo**.

    ![Título do gráfico de área](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

23. Selecione a área em branco para adicionar uma nova visualização.

24. No **visualizações** separador, selecione o **gráfico de dispersão** visualização. Arraste **modelo** para o **detalhes** e **legenda** áreas. Arraste **fuel** para o **eixo X** área. Alterar a agregação para **médio**. Arraste **engineTemperature** para o **eixo Y** área. Alterar a agregação para **médio**. Arraste **vin** para o **tamanho** área.

    ![Detalhes, áreas de legenda, eixo e tamanho](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

25. Altere o gráfico **título** para **médio de fuel, média de engineTemperature e contagem de vin pelo modelo e o modelo**.

    ![Título do gráfico de dispersão](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

    O relatório final aspeto semelhante ao seguinte exemplo:

    ![Relatório final](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Visualizações de PIN dos relatórios para o dashboard em tempo real
1. Criar um dashboard em branco, selecionando o símbolo de adição junto a **Dashboards**. Introduza o nome **Dashboard de análise de telemetria Vehicle**.

    ![Dashboard plus símbolo](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

2. Afixe visualizações dos relatórios anteriores ao dashboard. 

    ![Símbolo de pin de dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

    Quando todos os relatórios de três são afixados ao dashboard, deve ter um aspeto semelhante ao seguinte exemplo. Se não criar todos os relatórios, o dashboard poderá ter um aspeto diferente. 

    ![Dashboard com relatórios](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Criou com êxito o dashboard em tempo real. Como continuar a executar CarEventGenerator.exe e RealtimeDashboardApp.exe, verá as atualizações em direto no dashboard. Os seguintes passos demorar cerca de 10 a 15 minutos a concluir.

## <a name="set-up-the-power-bi-batch-processing-dashboard"></a>Configurar o dashboard de processamento de lote do Power BI
> [!NOTE]
> Demora cerca de duas horas (da conclusão com êxito da implementação) para o pipeline para concluir a execução e processar visão de um ano de dados gerados de processamento em lote ponto-a-ponto. Aguarde que o processamento ser concluído antes de continuar com os seguintes passos. 
> 
> 

### <a name="download-the-power-bi-designer-file"></a>Transfira o ficheiro de estruturador do Power BI

1. Um ficheiro de estruturador do Power BI pré-configurada é incluído como parte das instruções da operação manual de implementação. Procure "2. Configurar o dashboard de processamento de lote do PowerBI."

2. Transferir o modelo do Power BI para o dashboard de processamento em lote aqui chamado **ConnectedCarsPbiReport.pbix**.

3. Guarde-o localmente.

### <a name="configure-power-bi-reports"></a>Configurar os relatórios do Power BI

1. Abra o ficheiro estruturador **ConnectedCarsPbiReport.pbix** utilizando o Power BI Desktop. Se que ainda não o tiver, instale o Power BI Desktop do [instalação do Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331) Web site.

2. Selecione **editar consultas**.

    ![Editar consultas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

3. Faça duplo clique em **origem**.

    ![Origem](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

4. Atualize a cadeia de ligação do servidor com o servidor de SQL do Azure que obteve aprovisionado como parte da implementação. Consulte as instruções de operação manual em SQL database do Azure:

    * Servidor: somethingsrv.database.windows.net
    * Base de dados: connectedcar
    * Nome de utilizador: nome de utilizador
    * Palavra-passe: Pode gerir a palavra-passe do SQL Server a partir do portal do Azure.

5. Deixe **base de dados** como **connectedcar**.

    ![Base de Dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

6. Selecione **OK**.

7. O **credencial do Windows** separador está selecionado por predefinição. Altere-o para **as credenciais da base de dados** selecionando o **base de dados** separador no lado direito.

8. Introduza o **Username** e **palavra-passe** da base de dados SQL do Azure que foi especificada durante a configuração de implementação.

    ![Credenciais da base de dados](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

9. Selecione **Ligar**.

10. Repita os passos anteriores para cada uma das três consultas restantes presentes no painel direito. Em seguida, atualize os detalhes de ligação da origem de dados.

11. Selecione **fechar e carregar**. Conjuntos de dados de ficheiro do Power BI Desktop estão ligados a tabelas de base de dados do SQL Server.

12. Selecione **fechar** para fechar o ficheiro do Power BI Desktop.

    ![Fechar](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

13. Selecione **guardar** para guardar as alterações. 

Agora que configurou os relatórios que correspondem para o caminho na solução de processamento em lote. 

## <a name="upload-to-powerbicom"></a>Carregar para o powerbi.com
1. Vá para o [portal web do Power BI](http://powerbi.com)e iniciar sessão.

2. Selecione **obter dados**.

3. Carregue o ficheiro do Power BI Desktop. Selecione **obter dados** > **obter ficheiros** > **ficheiro Local**.

4. Aceda a **ConnectedCarsPbiReport.pbix**.

5. Depois do ficheiro é carregado, volte atrás para o seu espaço de trabalho do Power BI. Um conjunto de dados, um relatório e um dashboard em branco são criados por si.  

6. Gráficos de PIN para um novo dashboard chamado **Dashboard de análise de telemetria Vehicle** no Power BI. Selecione o dashboard em branco que foi criado anteriormente e, em seguida, avance para o **relatórios** secção. Selecione o relatório recentemente carregado.  

    ![Novo dashboard do Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 

    O relatório tem seis páginas:

    Página 1: Densidade de Vehicle  
    Página 2: o estado de funcionamento vehicle em tempo real  
    Página 3: Forma agressiva orientadas por veículos   
    Página 4: Resgatar os veículos  
    Página 5: Fuel eficientemente orientadas por veículos  
    6 de página: Logótipo de Contoso Motors  

    ![Relatório do Power BI com seis páginas](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)

7. De **página 3**, afixar o seguinte conteúdo:  

    a. **Contagem de vin**  

   ![Contagem de 3 páginas de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png)

    b. **Forma agressiva orientadas por veículos pelo modelo – comprador de gráfico de cascata** 

   ![Gráfico de página 3 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

8. De **página 5**, afixar o seguinte conteúdo: 

    a. **Contagem de vin**

   ![Gráfico de página 5 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)

    b. **Veículos Fuel-Efficient pelo modelo: gráfico de colunas agrupadas**

   ![Gráfico de página 5 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

9. De **página 4**, afixar o seguinte conteúdo:  

    a. **Contagem de vin** 

   ![Gráfico de página 4 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

    b. **Resgatar os veículos por cidade, do modelo: Treemap**

   ![Gráfico de página 4 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

10. De **página 6**, afixar o seguinte conteúdo:  

    * **Logótipo de Motors de contoso**

    ![Logótipo de Motors de contoso](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

### <a name="organize-the-dashboard"></a>Organizar o dashboard  

1. Aceda ao dashboard.

2. Coloque o cursor sobre cada gráfico. Mudar o nome de cada gráfico com base na nomenclatura fornecido no exemplo seguinte terminar dashboard:

   ![Organização do dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png) 
   
3. Mova os gráficos cerca para ter um aspeto semelhante ao seguinte exemplo de dashboard:

    ![Reorganizar dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

4. Depois de criar todos os relatórios mencionados neste documento, o dashboard de conclusão final aspeto semelhante ao seguinte exemplo: 

   ![Final dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Criou com êxito os relatórios e o dashboard para obter em tempo real, preditiva e insights batch no estado de funcionamento vehicle e ocasionar hábitos.  
