---
title: "Demonstração personalizada da solução Mapa do Serviço | Microsoft Docs"
description: "O Mapa de Serviço é uma solução no Operations Management Suite (OMS) que deteta componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços.  Esta é uma demonstração personalizada que explica o funcionamento do Mapa de Serviço para identificar e diagnosticar um problema simulado numa aplicação Web."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>Demonstração personalizada do Operations Management Suite (OMS) - Mapa de Serviço
Esta é uma demonstração personalizada que explica o funcionamento da [solução Mapa de Serviço](operations-management-suite-service-map.md) no Operations Management Suite (OMS) para identificar e diagnosticar um problema simulado numa aplicação Web.  O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços.  Também consolida os dados recolhidos por outros serviços do OMS, para o ajudar a analisar o desempenho e a identificar problemas.  Também vai utilizar [pesquisas de registos no Log Analytics](../log-analytics/log-analytics-log-searches.md) para desagregar dados recolhidos, de modo a identificar a causa do problema.


## <a name="scenario-description"></a>Descrição do cenário
Acabou de receber uma notificação de que a aplicação de Portal de Cliente ACME está a ter problemas de desempenho.  As únicas informações que tem são que estes problemas começaram hoje pelas 4:00 PST.  Não tem a certeza de todos os componentes que o portal depende, exceto um conjunto de servidores Web.  

## <a name="components-and-features-used"></a>Componentes e funcionalidades utilizados
- [Solução Mapa de Serviço](operations-management-suite-service-map.md)
- [Pesquisa de registos do Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>Instruções

### <a name="1-connect-to-the-oms-experience-center"></a>1. Ligar ao OMS Experience Center
Estas instruções utilizam o [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/), que proporciona um ambiente de OMS completo com dados de exemplo. Comece por seguir esta ligação, indique as informações e, em seguida, selecione o cenário **Informações e Análise de Dados** .


### <a name="2-start-service-map"></a>2. Iniciar Mapa de Serviço
Clique no mosaico **Mapa de Serviço** para iniciar a solução Mapa de Serviço.

![Mosaico do Mapa de Serviço](media/operations-management-suite-walkthrough-servicemap/tile.png)

A consola do Mapa de Serviço é apresentada.  No painel da esquerda está uma lista de computadores no seu ambiente com o agente do Mapa de Serviço instalado.  Irá selecionar o computador que pretende ver a partir desta lista.

![Lista de computadores](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Ver computador
Sabemos que os servidores Web se chamam AcmeWFE001 e AcmeWFE002, pelo que este parece ser um ponto de partida razoável.  Clique no **AcmeWFE001**.  Esta ação mostra o mapa de AcmeWFE001 e todas as respetivas dependências.  Pode ver os processos que estão em execução no computador selecionado e com que serviços externos eles comunicam.

![Servidor Web](media/operations-management-suite-walkthrough-servicemap/web-server.png)

Estamos preocupados com o desempenho da nossa aplicação Web, por isso clique no processo **AcmeAppPool (Conjunto de Aplicações do IIS)**.  Isto apresenta os detalhes deste processo e destaca as respetivas dependências.  

![Conjunto de Aplicações](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Alterar o período de tempo

Sabemos que o problema começou às 4:00, por isso vamos ver o que estava a acontecer nesse momento. Clique em **Intervalo de Tempo** e altere a hora para 4:00 PST (mantenha a data atual e ajuste o fuso horário local) com uma duração de 20 minutos.

![Selecionador de hora](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Ver alerta

Vemos agora que a dependência **acmetomcat** tem um alerta apresentado, que é o nosso potencial problema.  Clique no ícone de alerta no **acmetomcat**, para mostrar os detalhes do mesmo.  Podemos ver que temos utilização crítica da CPU e podemos expandi-la para obter mais detalhes.  Isto é provavelmente o que está a causar a lentidão do nosso desempenho. 

![Alerta](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Ver desempenho

Vamos olhar de forma mais atenta para **acmetomcat**.  Clique no canto superior direito de **acmetomcat** e selecione **Carregar Mapa do Servidor**, para mostrar os detalhes e as dependências desta máquina. Podemos, em seguida, ver um pouco mais dos contadores de desempenho para verificar a nossa suspeita.  Selecione o separador **Desempenho** para apresentar os [contadores de desempenho recolhidos pelo Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) ao longo do intervalo de tempo.  Podemos ver que estamos a obter picos periódicos no processador e na memória.

![Desempenho](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Ver monitorização de alterações
Vamos ver se conseguimos perceber o que poderá ter causado esta utilização elevada.  Clique no separador **Resumo**.  São disponibilizadas informações que o OMS recolheu do computador, como ligações falhadas, alertas críticos e alterações de software.  As secções com informações recentes interessantes já deverão estar expandidas e pode expandir outras secções para inspecionar as informações que contêm.


Se a **Monitorização de Alterações** ainda não estiver aberta, então expanda-a.  São apresentadas as informações recolhidas pela [solução Monitorização de Alterações](../log-analytics/log-analytics-change-tracking.md).  Parece que ocorreu uma alteração de software efetuada durante este período de tempo.  Clique em **Software** para obter detalhes.  Um processo de cópia de segurança foi adicionado à máquina imediatamente após as 4:00, pelo que este parece ser o motivo do consumo excessivo de recursos.

![Monitorização de alterações](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Ver detalhes na Pesquisa de Registos
Podemos verificar de forma mais aprofundada ao observar as informações de desempenho detalhadas recolhidas no repositório do Log Analytics.  Clique no separador **Alertas** novamente e, em seguida, em um dos alertas **CPU elevada**.  Clique em **Mostrar na Pesquisa de Registos**.  Esta ação abre a janela de Pesquisa de Registos onde pode realizar [pesquisas de registos](../log-analytics/log-analytics-log-searches.md) relativamente a todos os dados armazenados no repositório.  O Mapa de Serviço já preencheu uma consulta para obter o alerta que estamos interessados.  

![Pesquisas de registos](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Abrir pesquisa guardada
Vamos ver se conseguimos obter mais detalhes na recolha de desempenho que gerou este alerta e verificar a nossa suspeita de que os problemas estão a ser causados por esse processo de cópia de segurança.  Altere o intervalo de tempo para **6 horas**.  Em seguida, clique em **Favoritos** e desloque para baixo para as pesquisas guardadas do **Mapa de Serviço**.  Estas são as consultas que criámos especificamente para esta análise.  Clique em **Cinco processos principais pela CPU para acmetomcat**.

![Pesquisa guardada](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


Esta consulta devolve uma lista dos cinco processos principais que consomem o processador no **acmetomcat**.  Pode inspecionar a consulta para obter uma introdução do idioma de consulta utilizado para as pesquisas de registos.  Se estava interessado nos processos noutros computadores, pode modificar a consulta para obter essas informações.

Neste caso, podemos ver que o processo de cópia de segurança está a consumir consistentemente cerca de 60% da CPU do servidor da aplicação.  É bastante óbvio que este novo processo é responsável pelo nosso problema de desempenho.  A nossa solução seria obviamente remover este novo software de cópia de segurança do servidor de aplicação.  Podíamos tirar partido da Configuração de Estado Pretendido (DSC) gerida através da Automatização do Azure, para definir políticas que se certificam que este processo nunca é executado nestes sistemas críticos.


## <a name="summary-points"></a>Pontos de resumo
- O [Mapa de Serviço](operations-management-suite-service-map.md) fornece-lhe uma vista da sua aplicação completa, mesmo se não conhecer todos os respetivos servidores e as dependências.
- O Mapa de Serviço apresenta dados recolhidos por outras soluções do OMS, para o ajudar a identificar problemas com a sua aplicação e a respetiva infraestrutura subjacente.
- As [Pesquisas de Registos](../log-analytics/log-analytics-log-searches.md) permitem-lhe desagregar dados específicos recolhidos no repositório do Log Analytics.    

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [Mapa de Serviço](operations-management-suite-service-map.md).
- [Implemente e configure](operations-management-suite-service-map-configure.md) o Mapa de Serviço.
- Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md) que é necessário para o Mapa de Serviço e armazena dados operacionais armazenados por agentes.