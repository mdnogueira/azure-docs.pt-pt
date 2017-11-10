---
title: "Dashboard, monitorizar, a escala, configurar e as ligações híbridas nos BizTalk Services | Microsoft Docs"
description: Saiba mais sobre os controlos e monitorizar o desempenho dos BizTalk Services
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7a1815db-0de2-4274-8be0-198c1b077324
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 351809cd5f165a863dc02bfadf78fa59cbaabfd7
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Reveja os separadores do Dashboard, Monitor, Escala, Configurar e Ligação Híbrida

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Depois de criar o seu BizTalk Service e implementar a sua aplicação, pode alterar algumas das definições do BizTalk Service e monitorizar o desempenho da aplicação. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Esta ação abre uma nova janela com os seguintes separadores. Este tópico descreve os seguintes separadores.

## <a name="quickstart-quickstartquickstart"></a>(Guia de introdução![Início Rápido][Quickstart])
Consoante a edição de serviços do BizTalk, todas as opções apresentadas podem não estar disponíveis. 

<table border="1">
    <tr>
        <td><strong>Obtenha as ferramentas</strong></td>
        <td>Transferir o SDK dos BizTalk Services para instalar os modelos de projeto do Visual Studio no seu computador de desenvolvimento local. Estes modelos criam a <strong>BizTalk Services</strong> (bridge) e o <strong>BizTalk Service artefactos</strong> projetos (transformação) Visual Studio que são implementados para o seu BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Como devo começar a utilizar o SDK dos BizTalk Services do Azure </a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalar o SDK dos BizTalk Services do Azure</a> apresenta os passos para começar a utilizar.
        </td>
    </tr>
    <tr>
        <td><strong>Criar contratos de parceiros</strong></td>
        <td>Abre o BizTalk Services do Portal do Azure alojada no Azure, onde adicionar parceiros e criar X12, AS2 e nos contratos EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuração de componentes para EDI mensagens no Portal de serviços de BizTalk</a> apresenta os passos para começar a utilizar.
        </td>
    </tr>

<tr>
        <td><strong>Saiba mais sobre os BizTalk Services</strong></td>
        <td>Vá para o <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">learning center</a> para saber mais sobre os BizTalk Services do Azure.</td>
</tr>
</table>


Na barra de tarefas na parte inferior, pode:

<table border="1">

<tr>
<td><strong>Gerir</strong> a implementação da aplicação</td>
<td>Abre o portal de BizTalk Services do Azure. O Portal de serviços de BizTalk é o entrada como a configuração de EDI, incluindo a adição de parceiros e criar X12, AS2 e contratos EDIFACT.
<br/><br/>
Este é o mesmo que <strong>criar contratos de parceiros</strong> no <strong>início rápido</strong> separador.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuração de componentes para EDI mensagens no Portal de serviços de BizTalk</a> fornece mais informações sobre o Portal de serviços do BizTalk.</td>
</tr>

<tr>
<td><strong>Informações de ligação</strong> do espaço de nomes de controlo de acesso</td>
<td>Quando selecionar as informações da ligação, em seguida, o espaço de nomes de controlo de acesso, o emissor predefinido e a chave predefinida são apresentados. Pode copiar estes valores.
<br/><br/>
Pode também abrir o Portal de controlo de acesso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Criar um espaço de nomes do controlo de acesso</a> fornece mais informações sobre o Portal de controlo de acesso.</td>
</tr>

<tr>
<td><strong>Sincronizar chaves</strong> na conta de armazenamento</td>
<td>Quando cria uma Conta de armazenamento, são criadas automaticamente uma Chave Primária e uma Chave Secundária. Estas chaves de encriptação controlam o acesso à sua conta de armazenamento. O BizTalk Service utiliza automaticamente a chave primária. <strong>Sincronizar chaves</strong> permitir que os utilizadores alternar entre a chave primária e a chave secundária sem perturbar o BizTalk Service.
<br/><br/>
Por exemplo, pretende que o BizTalk Service para utilizar uma nova chave primária para a conta de armazenamento. Para efetuar este procedimento:
<br/><br/>
<ol>
<li>Selecione o seu BizTalk Service e selecione <strong>sincronizar chaves</strong>. Selecione a chave secundária. Ao fazê-lo, o BizTalk Service é iniciado utilizando a chave secundária.</li>
<li>Selecione a sua conta de armazenamento e voltar a gerar a chave primária. Lembre-se de que o BizTalk Service está a utilizar a chave secundária.</li>
<li>Selecione o seu BizTalk Service e selecione <strong>sincronizar chaves</strong>. Agora, selecione a chave primária. Esta é a nova chave primária que será gerada.</li>
<li>Selecione a sua conta de armazenamento e voltar a gerar a chave secundária.</li>
</ol>
<br/>
Este processo é denominado "chaves de rollover". O objetivo é permitir que os utilizadores alternar entre a chave primária e a chave secundária sem perturbar o BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> a aplicação</td>
<td>Quando seleciona eliminar, o BizTalk Service e todos os itens que foram implementados no mesmo são removidos.</td>
</tr>
</table>


## <a name="dashboard"></a>Dashboard
Consoante a edição de serviços do BizTalk, todas as opções apresentadas podem não estar disponíveis. 

Quando selecionar o nome do seu BizTalk Service, é apresentado o separador Dashboard. No Dashboard, pode:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Descrição geral da utilização: Mostra o número de utilizadas as ligações híbridas
Também apresenta a utilização de dados em GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico de métrico: Mostra uma lista fixa de métricas de desempenho
Estas métricas fornecem valores em tempo real sobre o estado de funcionamento do BizTalk Service. Também pode escolher o **relativo** ou **absoluto** valores e o intervalo de tempo **intervalo** das métricas que são apresentadas no gráfico. 

Para obter uma descrição estas métricas de desempenho, aceda a [as métricas disponíveis](#Metrics) neste tópico.

##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Leitura rápida: Apresenta uma lista as propriedades do BizTalk Service
<table border="1">

<tr>
<td><strong>Atualizar as credenciais da base de dados de controlo</strong></td>
<td>O nome de utilizador e palavra-passe utilizada para iniciar sessão na base de dados de controlo é alterado.</td>
</tr>
<tr>
<td><strong>Atualizar o certificado SSL</strong></td>
<td>Pode atualizar o BizTalk Service para utilizar um certificado SSL diferentes. Um certificado SSL autoassinado é criado automaticamente quando é <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">criar o BizTalk Service</a>.</td>
</tr>
<tr>
<td><strong>Transferir o certificado</strong></td>
<td>Pode transferir o certificado SSL utilizado pelo BizTalk Service para um computador local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Apresenta o estado atual do seu BizTalk Service. Consulte <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk Services: gráfico de estado do serviço</a>. </td>
</tr>
<tr>
<td><strong>URL do serviço</strong></td>
<td>O URL para o seu BizTalk Service. Este é o mesmo que o <strong>URL do domínio</strong> introduzido quando é criado o seu BizTalk Service.</td>
</tr>
<tr>
<td><strong>Endereço IP Virtual público (VIP)</strong></td>
<td>O endereço IP atribuído ao seu BizTalk Service. É utilizado para todos os pontos finais de entrada e se o endereço de origem do tráfego de saída. Este endereço IP pertence ao seu BizTalk Service, desde que é criado. Se eliminar o BizTalk Service, o endereço IP é atribuído a mais nenhum BizTalk Service.</td>
</tr>
<tr>
<td><strong>Espaço de nomes do ACS</strong></td>
<td>Autentica com o BizTalk Service.</td>
</tr>
<tr>
<td><strong>Edição</strong></td>
<td>Apresenta uma lista a edição introduzido quando é criado o BizTalk Service.</td>
</tr>
<tr>
<td><strong>Localização</strong></td>
<td>Apresenta a região geográfica que aloja o seu BizTalk Service.</td>
</tr>
<tr>
<td><strong>Criado</strong></td>
<td>Apresenta a data e hora em que foi criado o BizTalk Service.</td>
</tr>
<tr>
<td><strong>Base de dados de controlo</strong></td>
<td>O nome de SQL Database do Azure que armazena as tabelas de controlo utilizadas pelo BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos Explained</a> fornece detalhes sobre a base de dados de controlo.</td>
</tr>
<tr>
<td><strong>Armazenamento de monitorização/arquivo</strong></td>
<td>O nome de conta do Storage do Azure que armazena o resultado da monitorização do seu BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisitos Explained</a> fornece detalhes sobre a conta de armazenamento.</td>
</tr>
<tr>
<td><strong>Nome da subscrição</strong></td>
<td>Apresenta uma lista a subscrição que aloja o seu BizTalk Service. A subscrição é regida pelas acesso.</td>
</tr>
<tr>
<td><strong>ID de subscrição</strong></td>
<td>Quando é criada uma subscrição, é gerado automaticamente um ID de subscrição. Quando utilizar REST APIs, poderá ter de introduzir o ID de subscrição.</td>
</tr>
</table>

[BizTalk Services: Aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280) apresenta os passos para criar um BizTalk Service.

##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gerir informações de ligação, chaves de sincronização e eliminar na barra de tarefas:
<table border="1">

<tr>
<td><strong>Gerir</strong> a implementação da aplicação</td>
<td>Abre o Portal de serviços do BizTalk do Azure. O Portal de serviços de BizTalk é o entrada como a configuração de EDI, incluindo a adição de parceiros e criar X12, AS2 e contratos EDIFACT.
<br/><br/>
Este é o mesmo que <strong>criar contratos de parceiros</strong> no <strong>início rápido</strong> separador.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configuração de componentes para EDI mensagens no Portal de serviços de BizTalk</a> fornece mais informações sobre o Portal de serviços do BizTalk.</td>
</tr>
<tr>
<td><strong>Informações de ligação</strong> do espaço de nomes de controlo de acesso</td>
<td>Apresenta o espaço de nomes de controlo de acesso, o emissor predefinido e valores de chave predefinida; que podem ser copiados.
<br/><br/>
Pode também abrir o Portal de controlo de acesso. Este Portal de controlo de acesso é igual a utilizar a opção de Active Directory no painel de navegação esquerdo.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerir o espaço de nomes do ACS</a> fornece mais informações sobre o Portal de controlo de acesso.</td>
</tr>
<tr>
<td><strong>Sincronizar chaves</strong> na conta de armazenamento</td>
<td>Quando cria uma Conta de armazenamento, são criadas automaticamente uma Chave Primária e uma Chave Secundária. Estas chaves de encriptação controlam o acesso à sua conta de armazenamento. O BizTalk Service utiliza automaticamente a chave primária. <strong>Sincronizar chaves</strong> permitir que os utilizadores alternar entre a chave primária e a chave secundária sem perturbar o BizTalk Service.
<br/><br/>
Por exemplo, pretende que o BizTalk Service para utilizar uma nova chave primária para a conta de armazenamento. Para efetuar este procedimento:
<br/><br/>
<ol>
<li>Selecione o seu BizTalk Service e selecione <strong>sincronizar chaves</strong>. Selecione a chave secundária. Ao fazê-lo, o BizTalk Service é iniciado utilizando a chave secundária.</li>
<li>Selecione a sua conta de armazenamento e voltar a gerar a chave primária. Lembre-se de que o BizTalk Service está a utilizar a chave secundária.</li>
<li>Selecione o seu BizTalk Service e selecione <strong>sincronizar chaves</strong>. Agora, selecione a chave primária. Esta é a nova chave primária que será gerada.</li>
<li>Selecione a sua conta de armazenamento e voltar a gerar a chave secundária.</li>
</ol>
<br/>
Este processo é denominado "chaves de rollover". O objetivo é permitir que os utilizadores alternar entre a chave primária e a chave secundária sem perturbar o BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> a aplicação</td>
<td>O BizTalk Service e todos os itens que foram implementados no mesmo são removidos.</td>
</tr>
</table>


## <a name="monitor"></a>Monitorizar
Não é aplicável a edição gratuita.

Quando selecionar o nome do seu BizTalk Service, o separador Monitor está disponível e apresenta o seguinte:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Gráfico de métrico: Apresenta as métricas de desempenho selecionados
Estas métricas fornecem valores em tempo real sobre o estado de funcionamento do BizTalk Service. Pode escolher as métricas de desempenho são apresentadas. Um máximo de seis métricas de desempenho pode ser apresentado em simultâneo. 

Também pode escolher o **relativo** ou **absoluto** valores e o intervalo de tempo **intervalo** das métricas que são apresentadas. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para remover ou apresentação das métricas no gráfico de:
1. Selecione o **Monitor** separador.
2. Selecione **adicionar métricas** na barra de tarefas:  
   ![Selecione adicionar métricas][AddMetrics]
3. Verifique as métricas de desempenho que pretende apresentar.
4. Selecione a marca de verificação para voltar para o **Monitor** separador.
5. Selecione o círculo junto a métrica para apresentar o valor que métrica no gráfico.  
   
    Por exemplo, o **utilização da CPU** métrica fica a cinzento; o resultado não é apresentado no gráfico de:  
   ![Métrica de utilização da CPU fica a cinzento][GrayedMetric]  
   
    Selecione o desativada saída círculo para ativar o **utilização da CPU** métrica para apresentar os respetivos resultados no gráfico de:  
   ![Métrica de utilização da CPU está ativada][EnabledMetric]
6. Para remover uma métrica de gráfico a apresentar e uma lista, selecione **eliminar métrica** na barra de tarefas. Para adicionar a métrica de cópia de segurança à lista, selecione **adicionar métricas** na barra de tarefas, verifique a métrica e selecione a marca de verificação para voltar para o **Monitor** separador. Selecione o desativada saída círculo para ativar a métrica.

## <a name="Metrics"></a>Métricas disponíveis
Contadores/métricas de desempenho seguintes estão disponíveis:

<table border="1">

<tr>
<td><strong>Latência de RountdTrip</strong></td>
<td>Apresenta o tempo médio decorrido em milissegundos (ms) para processar uma mensagem desde o momento em que a mensagem é recebida até que a mensagem está totalmente processada pelo serviço BizTalk em todos os pontes. Apenas as mensagens processadas com êxito são contadas.<br/><br/>
Quando ocorrem os seguintes eventos, é criado um timestamp:
<ul>
<li>Mensagem introduz o gateway</li>
<li>Mensagem é encaminhada para o destino</li>
<li>É recebida uma resposta de destino</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>
Esta métrica apresenta o resultado do cálculo seguinte:
<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [mensagem introduz o gateway]</td>
</tr>
<tr>
<td><strong>Falhas na origem</strong></td>
<td>Apresenta o número total de mensagens em fila que falharam pelo BizTalk Service quando extrair as mensagens dos pontos finais de origem.</td>
</tr>
<tr>
<td><strong>Utilização da CPU</strong></td>
<td>Apresenta a % de média de tempo do processador de todas as instâncias de função.</td>
</tr>
<tr>
<td><strong>Latência de processamento</strong></td>
<td>Apresenta o tempo médio que em milissegundos (ms) para processar uma mensagem o BizTalk Service em todos os pontes, excluindo o tempo despendido no destinos. Apenas as mensagens processadas com êxito são contadas.<br/><br/>
Quando cada um dos seguintes eventos ocorrem, é criado um timestamp:

<ul>
<li>Mensagem introduz o gateway</li>
<li>Mensagem é encaminhada para o destino</li>
<li>É recebida uma resposta de destino</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>Esta métrica apresenta o resultado do cálculo seguinte:<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [introduz o gateway de mensagem] - [é recebida uma resposta de destino] + [mensagem é encaminhada para o destino]</td>
</tr>
<tr>
<td><strong>Falhas de processo</strong></td>
<td>Apresenta o número total de mensagens em fila que falharam durante o processamento pelo BizTalk Service em todos os pontes dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens enviadas</strong></td>
<td>Apresenta o número total de mensagens enviadas pelo BizTalk Service, nas pontes todos os dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem enviada de um pipeline atinge o destino de rota. Esta métrica não indica que uma mensagem é processou com êxito.<br/><br/>
Num cenário de pedido-resposta, a métrica é incrementada quando o destino de rota envia uma confirmação de receção novamente para o pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens recebidas</strong></td>
<td>Apresenta o número total de mensagens recebidas pelo BizTalk Service, nas pontes todos os dentro de um intervalo de tempo. Esta métrica é incrementada quando uma nova mensagem é recebida pelo pipeline.</td>
</tr>
<tr>
<td><strong>Mensagens no processo</strong></td>
<td>Apresenta o número total de mensagens em processamento pelo BizTalk Service dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Processada de mensagens</strong></td>
<td>Apresenta o número total de mensagens em fila processada com êxito pelo BizTalk Service em todos os pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem é recebida com êxito pelo pipeline e encaminhada com êxito para o destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
No separador de escala, pode adicionar ou subtrair o número de unidades utilizados pelo BizTalk Service. Por predefinição, não há uma unidade configurada. Unidades adicionais podem ser adicionadas ao dimensionar o seu BizTalk Service. Quando aumenta a escala, são aumentar o débito. A quantidade de recursos também aumenta, incluindo pontes implementados, contratos, ligações de LOB e poder de processamento. Por exemplo, aumenta a escala de 1 unidade a 2 unidades. Nesta situação, pode implementar duplicar o número de pontes, faça duplo os contratos, duplicar as ligações de LOB e duplo a capacidade de processamento.

Algumas edições dos BizTalk oferece uma opção de dimensionamento. Nesta situação, é permitida uma unidade. Para determinar quantos unidades sua edição pode ser ampliada, consulte [BizTalk Services: gráfico de edições](biztalk-editions-feature-chart.md).

Aumento do número de unidades pode afetar o preço. Se aumentar as unidades, selecionar **guardar** apresenta uma mensagem que indica se a faturação é afetada. Em seguida, escolha de continuar. Quando aumenta o número de unidades, as alterações de estado do BizTalk Service do Active Directory para atualização. No estado de atualização, o seu BizTalk Service continua a ser executado.

[BizTalk Services: Gráfico de edições](biztalk-editions-feature-chart.md) define uma "unidade".

## <a name="configure"></a>Configurar
Não é aplicável a ligações híbridas.

Define o estado de cópia de segurança como None ou automático. Se estiver definida como None, não existem cópias de segurança são criadas automaticamente. Quando definido como automático, configurar a localização de cópia de segurança, a frequência da cópia de segurança e o intervalo de tempo para manter os ficheiros de cópia de segurança. 

[BizTalk Services: Cópia de segurança e restaurar](biztalk-backup-restore.md) fornece os detalhes. 

## <a name="HybridConnections"></a>Ligações híbridas
As ligações híbridas ligar uma aplicação do Azure, como as Web Apps ou Mobile Apps no App Service do Azure, a um recurso no local que utiliza uma porta TCP estática, como o SQL Server, MySQL, APIs da Web HTTP e a maioria dos serviços Web personalizados. As ligações híbridas são geridas na BizTalk Services.

Para criar ou gerir ligações híbridas no BizTalk Services do Azure, consulte [ligações híbridas](integration-hybrid-connection-overview.md).

## <a name="next"></a>Seguinte
Agora que está familiarizado com os diferentes separadores, pode saber mais sobre as funcionalidades de BizTalk Services do Azure:

* [Serviços BizTalk: limitação](biztalk-throttling-thresholds.md)  
* [Serviços BizTalk: Nome e Chave do Emissor](biztalk-issuer-name-issuer-key.md)  
* [Serviços BizTalk: Cópia de segurança e Restauro](biztalk-backup-restore.md)

## <a name="see-also"></a>Veja Também
* [Ligações Híbridas](integration-hybrid-connection-overview.md)  
* [Os BizTalk Services: Programador, básicas, Standard e Premium gráfico de edições](biztalk-editions-feature-chart.md)  
* [Os BizTalk Services: aprovisionamento](biztalk-provision-services.md)  
* [BizTalk Services: Gráfico de estado do serviço BizTalk](biztalk-service-state-chart.md)  
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[Quickstart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png

