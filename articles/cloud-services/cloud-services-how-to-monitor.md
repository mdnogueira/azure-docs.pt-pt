---
title: "Como monitorizar um serviço em nuvem | Microsoft Docs"
description: "Saiba como monitorizar serviços em nuvem utilizando o portal clássico do Azure."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 5c48d2fb-b8ea-420f-80df-7aebe2b66b1b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2015
ms.author: adegeo
ms.openlocfilehash: c369b22cf068a473343b006eb1b06fdd350d31db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-cloud-services"></a>Como Monitorizar os Serviços Cloud
[!INCLUDE [disclaimer](../../includes/disclaimer.md)]

Pode monitorizar `key` métricas de desempenho para os seus serviços em nuvem no portal clássico do Azure. Pode definir o nível de monitorização para mínima e verboso para cada função de serviço e pode personalizar a monitorização apresenta. Dados de monitorização verbosos são armazenados numa conta do storage, que pode aceder ao fora do portal. 

Monitorização apresenta no portal clássico do Azure é altamente configuráveis. Pode escolher as métricas que pretende monitorizar na lista de métricas no **Monitor** página e pode escolher quais as métricas para desenhar no métricas gráficos no **Monitor** página e o dashboard. 

## <a name="concepts"></a>Conceitos
Por predefinição, a monitorização mínima é fornecido para um novo serviço em nuvem através de contadores de desempenho recolhidos a partir do sistema operativo anfitrião para as instâncias de funções (máquinas virtuais). As métricas mínimas estão limitadas a percentagem de CPU, dados em, dados de saída, débito de leitura do disco e débito de escrita de disco. Ao configurar a monitorização verboso, pode receber métricas adicionais com base nos dados de desempenho nas máquinas virtuais (instâncias de função). As métricas verbosas ativar análise quanto mais próximo dos problemas que ocorrem durante as operações de aplicação.

Por predefinição, dados de contador de desempenho das instâncias de função é amostragem e transferidos da instância de função em intervalos de 3 minutos. Quando ativar a monitorização verboso, os dados de contador de desempenho em bruto são agregados em intervalos de 5 minutos, 1 hora e 12 horas para cada instância de função e entre instâncias de função para cada função. Os dados agregados são removidos depois de 10 dias.

Após ativar a monitorização verboso, os dados agregados de monitorização são armazenados em tabelas na sua conta do storage. Para ativar a monitorização verboso para uma função, tem de configurar uma cadeia de ligação de diagnóstico que liga à conta de armazenamento. Pode utilizar contas de armazenamento diferentes para diferentes funções.

Ativar verbosa aumenta monitorização os custos de armazenamento relacionado com armazenamento de dados, a transferência de dados e armazenamento transações. Monitorização mínimo não necessita de uma conta de armazenamento. Os dados para as métricas que são expostos ao nível da monitorização mínimo não são armazenados na sua conta de armazenamento, mesmo que defina o nível de monitorização para verboso.

## <a name="how-to-configure-monitoring-for-cloud-services"></a>Como: configurar a monitorização de serviços cloud
Utilize os procedimentos seguintes para configurar a monitorização de verboso ou mínima no portal clássico do Azure. 

### <a name="before-you-begin"></a>Antes de começar
* Criar um *clássico* conta do storage para armazenar os dados de monitorização. Pode utilizar contas de armazenamento diferentes para diferentes funções. Para obter mais informações, consulte [como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* Ative o diagnóstico do Azure para as funções do serviço de nuvem. Consulte [configurar diagnósticos para serviços em nuvem](cloud-services-dotnet-diagnostics.md).

Certifique-se de que a cadeia de ligação de diagnóstico está presente na configuração da função. Não é possível ativar a monitorização verboso até ativar o diagnóstico do Azure e incluir uma cadeia de ligação de diagnóstico na configuração da função.   

> [!NOTE]
> Projetos direcionada para o Azure SDK 2.5 não incluiu automaticamente a cadeia de ligação de diagnósticos no modelo de projeto. Para estes projetos, terá de adicionar manualmente a cadeia de ligação de diagnóstico para a configuração da função.
> 
> 

**Para adicionar manualmente a cadeia de ligação de diagnóstico para a configuração da função**

1. Abra o projeto de serviço em nuvem no Visual Studio
2. Faça duplo clique no **função** para abrir a função estruturador e selecione o **definições** separador
3. Procure uma definição denominada **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Se esta definição não estiver presente, clique em de **Adicionar definição** botão para adicioná-lo para a configuração e alterar o tipo para a nova definição **ConnectionString**
5. Defina o valor de cadeia de ligação ao clicar no **...**  botão. Esta ação abre uma caixa de diálogo que permite selecionar uma conta de armazenamento.
   
    ![Definições do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Para alterar o nível de monitorização para verboso ou mínima
1. No [portal clássico do Azure](https://manage.windowsazure.com/), abra o **configurar** página para a implementação do serviço em nuvem.
2. No **nível**, clique em **verboso** ou **mínima**. 
3. Clique em **Guardar**.

Depois de ativar a monitorização verboso, deve começar a ver os dados de monitorização no portal clássico do Azure dentro da hora.

Os dados do contador de desempenho em bruto e os dados agregados de monitorização são armazenados na conta de armazenamento nas tabelas qualificado pelo ID de implementação para as funções. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Como: receber alertas para as métricas do serviço de nuvem
Pode receber alertas com base nas suas métricas de monitorização do serviço de nuvem. No **dos serviços de gestão** página do Azure clássico portal, pode criar uma regra para acionar um alerta quando a métrica que escolher atinge um valor que especificar. Também pode optar por e-mail enviado quando o alerta é acionado. Para obter mais informações, consulte [como: receber notificações de alerta e gerir regras de alertas no Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Como: Adicionar métricas para a tabela de métricas
1. No [portal clássico do Azure](http://manage.windowsazure.com/), abra o **Monitor** página para o serviço em nuvem.
   
    Por predefinição, a tabela de métricas apresenta um subconjunto das métricas disponíveis. A ilustração mostra verboso nas métricas predefinidas de um serviço em nuvem, que tem um limite para o contador de desempenho Memória \ MBytes disponíveis, com os dados agregados ao nível da função. Utilize **adicionar métricas** para selecionar métricas adicionais de agregação e o nível de função para monitorizar no portal clássico do Azure.
   
    ![Apresentar verboso](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
2. Para adicionar métricas para a tabela de métricas:
   
   1. Clique em **adicionar métricas** para abrir **escolha métricas**, mostrado abaixo.
      
       A primeira métrica disponível é expandida para mostrar as opções disponíveis. Para cada métrica, a opção superior apresenta dados de monitorização agregados para todas as funções. Além disso, pode escolher funções individuais para apresentar dados.
      
       ![Adicione as métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)
   2. Para selecionar métricas para apresentar
      
      * Clique na seta para baixo pela métrica para expandir as opções de monitorização.
      * Selecione a caixa de verificação para cada opção de monitorização que pretende apresentar.
        
        Pode visualizar as métricas de até 50 na tabela de métricas.
        
        > [!TIP]
        > Na monitorização verboso, a lista de métricas pode conter dezenas de métricas. Para apresentar uma barra de deslocamento, coloque o cursor sobre o lado direito da caixa de diálogo. Para filtrar a lista, clique no ícone de pesquisa e introduza o texto na caixa de pesquisa, como mostrado abaixo.
        > 
        > 
        
        ![Adicionar a pesquisa de métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)
3. Depois de concluir a seleção de métricas, clique em OK (marca de verificação).
   
    As métricas selecionadas são adicionadas à tabela de métricas, conforme mostrado abaixo.
   
    ![métricas de monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)
4. Para eliminar uma métrica da tabela de métricas, clique na métrica selecioná-lo e, em seguida, clique em **eliminar métrica**. (Apenas vir **eliminar métrica** quando tiver uma métrica selecionada.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Para adicionar métricas personalizadas para a tabela de métricas
O **verboso** monitorização nível fornece uma lista de nas métricas predefinidas de monitorização no portal. Além destas pode monitorizar as métricas personalizadas ou os contadores de desempenho definidos pela sua aplicação através do portal.

Os seguintes passos assumem que tiver ativado **verboso** nível de monitorização e tiver configurado a sua aplicação para recolher e transferir os contadores de desempenho personalizados. 

Para apresentar os contadores de desempenho personalizados no portal tem de atualizar a configuração no contentor de controlo de wad:

1. Abra o wad-controlo de contentor do blob na sua conta de armazenamento de diagnóstico. Pode utilizar o Visual Studio ou quaisquer outro Explorador de armazenamento para efetuar este procedimento.
   
    ![Explorador de servidores do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)
2. Navegue até o caminho de BLOBs através do padrão de **RoleName/DeploymentId/RoleInstance** para encontrar a configuração para a instância de função. 
   
    ![Explorador de armazenamento do Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Transfira o ficheiro de configuração para a instância de função e atualizá-la para incluir os contadores de desempenho personalizados. Por exemplo, ao monitor *Bytes escritos do disco/seg* para o *unidade C* adicione o seguinte em **PerformanceCounters\Subscriptions** nós
   
    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Guardar as alterações e carregue o ficheiro de configuração novamente para a mesma localização substituir o ficheiro existente no blob.
5. Alternar para o modo verboso na configuração do portal clássico do Azure. Se foram já em modo verboso terá de alternar para mínima e a verboso.
6. O contador de desempenho personalizados agora estará disponível o **adicionar métricas** caixa de diálogo. 

## <a name="how-to-customize-the-metrics-chart"></a>Como: personalizar o gráfico de métricas
1. Na tabela de métricas, selecione métricas até 6 para desenhar no gráfico de métricas. Para selecionar uma métrica, clique na caixa de verificação no seu lado esquerdo. Para remover uma métrica do gráfico de métricas, desmarque a caixa de verificação na tabela de métricas.
   
    À medida que seleciona métricas na tabela de métricas, são adicionadas as métricas do gráfico de métricas. No ecrã restritas, uma **n mais** na lista pendente contém os cabeçalhos de métricos não cabem a apresentação.
2. Alternar entre apresentar valores relativos (valor final apenas para cada métrica) e os valores absolutos (é apresentado um eixo Y), selecione o caminho relativo ou absoluto na parte superior do gráfico.
   
    ![Relativo ou absoluto](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)
3. Para alterar o intervalo de tempo apresenta de gráfico métricas, selecione 1 hora, 24 horas ou sete dias na parte superior do gráfico.
   
    ![Período de visualização do monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)
   
    No gráfico de métricas de dashboard, o método para representar métricas é diferente. Um conjunto padrão de métricas está disponível e métricas são adicionadas ou removidas selecionando o cabeçalho da métrico.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Para personalizar o gráfico de métricas no dashboard
1. Abra o dashboard para o serviço em nuvem.
2. Adicionar ou remover as métricas do gráfico de:
   
   * Para representar uma métrica de novo, selecione a caixa de verificação para a métrica nos cabeçalhos do gráfico. No ecrã estreito, clique na seta para baixo por  ***n* ?? métricas** para desenhar uma métrica não é possível apresentar a área de cabeçalho do gráfico.
   * Para eliminar uma métrica de que é representada no gráfico, limpe a caixa de verificação, o cabeçalho.
   
3. Alternar entre **relativo** e **absoluto** apresenta.
4. Escolha 1 hora, 24 horas ou dados a apresentar de 7 dias.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Como: verboso dados fora do portal clássico do Azure de monitorização de acesso
Dados de monitorização verbosos são armazenados nas tabelas nas contas do storage que especificar para cada função. Para cada implementação de serviço em nuvem, são criadas seis tabelas para a função. Duas tabelas são criadas para cada (5 minutos, 1 hora e 12 horas). Um destas tabelas armazena agregações ao nível de função; outra tabela armazena agregações para instâncias de função. 

Os nomes de tabela com o seguinte formato:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

Em que:

* *deploymentID* é o GUID atribuído à implementação de serviço em nuvem
* *aggregation_interval* = 5 M, H de 1 ou 12 H
* agregações ao nível de função = R
* agregações para instâncias de função = RI

Por exemplo, as tabelas seguintes seriam armazenar dados de monitorização verbosos agregados em intervalos de 1 hora:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
