---
title: Monitorizar e gerir pipelines de dados - Azure | Microsoft Docs
description: "Saiba como utilizar a aplicação de monitorização e gestão para monitorizar e gerir as fábricas de dados do Azure e pipelines."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 65b5389837dc1d1693b1c4326b98264c8d75fd06
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorizar e gerir pipelines do Azure Data Factory utilizando a aplicação de monitorização e gestão
> [!div class="op_single_selector"]
> * [Utilizar o portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Utilizar aplicações de gestão e monitorização](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [monitorizar e gerir pipelines do Data Factory na versão 2](../monitor-visually.md).

Este artigo descreve como utilizar a aplicação de monitorização e gestão para monitorizar, gerir e depurar pipelines fábrica de dados. Também fornece informações sobre como criar alertas para ser notificado sobre falhas. Pode começar a utilizar com a utilização da aplicação por ver o vídeo seguinte:

> [!NOTE]
> A interface de utilizador apresentada no vídeo poderá corresponder não exatamente que vir no portal. É ligeiramente mais antigo, mas conceitos permanecerem o mesmo. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Iniciar a monitorização e a aplicação de gestão
Para iniciar a aplicação de monitorização e gestão, clique em de **Monitor & Gerir** mosaico a **Data Factory** painel para a fábrica de dados.

![Mosaico monitorização na home page do Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Deverá ver a aplicação de monitorização e gestão abrir numa janela separada.  

![Aplicação de Monitorização e Gestão](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se vir que o browser é bloqueado no "A autorizar …", desmarque a **bloquear cookies de terceiros e dados do site** caixa de verificação – ou mantenha ainda selecionada, crie uma exceção para **login.microsoftonline.com**e, em seguida, tente abrir novamente a aplicação.


Na lista de Windows de atividade no painel do meio, verá uma janela de atividade para cada execução de uma atividade. Por exemplo, se tiver a atividade agendada para ser executada de hora a hora de cinco horas, verá cinco windows de atividade associadas com cinco setores de dados. Se não vir windows atividade na lista na parte inferior, efetue o seguinte:
 
- Atualização do **hora de início** e **hora de fim** filtros na parte superior para fazer corresponder os tempos de início e de fim do pipeline e, em seguida, clique em de **aplicar** botão.  
- A lista de Windows de atividade não é atualizada automaticamente. Clique em de **atualizar** botão na barra de ferramentas no **atividade Windows** lista.  

Se não tiver uma aplicação do Data Factory para testar estes passos com, siga o tutorial: [copiar dados de armazenamento de BLOBs para base de dados SQL utilizando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Compreender a monitorização e a aplicação de gestão
Existem três separadores no lado esquerdo: **Explorador de recursos**, **vistas de monitorização**, e **alertas**. O primeiro separador (**Explorador de recursos**) está selecionado por predefinição.

### <a name="resource-explorer"></a>Explorador de Recursos
Verá o seguinte:

* O Explorador de recursos **vista de árvore** no painel esquerdo.
* O **vista de diagrama** na parte superior no painel do meio.
* O **atividade Windows** lista na parte inferior no painel do meio.
* O **propriedades**, **Explorador de janela de atividade**, e **Script** separadores no painel direito.

No Explorador de recursos, pode ver todos os recursos (pipelines, conjuntos de dados, serviços ligados) na fábrica de dados numa vista de árvore. Quando seleciona um objeto no Explorador de recursos:

* A entidade de fábrica de dados associada estiver realçada na vista de diagrama.
* [Associados windows atividade](data-factory-scheduling-and-execution.md) são realçados na lista de atividade Windows na parte inferior.  
* As propriedades do objeto selecionado são apresentadas na janela de propriedades no painel direito.
* A definição de JSON do objeto selecionado é apresentada, se aplicável. Por exemplo: um serviço ligado, um conjunto de dados ou um pipeline.

![Explorador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o [agendamento e execução](data-factory-scheduling-and-execution.md) artigo para obter informações detalhadas conceptuais sobre janelas de atividade.

### <a name="diagram-view"></a>Vista de Diagrama
A vista de diagrama de uma fábrica de dados disponibiliza um painel único de transparência para monitorizar e gerir uma fábrica de dados e os respetivos recursos. Quando seleciona uma entidade de fábrica de dados (conjunto de dados/pipeline) na vista de diagrama:

* A entidade de fábrica de dados está selecionada na vista de árvore.
* As janelas de atividade associada são realçadas na lista de Windows de atividade.
* As propriedades do objeto selecionado são apresentadas na janela Propriedades.

Quando o pipeline está ativado (não num Estado em pausa), é apresentado com uma linha verde:

![Pipeline em execução](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Pode interromper, retomar ou terminar um pipeline selecionando-a na vista de diagrama e utilizando os botões na barra de comandos.

![Colocar em pausa/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Existem três botões da barra de comando para o pipeline na vista de diagrama. Pode utilizar o botão segundo para colocar em pausa o pipeline. Colocar em pausa não terminar as atividades em execução e permite-lhes avançar para a conclusão. O terceiro botão interrompe o pipeline e termina a execução de atividades existentes. O botão da primeira retoma o pipeline. Quando o pipeline está em pausa, altera a cor do pipeline. Por exemplo, um pipeline em pausa aspeto na imagem seguinte: 

![Pipeline está em pausa](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Pode selecionar vários pipelines de dois ou mais utilizando a tecla Ctrl. Pode utilizar os botões da barra de comando para colocar em pausa/retomar pipelines vários cada vez.

Pode também um pipeline com o botão direito e selecione as opções para suspender, retomar ou terminar um pipeline. 

![Menu de contexto para o pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique em de **abrir pipeline** opção para ver todas as atividades no pipeline. 

![Menu Abrir pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na vista de pipeline foi aberto, pode ver todas as atividades no pipeline. Neste exemplo, é apenas uma atividade: atividade de cópia. 

![Pipeline aberta](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para voltar para a vista anterior, clique no nome da fábrica de dados no menu trilho na parte superior.

Na vista de pipeline, quando seleciona um conjunto de dados de saída ou ao mover o rato sobre o conjunto de dados de saída, consulte a janela de pop-up do Windows de atividade para esse conjunto de dados.

![Janela de pop-up de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Pode clicar numa janela de atividade para ver detalhes do mesmo no **propriedades** janela no painel direito.

![Propriedades da janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel da direita, mude para o **Explorador de janela de atividade** separador para ver mais detalhes.

![Explorador de janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Consulte também **resolver variáveis** para cada tentativa de execução de uma atividade no **tentativas** secção.

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mudar para o **Script** separador para ver a definição de script JSON para o objeto selecionado.   

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Pode ver windows da atividade em três locais:

* O pop-up do Windows da actividade na vista de diagrama (painel central).
* O Explorador de janela de atividade no painel direito.
* A lista de Windows de atividade no painel inferior.

No pop-up do Windows de atividade e no Explorador de janela de atividade, pode se deslocar para a semana anterior e a semana seguinte, utilizando as setas esquerdas e da direita.

![Setas de esquerda/direita do Explorador de janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da vista de diagrama, pode ver estes botões: Zoom no, Zoom saída Zoom para caberem, 100%, o esquema de bloqueio de Zoom. O **esquema de bloqueio** botão impede o acidentalmente tabelas e pipelines na vista de diagrama. Está ativada por predefinição. Pode desativá-la e mover entidades-a no diagrama. Quando desativar, pode utilizar o botão da última para posicione automaticamente tabelas e pipelines. Também pode ampliar ou reduzir utilizando a roda do rato.

![Comandos de zoom da vista de diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista de Windows de atividade
A lista de atividade Windows na parte inferior do painel do meio apresenta todas as janelas de atividade para o conjunto de dados que selecionou no Explorador de recursos ou a vista de diagrama. Por predefinição, a lista está por ordem, o que significa que vê a janela de atividade mais recente na parte superior descendente.

![Lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Esta lista não são atualizados automaticamente, por isso, utilize o botão de atualização na barra de ferramentas para atualizá-lo manualmente.  

Windows atividade podem ser dos seguintes Estados:

<table>
<tr>
    <th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
    <td rowspan="8">A aguardar</td><td>ScheduleTime</td><td>A hora não são fornecidos para a janela de atividade executar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências a montante não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas a executar outras janelas de atividade.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade está em pausa e não é possível executar os windows de atividade até que seja retomada.</td>
</tr>
<tr>
<td>Tente novamente</td><td>A execução da atividade está a ser repetida.</td>
</tr>
<tr>
<td>Validação</td><td>Validação ainda não começou.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Validação está a aguardar para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Em curso</td><td>A validação</td><td>Validação está em curso.</td>
</tr>
<td>-</td>
<td>A janela de atividade está a ser processada.</td>
</tr>
<tr>
<td rowspan="4">Falha</td><td>ServiceHost</td><td>A execução da atividade demorou mais do que as permitidas pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividade foi cancelada pelo ação do utilizador.</td>
</tr>
<tr>
<td>Validação</td><td>Falha na validação.</td>
</tr>
<tr>
<td>-</td><td>A janela de atividade falhou ser gerado ou validado.</td>
</tr>
<td>Pronto</td><td>-</td><td>A janela de atividade está pronta para consumo.</td>
</tr>
<tr>
<td>Foi ignorada</td><td>-</td><td>A janela de atividade não foi processada.</td>
</tr>
<tr>
<td>Nenhuma</td><td>-</td><td>Uma janela de atividade existia com um Estado diferente, mas foi reposta.</td>
</tr>
</table>


Ao clicar uma janela de atividade na lista, poderá ver detalhes sobre-lo no **Explorador do Windows de atividade** ou **propriedades** janela à direita.

![Explorador de janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar o windows de atividade
Os detalhes não são atualizados automaticamente, por isso, utilize o botão de atualização (botão segundo) na barra de comando para atualizar manualmente a lista de windows de atividade.  

### <a name="properties-window"></a>Janela de propriedades
A janela de propriedades é no painel de mais à direita da aplicação de monitorização e gestão.

![Janela de propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Apresenta as propriedades para o item selecionado no Explorador de recursos (vista de árvore), vista de diagrama ou lista de Windows de atividade.

### <a name="activity-window-explorer"></a>Explorador de janela de atividade
O **Explorador de janela de atividade** janela é no painel de mais à direita da aplicação de monitorização e gestão. Apresenta detalhes sobre a janela de atividade que selecionou na janela de pop-up de atividade Windows ou a lista de Windows de atividade.

![Explorador de janela de atividade](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Pode mudar para outra janela de atividade ao clicar no mesmo na vista de calendário, na parte superior. Também pode utilizar os botões de seta de seta para a esquerda/direito na parte superior para ver o windows de atividade da semana anterior ou próximas semanas.

Pode utilizar os botões da barra de ferramentas no painel inferior para executar novamente a janela de atividade ou atualizar os detalhes no painel.

### <a name="script"></a>Script
Pode utilizar o **Script** separador para ver a definição de JSON da entidade fábrica de dados selecionada (serviço ligado, conjunto de dados ou pipeline).

![Separador de script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Utilizar vistas de sistema
A aplicação de monitorização e gestão inclui vistas de sistema pré-criadas (**windows de atividade recente**, **falha windows atividade**, **windows actividade em curso**) que lhe permitem ver janelas de atividade recente/falhou/em curso para a fábrica de dados.

Mudar para o **vistas de monitorização** separador no lado esquerdo clicando nela.

![Separador de vistas de monitorização](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, existem três vistas de sistema que são suportadas. Selecione uma opção para ver o windows de atividade recente, actividade falhada windows ou windows da atividade em curso na lista de atividade Windows (na parte inferior do painel do meio).

Quando seleciona o **windows de atividade recente** opção, pode ver todas as janelas de atividade recente por ordem de descendente o **hora da última tentativa**.

Pode utilizar o **falha windows atividade** vista para ver todas as janelas de actividade falhada na lista. Selecione uma janela de actividade falhada na lista para ver detalhes acerca do mesmo no **propriedades** janela ou o **Explorador de janela de atividade**. Também pode transferir os registos para uma janela de actividade falhada.

## <a name="sort-and-filter-activity-windows"></a>Ordenar e filtrar windows de atividade
Alterar o **hora de início** e **hora de fim** definições na barra de comando para windows de atividade do filtro. Depois de alterar a hora de início e a hora de fim, clique no botão junto à hora de fim para atualizar a lista de Windows de atividade.

![Horas de início e de fim](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todas as horas são em formato UTC na aplicação de monitorização e gestão.
>
>

No **lista atividade Windows**, clique no nome de uma coluna (por exemplo: Estado).

![Menu de coluna de lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Pode efetuar o seguinte:

* Ordenar por ordem ascendente.
* Ordenar por ordem descendente.
* Filtrar por um ou mais valores (pronta, espera e assim sucessivamente).

Quando especificar um filtro numa coluna, é apresentada no botão Filtro ativado para essa coluna, o que indica que os valores na coluna são filtrados valores.

![Filtrar uma coluna de lista de Windows de atividade](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Pode utilizar a mesma janela de pop-up para limpar filtros. Para limpar todos os filtros para a lista de atividade Windows, clique no botão Filtro encriptado na barra de comandos.

![Limpar todos os filtros para a lista de Windows de atividade](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Executar ações de batch
### <a name="rerun-selected-activity-windows"></a>Volte a executar windows atividade selecionada
Selecione uma janela de atividade, clique na seta para baixo para o primeiro botão de barra de comando e selecione **Reexecução** / **voltar a executar com upstream no pipeline**. Quando seleciona o **voltar a executar com upstream no pipeline** opção, volta a executar, bem como todas as janelas de atividade a montante.
    ![Executar novamente uma janela de atividade](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também pode selecionar várias janelas de atividade na lista e volte a executar em simultâneo. Pode querer filtrar com base no estado de windows de atividade (por exemplo: **falha**) – e, em seguida, voltar a executar os windows de actividade falhada depois de corrigir o problema que faz com que as janelas de atividade falhe. Consulte a secção seguinte para obter detalhes sobre a filtragem do windows de atividade na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Colocar em pausa/retomar pipelines vários
Pode MultiSelect é pipelines de dois ou mais utilizando a tecla Ctrl. Pode utilizar os botões da barra de comando (que são realçados no retângulo vermelho na imagem seguinte) para colocar em pausa/retomá-los.

![Colocar em pausa/retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="create-alerts"></a>Criar alertas
O **alertas** página permite-lhe criar um alerta e alertas existentes de vista/edição/eliminação. Pode também desativar/ative um alerta. Para ver a página de alertas, clique o **alertas** separador.

![Separador alertas](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Para criar um alerta
1. Clique em **Adicionar alerta** para adicionar um alerta. Pode ver o **detalhes** página.

    ![Criar alertas - página de detalhes](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. Especifique o **nome** e **Descrição** para o alerta e clique em **seguinte**. Deverá ver o **filtros** página.

    ![Criar alertas - página de filtros](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Selecione o **eventos**, **estado**, e **subestado** (opcional) que pretende criar um alerta de serviço do Data Factory para e clique em **seguinte**. Deverá ver o **destinatários** página.

    ![Criar alertas - página destinatários](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png)
4. Selecione o **administradores da subscrição de E-Mail** opção e/ou introduza um **e-mail do administrador adicionais**e clique em **concluir**. Deverá ver o alerta na lista.

    ![Lista de alertas](./media/data-factory-monitor-manage-app/AlertsList.png)

Na lista de alertas, utilize os botões que estão associados com o alerta a edição/eliminação/desativar/ative um alerta.

### <a name="eventstatussubstatus"></a>Estado/evento/subestado
A tabela seguinte fornece a lista de eventos disponíveis e Estados (e substatuses).

| Nome do evento | Estado | Subestado |
| --- | --- | --- |
| Introdução de execução da atividade |Foi iniciada |A Iniciar |
| Terminar de execução da atividade |Bem-sucedido |Bem-sucedido |
| Terminar de execução da atividade |Falha |Alocação de recursos falhou<br/><br/>Falha de execução<br/><br/>Foi excedido<br/><br/>Validação falhada<br/><br/>Abandonado |
| Cluster HDI a pedido criar introdução |Foi iniciada |-|
| Cluster HDI a pedido criado com êxito |Bem-sucedido |-|
| Cluster HDI a pedido eliminado |Bem-sucedido |-|

### <a name="to-edit-delete-or-disable-an-alert"></a>Para editar, eliminar ou desativar um alerta

Utilize os botões seguintes (realçados a vermelho) para editar, eliminar ou desativar um alerta.

![Botões de alertas](./media/data-factory-monitor-manage-app/AlertButtons.png)
