---
title: Monitorizar, diagnosticar e resolver problemas de armazenamento do Azure | Microsoft Docs
description: "Utilize funcionalidades como a análise de armazenamento, o registo do lado do cliente e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas relacionados com o Storage do Azure."
services: storage
documentationcenter: 
author: fhryo-msft
manager: jahogg
editor: tysonn
ms.assetid: d1e87d98-c763-4caa-ba20-2cf85f853303
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.openlocfilehash: 1a9c9354b665294778886441cc6d7f02adb1163f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorizar, diagnosticar e resolver problemas do Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Descrição geral
Diagnosticar e resolver problemas de uma aplicação distribuída alojada num ambiente de nuvem podem ser mais complexos do que em ambientes tradicionais. As aplicações podem ser implementadas numa infraestrutura PaaS ou IaaS, no local, num dispositivo móvel, ou numa combinação destas. Normalmente, o tráfego de rede da sua aplicação pode atravessar redes públicas e privadas e a aplicação pode utilizar várias tecnologias de armazenamento, como tabelas de armazenamento do Microsoft Azure, Blobs, filas ou ficheiros, além de outros dados armazena, tais como relacional e documentar as bases de dados.

Para gerir com êxito essas aplicações deve monitorizá-los de forma pró-ativa e compreender melhor como diagnosticar e resolver problemas de todos os aspetos da-los e as tecnologias dependentes. Como um utilizador dos serviços de armazenamento do Azure, deve continuamente monitorizar os serviços de armazenamento, que a aplicação utiliza para efetuar quaisquer alterações inesperadas no comportamento (por exemplo, mais lenta do que os tempos de resposta habitual) e utilizar o registo para recolher dados mais detalhados e analisar um problema em profundidade. As informações de diagnóstico que obter de monitorização e registo irão ajudá-lo a determinar a causa de raiz do problema encontrou a sua aplicação. Em seguida, pode resolver o problema e determinar os passos adequados que pode tomar para resolvê-lo. Armazenamento do Azure é um núcleo de serviço do Azure e constitui uma parte importante da maioria das soluções que os clientes implementar a infraestrutura do Azure. Armazenamento do Azure inclui capacidades para simplificar a monitorização, diagnosticar e resolver problemas de armazenamento nas suas aplicações baseado na nuvem.

> [!NOTE]
> Os ficheiros do Azure não suporta o registo neste momento.
> 

Para obter um guia prática ponto-a-ponto de resolução de problemas em aplicações de armazenamento do Azure, consulte [ponto-a-ponto resolução de problemas com as métricas do Storage do Azure e o registo, o AzCopy e o Message Analyzer](../storage-e2e-troubleshooting.md).

* [Introdução]
  * [Como este guia está organizado]
* [monitorização do seu serviço de armazenamento]
  * [Monitorização de estado de funcionamento do serviço]
  * [Capacidade de monitorização]
  * [Monitorização de disponibilidade]
  * [Monitorização do desempenho]
* [diagnosticar problemas de armazenamento]
  * [Problemas de estado de funcionamento de serviço]
  * [Problemas de desempenho]
  * [Erros de diagnóstico]
  * [Problemas de emulador de armazenamento]
  * [Ferramentas de registo de armazenamento]
  * [Utilizar ferramentas de registo de rede]
* [rastreio ponto-a-ponto]
  * [Correlacionar dados de registo]
  * [ID do pedido de cliente]
  * [ID do pedido de servidor]
  * [Carimbos]
* [orientações de resolução de problemas]
  * [métricas mostram AverageE2ELatency alta e baixa AverageServerLatency]
  * [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
  * [As métricas apresentam uma AverageServerLatency alta]
  * [Ocorrem atrasos inesperados na entrega de mensagens numa fila]
  * [métricas mostram um aumento no PercentThrottlingError]
  * [métricas mostram um aumento no PercentTimeoutError]
  * [As métricas apresentam um aumento do PercentNetworkError]
  * [O cliente está a receber mensagens HTTP 403 (proibido)]
  * [O cliente está a receber mensagens HTTP 404 (não for encontrado)]
  * [O cliente está a receber mensagens de HTTP 409 (conflito)]
  * [métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]
  * [Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]
  * [Estão a experienciar inesperados reinícios das máquinas virtuais que tenham um grande número de VHDs ligados]
  * [O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]
  * [Pode encontrar problemas ao instalar o Azure SDK para .NET]
  * [Tem um problema com um serviço de armazenamento diferente]
  * [Resolução de problemas de ficheiros do Azure com o Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Resolução de problemas de ficheiros do Azure com o Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Appendices]
  * [apêndice 1: utilizar o Fiddler para capturar o tráfego HTTP e HTTPS]
  * [apêndice 2: com o Wireshark para capturar o tráfego de rede]
  * [apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede]
  * [Apêndice 4: Com o Excel para ver métricas e registo de dados]
  * [Apêndice 5: Monitorizar com o Application Insights para Visual Studio Team Services]

## <a name="introduction"></a>Introdução
Este guia mostra como utilizar funcionalidades como a análise de armazenamento do Azure, do lado do cliente registo na biblioteca de cliente do Storage do Azure e outras ferramentas de terceiros para identificar, diagnosticar e resolver problemas de armazenamento do Azure problemas relacionados com.

![][1]

Este guia destina-se a ser lido principalmente pelos programadores dos serviços online que utilizam serviços de armazenamento do Azure e os profissionais de TI responsáveis pela gestão desses serviços online. Os objetivos deste guia são:

* Para o ajudar a manter o estado de funcionamento e o desempenho das suas contas do Storage do Azure.
* Para lhe fornecer os processos necessários e ferramentas para ajudar a decidir se um problema numa aplicação ou um problema relacionado com armazenamento do Azure.
* Para lhe fornecer orientações acionável para resolver problemas relacionados com o Storage do Azure.

### <a name="how-this-guide-is-organized"></a>Como este guia está organizado
A secção "[monitorização do seu serviço de armazenamento]" descreve como monitorizar o estado de funcionamento e desempenho dos seus serviços de armazenamento do Azure através de métricas de análise de armazenamento do Azure (as métricas do Storage).

A secção "[diagnosticar problemas de armazenamento]" descreve como diagnosticar problemas com o registo do Azure Storage Analytics (registo de armazenamento). Também descreve como ativar o registo do lado do cliente utilizando as instalações de uma das bibliotecas de cliente, tais como a biblioteca de clientes de armazenamento para .NET ou o Azure SDK para Java.

A secção "[rastreio ponto-a-ponto]" descreve como pode correlacionar as informações contidas nos vários ficheiros de registo e dados de métricas.

A secção "[orientações de resolução de problemas]" fornece orientações de resolução de problemas para algumas das comuns relacionadas com o armazenamento problemas poderá encontrar.

O "[Appendices]" incluem informações sobre como utilizar outras ferramentas, como o Wireshark e Netmon, para analisar dados de pacote, o Fiddler para analisar mensagens HTTP/HTTPS, de rede e o Microsoft Message Analyzer para correlacionar dados de registo.

## <a name="monitoring-your-storage-service"></a>O serviço de armazenamento de monitorização
Se estiver familiarizado com a monitorização de desempenho do Windows, pode considerar as métricas do Storage como sendo equivalente Storage do Azure de contadores de Monitor de desempenho do Windows. As métricas do Storage, encontrará um conjunto abrangente de métricas (contadores na terminologia de Monitor de desempenho do Windows), tais como a disponibilidade do serviço, número total de pedidos de serviço ou a percentagem de pedidos com êxito ao serviço. Para obter uma lista completa das métricas disponíveis, consulte [armazenamento esquema da tabela de métricas de análise](http://msdn.microsoft.com/library/azure/hh343264.aspx). Pode especificar se pretende que o serviço de armazenamento para recolher e agregar métricas a cada hora ou a cada minuto. Para obter mais informações sobre como ativar as métricas e monitorizar as contas do storage, consulte [ativar as métricas do storage e visualizar dados de métricas](http://go.microsoft.com/fwlink/?LinkId=510865).

Pode escolher as métricas de hora a hora que pretende apresentar no [portal do Azure](https://portal.azure.com) e configurar regras de notificar os administradores por e-mail sempre que uma métrica de hora a hora excede um limiar específico. Para obter mais informações, consulte [receber notificações de alerta](/azure/monitoring-and-diagnostics/monitoring-overview-alerts.md). 

O serviço de armazenamento recolhe métricas de utilizar um melhor esforço, mas não pode registar cada operação de armazenamento.

No portal do Azure, pode ver as métricas como disponibilidade, total de pedidos e números de latência média de uma conta de armazenamento. Uma regra de notificação também foi definida para alertar o administrador se disponibilidade descerem abaixo de um determinado nível. De ver estes dados, uma área possíveis para investigação é a percentagem de êxito de serviço de tabela a ser inferior a 100% (para obter mais informações, consulte a secção "[métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]").

Deverá monitorizar continuamente as suas aplicações do Azure para garantir que estão em bom estado e execução conforme esperado pelo:

* Estabelecer algumas métricas de linha de base para a aplicação que irá permitir-lhe comparar dados atuais e identificar quaisquer alterações significativas no comportamento do armazenamento do Azure e a sua aplicação. Em muitos casos, os valores das métricas de linha de base estará específica de aplicação e deve estabelecer quando estiver a testar a aplicação de desempenho.
* Gravar métricas minutos e utilizá-los para monitorizar ativamente erros inesperados e anomalias como picos de erro contagem ou taxas de pedidos.
* Gravar as métricas de hora a hora e utilizá-los para monitorizar os valores médios como tempo médio de contagens de erro e taxas de pedidos.
* Investigar potenciais problemas de utilizar ferramentas de diagnóstico, tal como explicado posteriormente na secção "[diagnosticar problemas de armazenamento]."

Os gráficos na imagem seguinte ilustram como a média que ocorre a cada hora com base nas métricas pode ocultar picos na atividade. As métricas de hora a hora apresentada mostrar uma taxa de pedidos, gradual durante o minuto métricas revelar flutuações que realmente estão a decorrer.

![][3]

O resto esta secção descreve as métricas, deve monitorizar e por que motivo.

### <a name="monitoring-service-health"></a>Monitorização de estado de funcionamento do serviço
Pode utilizar o [portal do Azure](https://portal.azure.com) para ver o estado de funcionamento do serviço de armazenamento (e outros serviços do Azure) em todas as regiões do Azure em todo o mundo. Isto permite-lhe ver imediatamente se um problema fora do controlo está a afetar o serviço de armazenamento na região que utilizar para a sua aplicação.

O [portal do Azure](https://portal.azure.com) também pode fornecer notificações de incidentes que afetam os vários serviços do Azure.
Nota: Estas informações estavam anteriormente disponíveis, along com os dados históricos sobre o [Dashboard do serviço Azure](http://status.azure.com).

Enquanto o [portal do Azure](https://portal.azure.com) recolhe informações de estado de funcionamento de dentro de centros de dados do Azure (monitorização do interior-out), pode também considerar adotar uma abordagem de exterior para gerar transações sintéticas que periodicamente aceder a aplicação web alojado no Azure de várias localizações. Os serviços oferecidos por [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) e o Application Insights para Visual Studio Team Services são exemplos desta abordagem no exterior. Para mais informações sobre o Application Insights para Visual Studio Team Services, consulte o apêndice "[apêndice 5: monitorizar com o Application Insights para Visual Studio Team Services](#appendix-5)."

### <a name="monitoring-capacity"></a>Capacidade de monitorização
As métricas do Storage apenas armazena as métricas de capacidade para o serviço blob porque blobs normalmente a conta para a maior proporção dos dados armazenados (no momento da escrita, não é possível utilizar as métricas do Storage para monitorizar a capacidade do seu tabelas e filas). Pode encontrar estes dados no **$MetricsCapacityBlob** tabela se tiver ativado a monitorização para o serviço Blob. As métricas do Storage regista estes dados uma vez por dia, e pode utilizar o valor da **RowKey** para determinar se a linha contém uma entidade que está relacionada com a dados de utilizador (valor **dados**) ou dados de análise (valor **análise**). Cada entidade armazenada contém informações sobre a quantidade de armazenamento utilizado (**capacidade** medido em bytes) e o número atual de contentores (**ContainerCount**) e os blobs (**ObjectCount**) em utilização na conta de armazenamento. Para obter mais informações sobre as métricas de capacidade armazenadas no **$MetricsCapacityBlob** tabela, consulte [armazenamento esquema da tabela de métricas de análise](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Deverá monitorizar estes valores para um aviso inicial que está a atingir os limites de capacidade da sua conta do storage. No portal do Azure, pode adicionar regras de alertas para notificá-lo se a utilização do armazenamento agregado excede ou fica abaixo limiares que especificar.
> 
> 

Para obter ajuda a estimar o tamanho de vários objetos de armazenamento, tais como blobs, consulte a mensagem de blogue [compreender Azure armazenamento faturação – largura de banda, as transações e a capacidade](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Monitorização de disponibilidade
Deverá monitorizar a disponibilidade dos serviços de armazenamento na sua conta de armazenamento através da monitorização o valor de **disponibilidade** colunas nas tabelas de métricas de hora a hora ou minutos — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. O **disponibilidade** coluna contém um valor de percentagem que indica a disponibilidade de serviço ou a operação de API representado por linha (o **RowKey** mostra se a linha contém as métricas para o serviço como um todo, ou para uma operação de API específica).

Qualquer valor inferior a 100% indica que alguns pedidos de armazenamento estão a falhar. Pode ver por que motivo falhar, examinando as outras colunas nos dados de métricas que mostram como os números de pedidos com tipos de erro diferente de **ServerTimeoutError**. Deverá ver **disponibilidade** enquadram-se temporariamente inferior a 100% por motivos como tempos limite de servidor transitório enquanto o serviço move partições para melhor pedido de balanceamento de carga; a lógica de repetição na sua aplicação cliente deve processar estas condições intermitentes. O artigo [operações de registadas análise de armazenamento e as mensagens de estado](http://msdn.microsoft.com/library/azure/hh343260.aspx) lista os tipos de transação que inclui as métricas do Storage no respetivo **disponibilidade** cálculo.

No [portal do Azure](https://portal.azure.com), pode adicionar regras de alertas para notificá-lo se **disponibilidade** para um serviço está abaixo de um limiar que especificar.

O "[orientações de resolução de problemas]" secção deste guia descreve alguns problemas comuns do serviço de armazenamento relacionadas com a disponibilidade.

### <a name="monitoring-performance"></a>Monitorização do desempenho
Para monitorizar o desempenho dos serviços de armazenamento, pode utilizar as métricas seguintes das tabelas de métricas de hora a hora e minutos.

* Os valores existentes no **AverageE2ELatency** e **AverageServerLatency** colunas mostram a média do tempo o serviço de armazenamento ou tipo de operação de API está a demorar a pedidos de processo. **AverageE2ELatency** é uma medida de latência de ponto-a-ponto que inclui o tempo necessário para ler o pedido e enviar a resposta para além do tempo que demora a processar o pedido (, por conseguinte, a inclui a latência de rede quando o pedido atinge o serviço de armazenamento); **AverageServerLatency** é uma medida de apenas o tempo de processamento e, por conseguinte, exclui quaisquer latência de rede relacionadas com a comunicar com o cliente. Consulte a secção "[métricas mostram AverageE2ELatency alta e baixa AverageServerLatency]" mais tarde neste guia para um debate do motivo poderão existir uma diferença significativa entre estes dois valores.
* Os valores existentes no **TotalIngress** e **TotalEgress** colunas a mostrar a quantidade total de dados, em bytes, recebidos pelo e ficar fora do seu serviço de armazenamento ou através de um tipo de operação de API específico.
* Os valores existentes no **TotalRequests** coluna Mostrar o número total de pedidos que o serviço de armazenamento da operação de API está a receber. **TotalRequests** é o número total de pedidos que recebe o serviço de armazenamento.

Normalmente, irá monitorizar alterações inesperado em qualquer um destes valores como um indicador de que tem um problema que requerem a investigação.

No [portal do Azure](https://portal.azure.com), pode adicionar regras de alertas para notificá-lo se qualquer uma das métricas de desempenho para este serviço descerem abaixo ou exceder um limiar que especificar.

O "[orientações de resolução de problemas]" secção deste guia descreve alguns problemas comuns do serviço de armazenamento relacionados com o desempenho.

## <a name="diagnosing-storage-issues"></a>Diagnosticar problemas de armazenamento
Existem várias formas que que pode tornar-se em consideração um problema ou um problema na sua aplicação, estas incluem:

* Uma falha de principais que faz com que a aplicação para falhas ou deixe de funcionar.
* As alterações significativas de valores de linha de base nas métricas que está a monitorizar, tal como descrito na secção anterior "[monitorização do seu serviço de armazenamento]."
* Relatórios de utilizadores da sua aplicação que algumas operações específica não foram concluída conforme esperado ou que algumas funcionalidades não está a funcionar.
* Erros gerados na aplicação que aparecem nos ficheiros de registo ou através de algum outro método de notificação.

Normalmente, problemas relacionados com serviços de armazenamento do Azure enquadram-se um dos quatro amplas categorias:

* A aplicação tem um problema de desempenho comunicados pelos seus utilizadores, ou revelado por alterações de métricas de desempenho.
* Não há um problema com a infraestrutura de armazenamento do Azure num ou mais regiões.
* Verificou um erro comunicado pelos seus utilizadores, ou revelado por um aumento de uma das métricas de contagem de erros, monitorizar a sua aplicação.
* Durante o desenvolvimento e teste, pode utilizar o emulador de armazenamento local; poderá encontrar alguns problemas que estão especificamente relacionadas com a utilização do emulador de armazenamento.

As secções seguintes descrevem os passos que deverá seguir para diagnosticar e resolver problemas em cada um destes quatro categorias. A secção "[orientações de resolução de problemas]" mais adiante neste guia, fornece mais detalhes, para alguns problemas comuns que poderá encontrar.

### <a name="service-health-issues"></a>Problemas de estado de funcionamento de serviço
Problemas de estado de funcionamento de serviço normalmente estão fora do controlo. O [portal do Azure](https://portal.azure.com) fornece informações sobre quaisquer problemas em curso com serviços do Azure, incluindo os serviços de armazenamento. Se tiver optado por armazenamento Georredundante com acesso de leitura quando criou a conta de armazenamento, em seguida, no caso dos dados a ser disponível na localização principal, a aplicação foi possível mudar temporariamente para a cópia só de leitura na localização secundária. Para tal, a aplicação tem de conseguir alternar entre utilizar as localizações de armazenamento primário e secundário e ser capazes de trabalhar no modo de funcionalidade reduzida com dados só de leitura. As bibliotecas de cliente de armazenamento do Azure permitem-lhe definir uma política de repetição pode ler a partir do armazenamento secundário no caso de falha de uma leitura de armazenamento primário. A aplicação também tem de ter em consideração que os dados na localização secundária são eventualmente consistentes. Para obter mais informações, consulte a mensagem de blogue [as opções de redundância do armazenamento do Azure e de armazenamento redundantes do acesso de leitura Georreplicação](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problemas de desempenho
O desempenho de uma aplicação pode ser subjetivo, sobretudo na perspetiva do utilizador. Por conseguinte, é importante ter métricas de linha base disponíveis para o ajudar a identificar onde poderá existir um problema de desempenho. Vários fatores, poderão afetar o desempenho de um serviço de armazenamento do Azure da perspetiva de aplicação de cliente. Estes fatores podem operar no serviço de armazenamento, no cliente ou na infraestrutura de rede; Por conseguinte, é importante que tenha uma estratégia para identificar a origem do problema de desempenho.

Depois de identificar a localização provável da causa do problema de métricas de desempenho, em seguida, pode utilizar os ficheiros de registo para encontrar informações detalhadas para diagnosticar e resolver o problema continuar.

A secção "[orientações de resolução de problemas]" mais adiante neste guia fornece mais informações sobre algumas comuns desempenho relacionado com problemas que poderá encontrar.

### <a name="diagnosing-errors"></a>Erros de diagnóstico
Os utilizadores da sua aplicação podem notificá-lo de erros reportados pela aplicação de cliente. As métricas do Storage também regista contagens dos tipos de erro diferente de serviços de armazenamento como **NetworkError**, **ClientTimeoutError**, ou **AuthorizationError**. Enquanto as métricas do Storage apenas regista contagens dos tipos de erro diferente, pode obter mais detalhes sobre pedidos individuais, examinando o lado do servidor, do lado do cliente e registos de rede. Normalmente, o código de estado HTTP devolvido pelo serviço de armazenamento fornecem uma indicação do motivo pelo qual o pedido falhou.

> [!NOTE]
> Lembre-se de que devem esperar ver alguns erros intermitentes: por exemplo, os erros devido a condições de rede transitórios ou erros de aplicações.
> 
> 

Os seguintes recursos são úteis para compreender os códigos de erro e de estado relacionadas com o armazenamento:

* [Códigos de erro de API de REST comuns](http://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Códigos de erro do serviço blob](http://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Códigos de erro do serviço fila](http://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Códigos de erro do serviço tabela](http://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Códigos de erro do serviço de ficheiro](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problemas de emulador de armazenamento
O SDK do Azure inclui um emulador do storage que pode executar uma estação de trabalho de desenvolvimento. Este emulador simula maioria do comportamento dos serviços de armazenamento do Azure e é útil durante a programação e teste, permitindo-lhe executar aplicações que utilizam os serviços de armazenamento do Azure sem a necessidade de uma subscrição do Azure e uma conta de armazenamento do Azure.

O "[orientações de resolução de problemas]" secção deste guia descreve alguns problemas comuns encontrados utilizando o emulador de armazenamento.

### <a name="storage-logging-tools"></a>Ferramentas de registo de armazenamento
O registo de armazenamento fornece o registo do lado do servidor de pedidos de armazenamento na sua conta do storage do Azure. Para obter mais informações sobre como ativar o registo do lado do servidor e aceder aos dados de registo, consulte [aceder aos dados de registo e ativar o registo de armazenamento](http://go.microsoft.com/fwlink/?LinkId=510867).

A biblioteca de clientes de armazenamento para .NET permite-lhe recolher dados de registo do lado do cliente que está relacionada com operações de armazenamento efetuadas pela sua aplicação. Para obter mais informações, consulte [do lado do cliente registo com a biblioteca de clientes de armazenamento de .NET](http://go.microsoft.com/fwlink/?LinkId=510868).

> [!NOTE]
> Em algumas circunstâncias (como falhas de autorização de SAS), um utilizador poderá reportar um erro para o qual pode encontrar sem dados de pedido nos registos de armazenamento do lado do servidor. Pode utilizar as capacidades de registo da biblioteca de clientes de armazenamento para investigar se a causa do problema é no cliente ou utilizar ferramentas de monitorização de rede para investigar da rede.
> 
> 

### <a name="using-network-logging-tools"></a>Utilizar ferramentas de registo de rede
Pode capturar o tráfego entre o cliente e servidor para fornecer informações detalhadas sobre os dados que são trocar o cliente e o servidor e as condições de rede subjacente. Ferramentas de registo de rede útil incluem:

* [Fiddler](http://www.telerik.com/fiddler) é uma web livre depuração proxy que lhe permite examinar os cabeçalhos e dados do payload de mensagens de pedido e resposta HTTP e HTTPS. Para obter mais informações, consulte [apêndice 1: utilizar o Fiddler para capturar o tráfego HTTP e HTTPS](#appendix-1).
* [Monitor de rede da Microsoft (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) e [Wireshark](http://www.wireshark.org/) é rede livre analisadores de protocolo que permitem-lhe ver informações detalhadas do pacote para uma vasta gama de protocolos de rede. Para obter mais informações sobre o Wireshark, consulte "[apêndice 2: Wireshark a utilizar para capturar o tráfego de rede](#appendix-2)".
* Microsoft Message Analyzer é uma ferramenta da Microsoft que substitui o Netmon e que, além de captura de dados do pacote de rede, ajuda-o a ver e analisar os dados de registo capturados a partir de outras ferramentas. Para obter mais informações, consulte "[apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede](#appendix-3)".
* Se pretender efetuar um teste básico de conectividade para verificar se o seu computador cliente pode ligar ao serviço de armazenamento do Azure através da rede, que não pode fazer isto utilizando a norma **ping** ferramenta no cliente. No entanto, pode utilizar o [ **tcping** ferramenta](http://www.elifulkerson.com/projects/tcping.php) para verificar a conectividade.

Em muitos casos, os dados de registo do registo de armazenamento e a biblioteca de clientes de armazenamento serão suficientes para diagnosticar um problema, mas em alguns cenários, poderá ter as informações mais detalhadas que podem fornecer estas ferramentas de registo de rede. Por exemplo, com o Fiddler para ver mensagens de HTTP e HTTPS permite-lhe ver cabeçalho e o payload de dados enviados de e para os serviços de armazenamento, seriam permitem-lhe examinar a forma como uma aplicação cliente repete operações de armazenamento. Analisadores de protocolo, tais como o Wireshark funcionam ao nível do pacote, permitindo-lhe ver os dados TCP, seriam permitem-lhe resolver pacotes perdidas e problemas de conectividade. Analisador de mensagens pode operar em camadas HTTP e TCP.

## <a name="end-to-end-tracing"></a>Rastreio de ponto a ponto
Utilizando uma variedade de ficheiros de registo de rastreio de ponto a ponto é uma técnica útil para investigar potenciais problemas. Pode utilizar as informações de data/hora dos seus dados de métricas como uma indicação de onde pretende começar a procurar nos ficheiros de registo para as informações detalhadas que o irão ajudar a resolver o problema.

### <a name="correlating-log-data"></a>Correlacionar dados de registo
Ao visualizar registos de aplicações de cliente, rede rastreios e registo de armazenamento do lado do servidor é fundamental para conseguir correlacionar os pedidos entre os diferentes ficheiros de registo. Os ficheiros de registo incluem um número de diferentes campos que são úteis como identificadores de correlação. O ID do pedido de cliente é o campo mais úteis para utilizar para correlacionar entradas nos registos de diferentes. No entanto, por vezes, pode ser útil utilizar o ID de pedido do servidor ou carimbos. As secções seguintes fornecem mais detalhes sobre estas opções.

### <a name="client-request-id"></a>ID do pedido de cliente
A biblioteca de clientes de armazenamento gera automaticamente um ID de pedido de cliente exclusivos para cada pedido.

* No registo do lado do cliente cria a biblioteca de clientes de armazenamento, o ID do pedido de cliente é apresentado no **ID do pedido de cliente** campo cada entrada de registo relacionados com o pedido.
* Um rastreio de rede, tais como um capturado por Fiddler, o ID do pedido de cliente é visível em mensagens de pedido como o **x-ms-client-request-id** valor de cabeçalho HTTP.
* No registo de registo de armazenamento do lado do servidor, o ID do pedido de cliente é apresentado na coluna de ID do pedido de cliente.

> [!NOTE]
> É possível que vários pedidos partilhar o mesmo ID de pedido de cliente, porque o cliente pode atribuir este valor (embora a biblioteca de clientes de armazenamento atribui automaticamente um novo valor). No caso de tentativas do cliente, todas as tentativas partilham o mesmo ID de pedido de cliente. No caso de um lote enviado do cliente, o batch tem um ID de pedido de cliente único.
> 
> 

### <a name="server-request-id"></a>ID de pedido do servidor
O serviço de armazenamento gera automaticamente os ids de pedido do servidor.

* No registo de registo de armazenamento do lado do servidor, o ID de pedido do servidor é apresentado o **cabeçalho de ID do pedido** coluna.
* No rastreio de rede, tais como um capturado por Fiddler, o ID de pedido do servidor é apresentado nas mensagens de resposta, como o **x-ms-request-id** valor de cabeçalho HTTP.
* No registo do lado do cliente cria a biblioteca de clientes de armazenamento, o ID de pedido do servidor é apresentado no **operação texto** coluna para a entrada de registo que mostra detalhes sobre a resposta do servidor.

> [!NOTE]
> O serviço de armazenamento sempre atribui um servidor único ID do pedido para todos os pedidos a receber, para que cada tentativa de repetição do cliente e de cada operação incluído num batch tem um ID de pedido de servidor único.
> 
> 

Se a biblioteca de clientes de armazenamento emitir um **StorageException** no cliente, o **RequestInformation** propriedade contém um **RequestResult** objeto que inclui um **ServiceRequestID** propriedade. Também pode aceder um **RequestResult** objeto a partir de um **OperationContext** instância.

O exemplo de código abaixo demonstra como definir um personalizado **ClientRequestId** valor ao anexar um **OperationContext** o pedido para o serviço de armazenamento de objeto. Também mostra como obter o **ServerRequestId** valor da mensagem de resposta.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>Carimbos
Também pode utilizar carimbos para localizar as entradas de registo relacionadas, mas tenha o cuidado de qualquer desfasamento de relógio entre o cliente e servidor que pode existir. Deve procurar mais ou menos de 15 minutos para que correspondam a entradas do lado do servidor com base no timestamp no cliente. Lembre-se de que os metadados do blob para os blobs que contém as métricas indicam o intervalo de tempo para as métricas armazenados no blob; Isto é útil se tiver muitos blobs de métricas para o mesmo minuto ou hora.

## <a name="troubleshooting-guidance"></a>Documentação de orientação de resolução de problemas
Nesta secção irão ajudá-lo de diagnóstico e de resolução de problemas de alguns dos problemas comuns a aplicação poderá encontrar ao utilizar os serviços de armazenamento do Azure. Utilize a lista abaixo para localizar as informações relevantes para o problema específico.

**Resolução de problemas da árvore de decisões**

---
O problema referem-se ao nível de desempenho de um dos serviços de armazenamento?

* [métricas mostram AverageE2ELatency alta e baixa AverageServerLatency]
* [As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]
* [As métricas apresentam uma AverageServerLatency alta]
* [Ocorrem atrasos inesperados na entrega de mensagens numa fila]

---
O problema se relacionam com a disponibilidade de um dos serviços de armazenamento?

* [métricas mostram um aumento no PercentThrottlingError]
* [métricas mostram um aumento no PercentTimeoutError]
* [As métricas apresentam um aumento do PercentNetworkError]

---
 A aplicação cliente recebe uma resposta de 4XX (por exemplo, 404) HTTP um serviço de armazenamento?

* [O cliente está a receber mensagens HTTP 403 (proibido)]
* [O cliente está a receber mensagens HTTP 404 (não for encontrado)]
* [O cliente está a receber mensagens de HTTP 409 (conflito)]

---
[métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]

---
[Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]

---
[Estão a experienciar inesperados reinícios das máquinas virtuais que tenham um grande número de VHDs ligados]

---
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]

---
[Pode encontrar problemas ao instalar o Azure SDK para .NET]

---
[Tem um problema com um serviço de armazenamento diferente]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency alta e baixa AverageServerLatency
A ilustração abaixo do [portal do Azure](https://portal.azure.com) monitorização ferramenta mostra um exemplo em que o **AverageE2ELatency** é significativamente maior do que o **AverageServerLatency**.

![][4]

Tenha em atenção que o serviço de armazenamento apenas calcula a métrica **AverageE2ELatency** para pedidos com êxito e, ao contrário **AverageServerLatency**, inclui o tempo que o cliente demora para enviar os dados e receber a confirmação do serviço de armazenamento. Por conseguinte, terá uma diferença entre **AverageE2ELatency** e **AverageServerLatency** pode ser devido a aplicação de cliente que está a ser lentas a responder ou devido a condições na rede.

> [!NOTE]
> Também pode ver **E2ELatency** e **ServerLatency** para operações de armazenamento individuais no registo de armazenamento de dados de registo.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Investigar problemas de desempenho do cliente
Razões possíveis para o cliente responder lentamente incluem a falta de recursos, tais como CPU, memória ou a rede de largura de banda ou ter um número limitado de ligações disponíveis ou threads. É possível resolver o problema ao modificar o código de cliente para ser mais eficiente (por exemplo ao utilizar chamadas assíncronas para o serviço de armazenamento) ou através de uma Máquina Virtual maior (com mais memória e mais núcleos).

Para os serviços tabela e fila, o algoritmo de Nagle pode também fazer com que alta **AverageE2ELatency** compared para **AverageServerLatency**: Para mais informações, consulte o post [algoritmo do Nagle Não é amigável para pedidos de pequenas](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Pode desativar o algoritmo de Nagle no código utilizando a **ServicePointManager** classe no **System.Net** espaço de nomes. Deve fazê-lo antes de efetuar quaisquer chamadas para a tabela ou serviços da fila na sua aplicação, uma vez que isto não afeta as ligações que já estão a abrir. O exemplo seguinte vêm o **Application_Start** método numa função de trabalho.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Deve verificar os registos do lado do cliente para ver a aplicação de cliente é submeter os pedidos quantos e verifique a existência .NET geral relacionadas com congestionamentos de desempenho no seu cliente, tais como CPU, libertação da memória de .NET, a utilização da rede ou memória. Como um ponto de partida para aplicações de cliente .NET de resolução de problemas, consulte [Debugging, rastreio e criação de perfis](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Investigar problemas de latência de rede
Normalmente, latência elevada de ponto a ponto causada pela rede é devido a condições transitórias. Pode investigar ambos os problemas de rede transitórios e persistente, tais como pacotes ignorados, utilizando ferramentas como Wireshark ou o Microsoft Message Analyzer.

Para obter mais informações sobre como utilizar o Wireshark para resolver problemas de rede, consulte "[apêndice 2: com o Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer para resolver problemas de rede, consulte "[apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede]."

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Métricas mostram AverageE2ELatency baixa e AverageServerLatency baixa, mas o cliente está com latência elevada
Neste cenário, a causa mais provável é um atraso nos pedidos de armazenamento atingir o serviço de armazenamento. Deve investigar por que motivo pedidos do cliente não estabelecem-lo para o serviço blob.

Uma razão possível para o cliente atrasando enviar pedidos é o que há um número limitado de ligações disponíveis ou threads.

Também deve verificar se o cliente está a efetuar várias tentativas e investigue o motivo, se for este o caso. Para determinar se o cliente está a efetuar várias tentativas, pode:

* Examine os registos de análise de armazenamento. Se várias tentativas estavam a acontecer, verá várias operações com o mesmo ID de pedido de cliente, mas com IDs de pedido de servidor diferente.
* Examine os registos de cliente. O registo verboso indicará que ocorreu uma repetição.
* Depurar o seu código e assinale as propriedades do **OperationContext** objeto associado ao pedido. Se a operação foi tentada novamente, o **RequestResults** propriedade incluirá o pedido de servidor exclusivo vários IDs. Também pode ver os tempos de início e de fim de cada pedido. Para obter mais informações, consulte o código de exemplo na secção [ID do pedido de servidor].

Se existirem não existem problemas no cliente, deve investigar potenciais problemas de rede, tais como a perda de pacotes. Pode utilizar ferramentas como Wireshark ou o Microsoft Message Analyzer para investigar problemas de rede.

Para obter mais informações sobre como utilizar o Wireshark para resolver problemas de rede, consulte "[apêndice 2: com o Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer para resolver problemas de rede, consulte "[apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede]."

### <a name="metrics-show-high-AverageServerLatency"></a>Métricas mostram AverageServerLatency elevada
No caso de alta **AverageServerLatency** para pedidos de transferência de blob, deve utilizar os registos de registo de armazenamento para ver se existem repetidos pedidos para o blob mesmo (ou conjunto de blobs). Para o blob de carregar pedidos, deve investigar o bloco de tamanho, o cliente está a utilizar (por exemplo, bloqueia inferior a 64 KB de tamanho pode resultar em sobrecargas, a menos que as leituras são também em menos de 64K segmentos), e se estiver a múltiplos clientes carregar blocos para o blob mesmo em paralelo. Também deverá verificar as métricas por minuto para picos no número de pedidos que resultam exceder a por segundo metas de escalabilidade: também ver "[métricas mostram um aumento no PercentTimeoutError]."

Se vir alto **AverageServerLatency** para transferir BLOBs pedidos quando são repetidos pedidos mesmo blob ou conjunto de blobs, em seguida, deve considerar a colocação em cache estas blobs com a Cache do Azure ou a rede Azure entrega de conteúdos (CDN). Para pedidos de carregamento, pode melhorar o débito utilizando um tamanho de bloco maior. Para consultas de tabelas, também é possível implementar a colocação em cache do lado do cliente nos clientes que executam as mesmas operações de consulta e onde os dados não mudam frequentemente.

Elevada **AverageServerLatency** valores também podem ser um sintoma de tabelas mal concebidas ou consultas, que resultará na análise operações ou que seguem o padrão de anti de preceder/append. Consulte "[métricas mostram um aumento no PercentThrottlingError]" para obter mais informações.

> [!NOTE]
> Pode encontrar uma lista de verificação abrangente desempenho lista de verificação aqui: [desempenho de armazenamento do Microsoft Azure e a lista de verificação de escalabilidade](storage-performance-checklist.md).
> 
> 

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Ocorrerem atrasos inesperados na entrega de mensagens numa fila
Se um atraso entre a hora de que uma aplicação adiciona uma mensagem para uma fila e a hora, torna-se disponível para ler a partir da fila, em seguida, deve ter os seguintes passos para diagnosticar o problema:

* Certifique-se de que a aplicação é adicionar com êxito as mensagens para a fila. Verifique se a aplicação não está a repetir a **AddMessage** método várias vezes antes de ter êxito. Os registos de biblioteca de clientes de armazenamento mostrará quaisquer tentativas repetidas das operações de armazenamento.
* Certifique-se de que não existe nenhum relógio dissimetrias entre a função de trabalho que adiciona a mensagem à fila e a função de trabalho que lê a mensagem da fila que faz com que são apresentadas como se houver um atraso de processamento.
* Verifique se a função de trabalho que lê as mensagens da fila está a falhar. Se um cliente de fila chama o **GetMessage** método mas não conseguirá responder com uma confirmação, a mensagem irá permanecer invisível em fila até que o **invisibilityTimeout** do período. Neste momento, a mensagem fica disponível para processar novamente.
* Verifique se o comprimento da fila está a crescer ao longo do tempo. Isto pode ocorrer se não tiver trabalhadores suficientes disponíveis para processar todas as mensagens que outros funcionários estão a colocar em fila. Também deverá verificar as métricas para ver se eliminar os pedidos são repetidos falhar e a contagem de dequeue nas mensagens, que pode indicar tentativas falhadas ao tentar eliminar a mensagem.
* Examine os registos de registo de armazenamento para as operações de fila que tenham superior que o esperado **E2ELatency** e **ServerLatency** valores ao longo de um período de tempo que o habitual.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Métricas mostram um aumento no PercentThrottlingError
Limitação erros ocorrem quando exceder os destinos de escalabilidade de um serviço de armazenamento. O serviço de armazenamento efetua este procedimento para se certificar de que nenhum cliente único ou inquilino pode utilizar o serviço em detrimento dos outros. Para obter mais informações, consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter detalhes sobre metas de desempenho para partições dentro de contas de armazenamento e os objetivos de escalabilidade para contas de armazenamento.

Se o **PercentThrottlingError** métrica apresentar um aumento na percentagem de pedidos que estão a falhar com um erro de limitação, necessárias para investigar um dos dois cenários:

* [Aumento transitório PercentThrottlingError]
* [Aumento permanente PercentThrottlingError erro]

Um aumento de **PercentThrottlingError** frequentemente ocorre ao mesmo tempo que um aumento no número de pedidos de armazenamento, ou quando são inicialmente carregar testar a sua aplicação. Isto pode também manifesto do próprio no cliente, como "503 servidor ocupado" ou mensagens de estado HTTP "500 tempo limite da operação" de operações de armazenamento.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Aumento transitório PercentThrottlingError
Se vir picos no valor da **PercentThrottlingError** que a operação com períodos de grande atividade para a aplicação, deve implementar um back exponencial (não linear) desativar estratégia para tentativas no seu cliente: Esta ação irá reduzir a carga imediata na partição e ajudar a sua aplicação para uniforme os picos de tráfego. Para mais informações sobre como implementar as políticas de repetição com a biblioteca de clientes de armazenamento, consulte [Microsoft.WindowsAzure.Storage.RetryPolicies espaço de nomes](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [!NOTE]
> Também poderá ver picos no valor da **PercentThrottlingError** que não a operação com períodos de grande atividade para a aplicação: aqui a causa mais provável é o serviço de armazenamento mover partições para melhorar o balanceamento de carga.
> 
> 

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Aumento permanente PercentThrottlingError erro
Se vir um valor consistentemente elevado para **PercentThrottlingError** seguir um aumento permanente os volumes de transação, ou quando estiver a efetuar o carregamento inicial testes na sua aplicação, em seguida, terá de avaliar como a aplicação está a utilizar as partições de armazenamento e se este está a aproximar-se os destinos de escalabilidade da conta de armazenamento. Por exemplo, se vir a limitação de erros de uma fila (que conta como uma única partição), em seguida, deve considerar a utilização filas adicionais para distribuir as transações em várias partições. Se vir limitação erros numa tabela, terá de considerar a utilização de um esquema de partições diferente para distribuir as suas transações entre várias partições através da utilização de uma vasta gama de valores de chave de partição. Uma causa comum deste problema é o prepend/acrescentar anti padrão onde selecionou a data como a chave de partição e, em seguida, todos os dados num dia específico é escrito para uma partição: sob carga, isto pode resultar num estrangulamento de escrita. Deve considerar uma estrutura de criação de partições diferente ou avaliar se utilizando o blob storage pode ser a melhor solução. Também deve verificar se a limitação está a ocorrer em resultado picos de tráfego e investigar formas de suavização seu padrão de pedidos.

Se distribuir as suas transações entre várias partições, tem ainda de ser em consideração os limites de escalabilidade definidas para a conta de armazenamento. Por exemplo, se utilizou as dez filas cada processar o número máximo de 2000 1KB mensagens por segundo, será atingiu o limite de 20.000 mensagens em fila por segundo para a conta de armazenamento geral. Se precisar de processar mais de 20.000 entidades por segundo, deve considerar a utilização de várias contas de armazenamento. Também deve tenha em atenção que o tamanho dos seus pedidos e entidades tem um impacto no quando o serviço de armazenamento acelera os seus clientes: Se tiver o maior pedidos e as entidades, pode ser limitadas mais cedo.

Estrutura da consulta ineficaz também pode fazer com que atingido os limites de escalabilidade para partições de tabela. Por exemplo, uma consulta com um filtro que seleciona apenas uma percentagem das entidades numa partição, mas que analisa todas as entidades numa partição precisar de aceder a cada entidade. Cada entidade ler contabilizará o número total de transações em dessa partição; Por conseguinte, pode aceder facilmente os destinos de escalabilidade.

> [!NOTE]
> Os testes de desempenho devem ficar a saber de qualquer estruturas de consulta ineficaz na sua aplicação.
> 
> 

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Métricas mostram um aumento no PercentTimeoutError
Métricas de apresentar um aumento no **PercentTimeoutError** para um dos seus serviços de armazenamento. Ao mesmo tempo, o cliente recebe um elevado volume de mensagens de estado HTTP "500 tempo limite da operação" de operações de armazenamento.

> [!NOTE]
> Poderá ver erros de tempo limite temporariamente como o serviço de armazenamento a carga de pedidos de saldos movendo uma partição para um novo servidor.
> 
> 

O **PercentTimeoutError** métrica é uma agregação de métricas seguintes: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**, e **SASServerTimeoutError**.

Os tempos limite de servidor são causados por um erro no servidor. Os tempos limite de cliente ter acontecido porque uma operação no servidor excedeu o tempo limite especificado pelo cliente; Por exemplo, um cliente utilizando a biblioteca de clientes de armazenamento pode definir o limite de tempo para uma operação utilizando o **ServerTimeout** propriedade o **QueueRequestOptions** classe.

Tempos limite de servidor indicarem um problema com o serviço de armazenamento que requerem a investigação. Pode utilizar as métricas para ver se estão a atingir os limites de escalabilidade para o serviço e para identificar os picos de tráfego que poderá estar a causar o problema. Se o problema intermitente, pode ser devido ao balanceamento de carga atividade no serviço. Se o problema é persistente e não é causado pela sua aplicação atingir os limites de escalabilidade do serviço, deve aumentar um problema de suporte. Para tempos limite de cliente, terá de decidir se o tempo limite está definido como um valor adequado no cliente e a alterar o valor de tempo limite definido no cliente ou investigar como pode melhorar o desempenho das operações no serviço de armazenamento, por exemplo, otimizar as consultas de tabela ou reduzir o tamanho do seu mensagens.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Métricas mostram um aumento no PercentNetworkError
Métricas de apresentar um aumento no **PercentNetworkError** para um dos seus serviços de armazenamento. O **PercentNetworkError** métrica é uma agregação de métricas seguintes: **NetworkError**, **AnonymousNetworkError**, e **SASNetworkError**. Estes ocorrerem quando o serviço de armazenamento Deteta um erro de rede quando o cliente faz um pedido de armazenamento.

A causa mais comum deste erro é um cliente desligar antes de um tempo limite expira no serviço de armazenamento. Deve investigar o código no seu cliente para compreender por que razão e quando o cliente desligar do serviço de armazenamento. Também pode utilizar o Wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. Estas ferramentas são descritas no [Appendices].

### <a name="the-client-is-receiving-403-messages"></a>O cliente está a receber mensagens HTTP 403 (proibido)
Se a aplicação cliente que está a gerar erros de HTTP 403 (proibido), uma causa provável é que o cliente está a utilizar um expirada acesso assinatura partilhado (SAS) ao enviar um pedido de armazenamento (embora outras causas possíveis incluem as chaves inválido, dissimetrias do relógio e cabeçalhos vazios). Se uma chave SAS expirada é a causa, não visualizará quaisquer entradas de dados de registo do registo de armazenamento do lado do servidor. A tabela seguinte mostra um exemplo de registo do lado do cliente gerado pela biblioteca de clientes de armazenamento que ilustra a ocorrer este problema:

| Origem | Verbosidade | Verbosidade | Id do pedido de cliente | Texto de operação |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |Iniciar a operação com a localização principal por PrimaryOnly do modo de localização. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |Iniciar o pedido síncrono https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;Tama = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;api-version = 2014-02-14. |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A aguardar resposta. |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab-... |Excepção emitida ao aguardar pela resposta: O servidor remoto devolveu um erro: proibido (403)... |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |Resposta recebida. Código de estado = 403, ID do pedido = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =. |
| Microsoft.WindowsAzure.Storage |Aviso |2 |85d077ab-... |Ocorreu uma excepção durante a operação: O servidor remoto devolveu um erro: proibido (403)... |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A verificar se a operação deve ser repetida. Contagem de tentativas = 0, código de estado HTTP 403, exceção de = = o servidor remoto devolvido um erro: proibido (403)... |
| Microsoft.WindowsAzure.Storage |Informações |3 |85d077ab-... |A localização seguinte foi definida para o servidor primário, o modo de localização. |
| Microsoft.WindowsAzure.Storage |Erro |1 |85d077ab-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: proibido (403). |

Neste cenário, deve investigar por que motivo o token SAS está prestes a expirar antes do cliente envia o token para o servidor:

* Normalmente, não deverá definir uma hora de início quando cria um SAS para um cliente para utilizar imediatamente. Se existem relógio pequenas diferenças entre os anfitriões a gerar a SAS utilizando a hora atual e o serviço de armazenamento, em seguida, é possível que o serviço de armazenamento receber uma SAS que ainda não é válida.
* Não deve definir um período de tempo muito curto expiração numa SAS. Novamente, relógio pequeno as diferenças entre o anfitrião a gerar a SAS e o serviço de armazenamento podem levar a uma SAS parecer expira anteriormente que o antecipado.
* O parâmetro de versão na chave SAS (por exemplo **sv = 2015-04-05**) corresponde à versão da biblioteca de clientes de armazenamento que está a utilizar? Recomendamos que utilize sempre a versão mais recente do [biblioteca de clientes de armazenamento](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Se voltar a gerar as chaves de acesso de armazenamento, isto pode invalidar quaisquer tokens SAS existentes. Isto pode ser um problema se gerar tokens SAS com um tempo de expiração longos para aplicações de cliente para a cache.

Se estiver a utilizar a biblioteca de clientes de armazenamento para gerar SAS tokens, é fácil de criar um token válido. No entanto, se estiver a utilizar a API de REST do Storage e construir a SAS tokens manualmente cuidadosamente leia o tópico [delegar o acesso com uma assinatura de acesso partilhado](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>O cliente está a receber mensagens HTTP 404 (não for encontrado)
Se a aplicação cliente recebe uma mensagem de HTTP 404 (não for encontrado) do servidor, isto implica que o objeto, que o cliente estava a tentar utilizar (tal como uma entidade, tabelas, BLOBs, contentor ou filas) não existe no serviço de armazenamento. Há uma série de razões possíveis para tal, tais como:

* [O cliente ou outro processo anteriormente eliminou o objeto]
* [Um problema de autorização de assinatura de acesso partilhado (SAS)]
* [No código JavaScript do lado do cliente não tem permissão para aceder ao objeto]
* [Falha de rede]

#### <a name="client-previously-deleted-the-object"></a>O cliente ou outro processo anteriormente eliminou o objeto
Em cenários em que o cliente está a tentar ler, atualizar ou eliminar dados de um serviço de armazenamento é normalmente fácil identificar nos registos do lado do servidor de uma operação anterior que eliminou o objeto em questão a partir do serviço de armazenamento. Muito frequentemente, os dados de registo mostram que outro utilizador ou processo eliminou o objeto. No registo de registo de armazenamento do lado do servidor, o tipo de operação e a pedido objeto-colunas chave mostram quando um cliente eliminado um objeto.

O cenário em que um cliente está a tentar inserir um objeto, poderá não ser imediatamente óbvios razão pela qual esta operação resulta numa resposta de HTTP 404 (não for encontrado) uma vez que o cliente está a criar um novo objeto. No entanto, se o cliente está a criar um blob tem de ser capaz de localizar o contentor de blob, se o cliente está a criar uma mensagem tem de ser capaz de localizar uma fila, e se o cliente está a adicionar uma linha tem de ser capaz de localizar a tabela.

Pode utilizar o registo do lado do cliente da biblioteca de cliente de armazenamento para obter uma compreensão mais detalhada do quando o cliente envia pedidos específicos para o serviço de armazenamento.

O registo do lado do cliente seguinte gerado pela biblioteca de clientes de armazenamento ilustra o problema, quando o cliente não é possível localizar o contentor do blob que está a criar. Este registo inclui detalhes das seguintes operações de armazenamento:

| ID do pedido | Operação |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** método ao eliminar o contentor de blob. Tenha em atenção que esta operação inclui um **HEAD** pedido para verificar a existência do contentor. |
| e2d06d78... |**CreateIfNotExists** método para criar o contentor de blob. Tenha em atenção que esta operação inclui um **HEAD** pedido que verifica a existência do contentor. O **HEAD** devolve uma mensagem 404, mas continua operacional. |
| de8b1c3c-... |**UploadFromStream** método para criar o blob. O **colocar** pedido falha apresentando uma mensagem 404 |

Entradas de registo:

| ID do pedido | Texto de operação |
| --- | --- |
| 07b26a5d-... |A iniciar o pedido síncrono https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 de Jun de 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |A aguardar resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado 200, ID do pedido de = = eeead849... Content-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o resto da operação. |
| 07b26a5d-... |Transferir o corpo da resposta. |
| 07b26a5d-... |Operação concluída com êxito. |
| 07b26a5d-... |A iniciar o pedido síncrono https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 de Jun de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |A aguardar resposta. |
| 07b26a5d-... |Resposta recebida. Código de estado = 202, ID do pedido = 6ab2a4cf-..., Content-MD5 = ETag =. |
| 07b26a5d-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o resto da operação. |
| 07b26a5d-... |Transferir o corpo da resposta. |
| 07b26a5d-... |Operação concluída com êxito. |
| e2d06d78-... |A iniciar um pedido assíncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 de Jun de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |A aguardar resposta. |
| de8b1c3c-... |A iniciar o pedido síncrono https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-blob-type:BlockBlob.x-MS-Client-Request-ID:de8b1c3c-...x-MS-Date:TUE, 03 de Jun de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |A preparar a escrever os dados do pedido. |
| e2d06d78-... |Excepção emitida ao aguardar pela resposta: O servidor remoto devolveu um erro: (404) não encontrado... |
| e2d06d78-... |Resposta recebida. Código de estado 404, ID do pedido de = = 353ae3bc-..., Content-MD5 = ETag =. |
| e2d06d78-... |Cabeçalhos de resposta foram processados com êxito, prosseguir com o resto da operação. |
| e2d06d78-... |Transferir o corpo da resposta. |
| e2d06d78-... |Operação concluída com êxito. |
| e2d06d78-... |A iniciar um pedido assíncrono para https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT... 0...x-MS-Client-Request-ID:e2d06d78-...x-MS-Date:TUE, 03 de Jun de 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |A aguardar resposta. |
| de8b1c3c-... |Escrever os dados no pedido. |
| de8b1c3c-... |A aguardar resposta. |
| e2d06d78-... |Excepção emitida ao aguardar pela resposta: O servidor remoto devolveu um erro: conflito (409)... |
| e2d06d78-... |Resposta recebida. Código de estado = 409, ID do pedido = c27da20e-..., Content-MD5 = ETag =. |
| e2d06d78-... |Transferir o corpo da resposta de erro. |
| de8b1c3c-... |Excepção emitida ao aguardar pela resposta: O servidor remoto devolveu um erro: (404) não encontrado... |
| de8b1c3c-... |Resposta recebida. Código de estado 404, ID do pedido de = = 0eaeab3e-..., Content-MD5 = ETag =. |
| de8b1c3c-... |Ocorreu uma excepção durante a operação: O servidor remoto devolveu um erro: (404) não encontrado... |
| de8b1c3c-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: (404) não encontrado... |
| e2d06d78-... |Política de repetição não permitiu para uma nova tentativa. Falha ao servidor remoto devolveu um erro: conflito (409)... |

Neste exemplo, o registo mostra que o cliente é interleaving pedidos a partir de **CreateIfNotExists** método (pedido id e2d06d78...) com os pedidos do **UploadFromStream** (de8b1c3c o método serializationmethod....); Isto acontece porque a aplicação de cliente é a invocar estes métodos de forma assíncrona. Deve modificar o código assíncrono no cliente para se certificar de que cria o contentor antes de tentar carregar dados para um blob no contentor. Idealmente, deve criar todos os seus contentores antecipadamente.

#### <a name="SAS-authorization-issue"></a>Um problema de autorização de assinatura de acesso partilhado (SAS)
Se a aplicação de cliente tenta utilizar uma chave SAS que não inclua as permissões necessárias para a operação, o serviço de armazenamento devolve uma mensagem de HTTP 404 (não for encontrado) para o cliente. Ao mesmo tempo, verá também um valor diferente de zero para **SASAuthorizationError** nas métricas.

A tabela seguinte mostra uma mensagem de registo do lado do servidor de exemplo do ficheiro de registo de registo de armazenamento:

| Nome | Valor |
| --- | --- |
| Hora de início de pedido | 2014-05-30T06:17:48.4473697Z |
| Tipo de operação     | GetBlobProperties            |
| Estado do pedido     | SASAuthorizationError        |
| Código de estado HTTP   | 404                          |
| Tipo de autenticação| SAs                          |
| Tipo de serviço       | Blobs                         |
| URL do pedido        | https://domemaildist.blob.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ? sv = 2014-02-14 & sr = Tama & c = mypolicy & sig = XXXXX&;api-version = 2014-02-14 |
| Cabeçalho de id do pedido  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| ID do pedido de cliente  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Deve investigar por que razão a aplicação de cliente está a tentar efetuar uma operação que tem não foram concedido permissões para.

#### <a name="JavaScript-code-does-not-have-permission"></a>No código JavaScript do lado do cliente não tem permissão para aceder ao objeto
Se estiver a utilizar um cliente de JavaScript e o serviço de armazenamento está a devolver mensagens HTTP 404, verifique se existem os seguintes erros de JavaScript no browser:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Pode utilizar as ferramentas de programador F12 no Internet Explorer para rastrear as mensagens trocadas entre o browser e o serviço de armazenamento, quando a resolução de problemas de JavaScript do lado do cliente.
> 
> 

Estes erros ocorrerem porque o browser implementa o [mesma política de origem](http://www.w3.org/Security/wiki/Same_Origin_Policy) provém de restrição de segurança que impede que uma página web ao chamar uma API num domínio diferente do domínio a página do.

Para solucionar o problema de JavaScript, pode configurar cruzada origem Resource Sharing (CORS) para o serviço de armazenamento, que o cliente está a aceder. Para obter mais informações, consulte [suporte de partilha de recursos de várias origens (CORS) para serviços de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

O exemplo de código seguinte mostra como configurar o serviço blob para permitir o JavaScript em execução no domínio Contoso aceder a um blob no seu serviço de armazenamento de BLOBs:

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>Falha de rede
Em algumas circunstâncias, pacotes de rede perdida podem levar ao serviço de armazenamento devolver mensagens de HTTP 404 ao cliente. Por exemplo, quando a aplicação de cliente está a eliminar uma entidade do serviço tabela, consulte o cliente acionar um relatório de exceção de armazenamento um "HTTP 404 (não for encontrado)" mensagem de estado do serviço tabela. Quando estiver a investigar a tabela no serviço de armazenamento de tabela, verá que o serviço foi possível eliminar a entidade conforme foi pedido.

Os detalhes da exceção no cliente incluem o id de pedido (7e84f12d …) atribuído pelo serviço tabela para o pedido: pode utilizar estas informações para localizar os detalhes do pedido nos registos do lado do servidor de armazenamento ao pesquisar no **cabeçalho de id de pedido** coluna no ficheiro de registo. Também pode utilizar as métricas para identificar quando falhas como esta ocorrerem e, em seguida, procure os ficheiros de registo com base na hora que as métricas registadas este erro. Esta entrada de registo mostra que a eliminação falhou com uma mensagem de estado de "Cliente outro erro HTTP (404)". A entrada de registo mesmo também inclui o id do pedido gerado pelo cliente no **client-request-id** coluna (813ea74f...).

O registo do lado do servidor inclui também outra entrada com o mesmo **client-request-id** operação para a mesma entidade e para o mesmo cliente para eliminar o valor (813ea74f...) para um com êxito. Esta operação de eliminação com êxito demorou muito em breve local antes de eliminar a pedido.

A causa mais provável deste cenário é que o cliente enviou um pedido de eliminação para a entidade no serviço tabela, que foi concluída com êxito, mas não recebeu uma confirmação do servidor (possivelmente devido a um problema de temporários da rede). O cliente, em seguida, repetir automaticamente a operação (com o mesmo **client-request-id**), e esta tentativa falhou porque a entidade já tinha sido eliminada.

Se este problema ocorrer com frequência, deve investigar por que motivo o cliente está a falhar receber confirmações do serviço tabela. Se o problema intermitente, deve trap o erro "HTTP (404) não encontrada" e o cliente de sessão, mas permitir que o cliente continuar.

### <a name="the-client-is-receiving-409-messages"></a>O cliente está a receber mensagens de HTTP 409 (conflito)
A tabela seguinte mostra um extrair a partir do registo do lado do servidor para duas operações de cliente: **DeleteIfExists** seguido imediatamente por **CreateIfNotExists** utilizando o mesmo nome de contentor do blob. Tenha em atenção que a operação de cada cliente resulta em duas pedidos enviados para o servidor, primeiro um **GetContainerProperties** pedido para verificar se o contentor existe, seguido de **DeleteContainer** ou **CreateContainer** pedido.

| Timestamp | Operação | resultado | Nome do contentor | Id do pedido de cliente |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

O código da aplicação de cliente elimina e, em seguida, recria imediatamente um contentor do blob com o mesmo nome: o **CreateIfNotExists** método (pedidos de cliente ID bc881924-...), eventualmente, falha com o erro de HTTP 409 (conflito). Quando um cliente Elimina contentores de BLOBs, tabelas ou filas há um breve período antes do nome fica disponível novamente.

A aplicação cliente deve utilizar os nomes de contentor exclusivo sempre que cria novos contentores se o padrão de eliminação/recrie é comum.

### <a name="metrics-show-low-percent-success"></a>Métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors
O **PercentSuccess** métrica captura a percentagem de operações que foram bem-sucedidas, com base no respetivo código de estado de HTTP. Contagem de operações com códigos de estado de 2XX conclusão com êxito, enquanto as operações com códigos de estado no 3XX, 4XX 5XX intervalos são contadas como êxito e inferior a **PercentSucess** valor métrico. Nos ficheiros de registo do armazenamento do lado do servidor, estas operações são registadas com um Estado de transação de **ClientOtherErrors**.

É importante ter em atenção que estas operações concluíram com êxito e, por conseguinte, não afetam outras métricas, como a disponibilidade. Alguns exemplos de operações que ser executado com êxito, mas que pode resultar em códigos de estado HTTP sem incluem:

* **ResourceNotFound** (não foi encontrado 404), por exemplo a partir de um pedido GET para um blob que não existe.
* **ResouceAlreadyExists** (conflito 409), por exemplo a partir de um **CreateIfNotExist** operação onde o recurso já existe.
* **ConditionNotMet** (não modificados 304), por exemplo a partir de uma operação condicional, tais como quando um cliente envia um **ETag** valor e um HTTP **If-None-Match** cabeçalho para pedir uma imagem apenas se tiver sido atualizado desde a última operação.

Pode encontrar uma lista de códigos de erro de REST API comuns que os serviços de armazenamento devolvem na página [códigos de erro da API de REST comuns](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento
Se vir repentino, alterações inesperadas na utilização da capacidade na sua conta de armazenamento, pode investigar as razões observando primeiro as métricas de disponibilidade; Por exemplo, um aumento no número de eliminação falhou pedidos podem representar um aumento na quantidade de armazenamento de BLOBs que está a utilizar como operações de limpeza de específicas da aplicação que poderá ter destinados a ser libertar espaço em podem não estar a funcionar conforme esperado (por exemplo porque os tokens SAS utilizados para libertar espaço expiraram).

### <a name="you-are-experiencing-unexpected-reboots"></a>Estão a experienciar reinícios inesperados de Virtual Machines do Azure que tenham um grande número de VHDs ligados
Se uma Máquina Virtual do Azure (VM) tem um grande número de VHDs anexados que estão na mesma conta de armazenamento, poderá exceder os destinos de escalabilidade para uma conta de armazenamento individuais, fazendo com que a VM falhar. Deve verificar as métricas de minutos para a conta de armazenamento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para picos que excedem os destinos de escalabilidade da conta de armazenamento. Consulte a secção "[métricas mostram um aumento no PercentThrottlingError]" para a assistência para determinar se a limitação ocorreu na sua conta de armazenamento.

Em geral, cada entrada individuais ou a operação de saída num VHD de uma Máquina Virtual traduz-se **obter página** ou **colocar página** operações no blob de página subjacente. Por conseguinte, pode utilizar o IOPS estimado para o seu ambiente para otimizar os VHDs quantos pode numa única conta de armazenamento com base no comportamento específico da sua aplicação. Não é recomendada a ter mais de 40 discos uma única conta de armazenamento. Consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter detalhes sobre as atual metas de escalabilidade para contas de armazenamento, em particular a pedido total velocidade total largura de banda e para o tipo de conta de armazenamento que está a utilizar.
Se está a exceder os destinos de escalabilidade para a sua conta de armazenamento, deve colocar os seus VHDs em várias contas de armazenamento diferentes para reduzir da atividade em cada conta individual.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste
Normalmente, utilizar o emulador de armazenamento durante o desenvolvimento e teste para evitar a necessidade de uma conta de armazenamento do Azure. Os problemas comuns que podem ocorrer quando estiver a utilizar o emulador do storage são:

* [A funcionalidade "X" não está a funcionar no emulador do storage]
* [Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" quando utilizar o emulador de armazenamento]
* [Executar o emulador do storage é necessários privilégios administrativos]

#### <a name="feature-X-is-not-working"></a>A funcionalidade "X" não está a funcionar no emulador do storage
O emulador de armazenamento não suporta todas as funcionalidades dos serviços de armazenamento do Azure, tais como o serviço de ficheiros. Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md).

Para essas funcionalidades que não suporta o emulador do storage, utilize o serviço de armazenamento do Azure na nuvem.

#### <a name="error-HTTP-header-not-correct-format"></a>Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" quando utilizar o emulador de armazenamento
Está a testar a aplicação que utiliza a biblioteca de clientes de armazenamento contra as chamadas de método e emulador de armazenamento local como **CreateIfNotExists** falhar com a mensagem de erro "o valor para um dos cabeçalhos de HTTP não está correto no formato de." Isto indica que a versão do emulador de armazenamento que está a utilizar não suporta a versão da biblioteca de clientes de armazenamento que está a utilizar. A biblioteca de clientes de armazenamento adiciona o cabeçalho **x-ms-version** para todos os pedidos faz. Se o emulador de armazenamento não reconhece o valor de **x-ms-version** cabeçalho, rejeita o pedido.

Pode utilizar os registos de cliente da biblioteca de armazenamento para ver o valor da **cabeçalho x-ms-version** está a enviar. Também pode ver o valor da **cabeçalho x-ms-version** se utilizar o Fiddler para os pedidos de rastreio a partir da sua aplicação de cliente.

Este cenário ocorre normalmente se instalar e utilizar a versão mais recente da biblioteca de clientes de armazenamento sem atualizar o emulador de armazenamento. Deve instalar a versão mais recente do emulador de armazenamento ou utilizar o armazenamento na nuvem em vez do emulador do desenvolvimento e teste.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Executar o emulador do storage é necessários privilégios administrativos
Lhe for pedido para as credenciais de administrador ao executar o emulador de armazenamento. Isto só ocorre quando está a inicializar o emulador do storage pela primeira vez. Depois de ter iniciado o emulador de armazenamento, não precisa de privilégios administrativos para executá-la novamente.

Para obter mais informações, veja [Utilizar o Emulador de Armazenamento do Azure para Programação e Teste](storage-use-emulator.md). Tenha em atenção que também pode iniciar o emulador do storage no Visual Studio, será também necessitam de privilégios administrativos.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Pode encontrar problemas ao instalar o Azure SDK para .NET
Quando tentar instalar o SDK, falhará a tentar instalar o emulador de armazenamento no seu computador local. O registo de instalação contém uma das seguintes mensagens:

* CAQuietExec: Erro: não é possível aceder a instância do SQL Server
* CAQuietExec: Erro: não é possível criar a base de dados

A causa é um problema com a instalação existente de LocalDB. Por predefinição, o emulador de armazenamento utiliza LocalDB para manter os dados ao mesmo simula os serviços de armazenamento do Azure. Pode repor a sua instância da LocalDB, executando os comandos seguintes numa janela de linha de comandos antes de tentar instalar o SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

O **eliminar** comando remove quaisquer ficheiros de base de dados antigos de instalações anteriores do emulador de armazenamento.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Tem um problema com um serviço de armazenamento diferente
Se as secções de resolução de problemas anteriores não incluírem o problema que estiver a ter com um serviço de armazenamento, deve adotar a seguinte abordagem para diagnosticar e resolver o seu problema.

* Verifique as métricas para ver se existe alguma alteração do seu comportamento de linha de base esperado. De métricas, poderá determinar se o problema é transitório ou permanente e as operações de armazenamento que o problema está a afetar.
* Pode utilizar as informações de métricas para o ajudar a procurar os dados de registo do lado do servidor para obter informações mais detalhadas sobre quaisquer erros que estão a ocorrer. Esta informação pode ajudar a resolver o problema.
* Se as informações nos registos do lado do servidor não são suficientes para solucionar o problema com êxito, pode utilizar os registos do lado do cliente de biblioteca de clientes de armazenamento para investigar o comportamento da sua aplicação de cliente e ferramentas como o Fiddler, o Wireshark e o Microsoft Message Analyzer para investigar a sua rede.

Para obter mais informações sobre como utilizar o Fiddler, consulte "[apêndice 1: utilizar o Fiddler para capturar o tráfego HTTP e HTTPS]."

Para obter mais informações sobre como utilizar o Wireshark, consulte "[apêndice 2: com o Wireshark para capturar o tráfego de rede]."

Para obter mais informações sobre como utilizar o Microsoft Message Analyzer, consulte "[apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede]."

## <a name="appendices"></a>Appendices
Os appendices descrevem várias ferramentas que poderão considerar úteis quando estiver a diagnosticar e resolver problemas com o Storage do Azure (e outros serviços). Estas ferramentas não fazem parte do armazenamento do Azure e alguns são produtos de terceiros. Como tal, as ferramentas abordadas estes appendices não são abrangidas por qualquer contrato de suporte que poderá ter com o Microsoft Azure ou o armazenamento do Azure e, por conseguinte, como parte do processo de avaliação, deve examinar os suporte e licenciamento opções disponíveis dos fornecedores destas ferramentas.

### <a name="appendix-1"></a>Apêndice 1: Utilizar o Fiddler para capturar o tráfego HTTP e HTTPS
[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta útil para analisar o tráfego HTTP e HTTPS entre a aplicação cliente e o serviço de armazenamento do Azure que está a utilizar.

> [!NOTE]
> Fiddler capaz de descodificar tráfego HTTPS; Leia a documentação de Fiddler cuidadosamente de forma a compreender como fazê-lo e para compreender as implicações de segurança.
> 
> 

Este anexo fornece uma breve obter instruções sobre como configurar o Fiddler para capturar o tráfego entre o computador local onde instalou o Fiddler e os serviços de armazenamento do Azure.

Depois de ter sido iniciada Fiddler, irá começar a capturar o tráfego HTTP e HTTPS no seu computador local. Seguem-se alguns comandos útil para controlar o Fiddler:

* Parar e iniciar a capturar o tráfego. No menu principal, aceda a **ficheiro** e, em seguida, clique em **capturar tráfego** para alternar a capturar e desativar.
* Guarde os dados de tráfego capturada. No menu principal, aceda a **ficheiro**, clique em **guardar**e, em seguida, clique em **todas as sessões**: Isto permite-lhe guardar o tráfego num ficheiro de arquivo de sessão. Pode recarregar um arquivo de sessão mais tarde para uma análise ou enviá-lo se o pedido de suporte da Microsoft.

Para limitar a quantidade de tráfego que capture o Fiddler, pode utilizar filtros que configura o **filtros** separador. A seguinte captura de ecrã mostra um filtro que capture apenas o tráfego enviado para o **contosoemaildist.table.core.windows.net** ponto final de armazenamento:

![][5]

### <a name="appendix-2"></a>Apêndice 2: Wireshark a utilizar para capturar o tráfego de rede
[Wireshark](http://www.wireshark.org/) é um analisador de protocolos de rede que permite-lhe ver informações detalhadas do pacote para uma vasta gama de protocolos de rede.

O procedimento seguinte mostra como capturar informações detalhadas do pacote para o tráfego do computador local onde instalou o Wireshark no serviço tabela na sua conta do storage do Azure.

1. Inicie o Wireshark no seu computador local.
2. No **iniciar** secção, selecione a interface de rede local ou as interfaces que estão ligadas à internet.
3. Clique em **capturar opções**.
4. Adicionar um filtro para o **capturar filtro** caixa de texto. Por exemplo, **alojar contosoemaildist.table.core.windows.net** irá configurar o Wireshark para capturar apenas os pacotes enviados para ou a partir do ponto final do serviço tabela no **contosoemaildist** conta de armazenamento. Veja o [lista completa de filtros de capturar](http://wiki.wireshark.org/CaptureFilters).
   
   ![][6]
5. Clique em **iniciar**. Wireshark agora irá capturar todos os pacotes de enviar para o ponto final de serviço de tabela como utilizar a aplicação de cliente no seu computador local.
6. Quando tiver terminado, do menu principal, clique em **capturar** e, em seguida, **parar**.
7. Para guardar os dados capturados num ficheiro capturar Wireshark, no menu principal clique **ficheiro** e, em seguida, **guardar**.

WireShark dar destaque quaisquer erros que existem no **packetlist** janela. Também pode utilizar o **especialista informações** janela (clique **analisar**, em seguida, **especialista informações**) para ver um resumo de erros e avisos.

![][7]

Também pode escolher ver os dados TCP como camada da aplicação vê-lo ao clicar nos dados TCP e selecionando **siga fluxo TCP**. Isto é particularmente útil se capturou sua informação sem um filtro de captura. Para obter mais informações, consulte [seguintes fluxos TCP](http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Para obter mais informações sobre como utilizar o Wireshark, consulte o [Wireshark utilizadores guia](http://www.wireshark.org/docs/wsug_html_chunked).
> 
> 

### <a name="appendix-3"></a>Apêndice 3: Utilizar o Microsoft Message Analyzer para capturar o tráfego de rede
Pode utilizar o Microsoft Message Analyzer para capturar o tráfego HTTP e HTTPS de forma semelhante para o Fiddler e capturar o tráfego de rede de forma semelhante para Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurar uma sessão de rastreio web utilizando o Microsoft Message Analyzer
Para configurar uma sessão de rastreio web para o tráfego HTTP e HTTPS, utilizando o Microsoft Message Analyzer, executar a aplicação Microsoft Message Analyzer e, em seguida, no **ficheiro** menu, clique em **captura/rastreio**. Na lista de cenários de rastreio disponíveis, selecione **Web Proxy**. Em seguida, no **configuração de cenário de rastreio** no painel a **HostnameFilter** caixa de texto, adicionar os nomes dos seus pontos finais de armazenamento (pode procurar estes nomes a [portal do Azure](https://portal.azure.com)). Por exemplo, se o nome da sua conta do storage do Azure é **contosodata**, deve adicionar o seguinte para o **HostnameFilter** caixa de texto:

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Um caráter de espaço separa os nomes de anfitriões.
> 
> 

Quando estiver pronto para iniciar a recolha de dados de rastreio, clique o **Start With** botão.

Para obter mais informações sobre o Microsoft Message Analyzer **Web Proxy** de rastreio, consulte [fornecedor do Microsoft-PEF-WebProxy](http://technet.microsoft.com/library/jj674814.aspx).

Incorporada **Web Proxy** trace no Microsoft Message Analyzer baseia-se no Fiddler; pode capturar o tráfego HTTPS do lado do cliente e apresentar mensagens HTTPS não encriptadas. O **Web Proxy** rastreio funciona ao configurar um proxy local para todos os tráfego HTTP e HTTPS, que concede-lhe acesso às mensagens não encriptadas.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnosticar problemas de rede com o Microsoft Message Analyzer
Além de utilizar o Microsoft Message Analyzer **Web Proxy** trace para capturar os detalhes do tráfego HTTP/HTTPs entre a aplicação de cliente e o serviço de armazenamento, pode também utilizar incorporada **camada de ligação Local** trace para capturar informações de pacotes de rede. Este permite-lhe capturar dados semelhantes ao que pode capturar com o Wireshark e diagnosticar problemas de rede, tais como remover pacotes.

A seguinte captura de ecrã mostra um exemplo **camada de ligação Local** rastreio com algumas **informativa** mensagens no **DiagnosisTypes** coluna. Clicar no ícone de **DiagnosisTypes** coluna mostra os detalhes da mensagem. Neste exemplo, o servidor retransmissão mensagem #305 porque não recebeu uma confirmação do cliente:

![][9]

Quando criar a sessão de rastreio no Microsoft Message Analyzer, pode especificar os filtros para reduzir a quantidade de ruído no rastreio. No **capturar / rastreio** página onde é possível definir o rastreio, clique em de **configurar** ligação junto a **Microsoft-Windows-NDIS-PacketCapture**. A seguinte captura de ecrã mostra uma configuração de filtros de tráfego TCP para os endereços IP dos três serviços de armazenamento:

![][10]

Para obter mais informações sobre o rastreio de camada de ligação Microsoft mensagem analisador Local, consulte [fornecedor do Microsoft-PEF NDIS PacketCapture](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Apêndice 4: Com o Excel para ver métricas e registo de dados
Diversas ferramentas permitem-lhe transferir os dados das métricas do armazenamento do table storage do Azure num formato delimitado que torna mais fácil carregar os dados para o Excel para visualização e análise. Dados de registo de armazenamento do armazenamento de Blobs do Azure já estão no formato delimitado que pode carregar para o Excel. No entanto, terá de adicionar cabeçalhos de coluna apropriado com base nas informações no [formato de registo de análise do armazenamento](http://msdn.microsoft.com/library/azure/hh343259.aspx) e [armazenamento esquema da tabela de métricas de análise](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Para importar os dados de registo de armazenamento para o Excel, depois de transferir a partir do blob storage:

* No **dados** menu, clique em **do texto**.
* Procure o ficheiro de registo que pretende visualizar e clique em **importação**.
* No passo 1 de **Assistente de importação de texto**, selecione **delimitado**.

No passo 1 de **Assistente de importação de texto**, selecione **ponto e vírgula** como o único delimitador e escolha double-aspa como o **qualificador de texto**. Em seguida, clique em **concluir** e selecione onde pretende colocar os dados no seu livro.

### <a name="appendix-5"></a>Apêndice 5: Monitorizar com o Application Insights para Visual Studio Team Services
Também pode utilizar a funcionalidade do Application Insights para Visual Studio Team Services como parte da sua monitorização de desempenho e disponibilidade. Esta ferramenta pode:

* Certifique-se de que o serviço web está disponível e reativa. Se a aplicação é um web site ou uma aplicação de dispositivo que utiliza um serviço web, este pode testar o URL de cada alguns minutos a partir de localizações em todo o mundo e informá-lo se houver um problema.
* Diagnostique rapidamente quaisquer problemas de desempenho ou exceções no seu serviço web. Determinar se estão a ser transferidos da CPU ou de outros recursos, obter rastreios de pilha de exceções e facilmente procurar rastreios de registo. Se o desempenho da aplicação descerem abaixo limites aceitáveis, podemos enviar um e-mail. Pode monitorizar os serviços web .NET e Java.

Pode encontrar mais informações em [o que é Application Insights](../../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introdução]: #introduction
[Como este guia está organizado]: #how-this-guide-is-organized

[monitorização do seu serviço de armazenamento]: #monitoring-your-storage-service
[Monitorização de estado de funcionamento do serviço]: #monitoring-service-health
[Capacidade de monitorização]: #monitoring-capacity
[Monitorização de disponibilidade]: #monitoring-availability
[Monitorização do desempenho]: #monitoring-performance

[diagnosticar problemas de armazenamento]: #diagnosing-storage-issues
[Problemas de estado de funcionamento de serviço]: #service-health-issues
[Problemas de desempenho]: #performance-issues
[Erros de diagnóstico]: #diagnosing-errors
[Problemas de emulador de armazenamento]: #storage-emulator-issues
[Ferramentas de registo de armazenamento]: #storage-logging-tools
[Utilizar ferramentas de registo de rede]: #using-network-logging-tools

[rastreio ponto-a-ponto]: #end-to-end-tracing
[Correlacionar dados de registo]: #correlating-log-data
[ID do pedido de cliente]: #client-request-id
[ID do pedido de servidor]: #server-request-id
[Carimbos]: #timestamps

[orientações de resolução de problemas]: #troubleshooting-guidance
[métricas mostram AverageE2ELatency alta e baixa AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageE2ELatency baixa e uma AverageServerLatency baixa, mas o cliente está a ter latência elevada]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[As métricas apresentam uma AverageServerLatency alta]: #metrics-show-high-AverageServerLatency
[Ocorrem atrasos inesperados na entrega de mensagens numa fila]: #you-are-experiencing-unexpected-delays-in-message-delivery

[métricas mostram um aumento no PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Aumento transitório PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Aumento permanente PercentThrottlingError erro]: #permanent-increase-in-PercentThrottlingError
[métricas mostram um aumento no PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[As métricas apresentam um aumento do PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[O cliente está a receber mensagens HTTP 403 (proibido)]: #the-client-is-receiving-403-messages
[O cliente está a receber mensagens HTTP 404 (não for encontrado)]: #the-client-is-receiving-404-messages
[O cliente ou outro processo anteriormente eliminou o objeto]: #client-previously-deleted-the-object
[Um problema de autorização de assinatura de acesso partilhado (SAS)]: #SAS-authorization-issue
[No código JavaScript do lado do cliente não tem permissão para aceder ao objeto]: #JavaScript-code-does-not-have-permission
[Falha de rede]: #network-failure
[O cliente está a receber mensagens de HTTP 409 (conflito)]: #the-client-is-receiving-409-messages

[métricas mostram PercentSuccess baixa ou entradas de registo de análise tem operações com o estado de transação de ClientOtherErrors]: #metrics-show-low-percent-success
[Métricas de capacidade mostram um aumento inesperado na utilização da capacidade de armazenamento]: #capacity-metrics-show-an-unexpected-increase
[Estão a experienciar inesperados reinícios das máquinas virtuais que tenham um grande número de VHDs ligados]: #you-are-experiencing-unexpected-reboots
[O problema se for utilizar o emulador de armazenamento para desenvolvimento ou teste]: #your-issue-arises-from-using-the-storage-emulator
[A funcionalidade "X" não está a funcionar no emulador do storage]: #feature-X-is-not-working
[Erro "o valor para um dos cabeçalhos de HTTP não está no formato correto" quando utilizar o emulador de armazenamento]: #error-HTTP-header-not-correct-format
[Executar o emulador do storage é necessários privilégios administrativos]: #storage-emulator-requires-administrative-privileges
[Pode encontrar problemas ao instalar o Azure SDK para .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Tem um problema com um serviço de armazenamento diferente]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[apêndice 1: utilizar o Fiddler para capturar o tráfego HTTP e HTTPS]: #appendix-1
[apêndice 2: com o Wireshark para capturar o tráfego de rede]: #appendix-2
[apêndice 3: utilizar o Microsoft Message Analyzer para capturar o tráfego de rede]: #appendix-3
[Apêndice 4: Com o Excel para ver métricas e registo de dados]: #appendix-4
[Apêndice 5: Monitorizar com o Application Insights para Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
