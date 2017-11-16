---
title: "O que é informações de séries de tempo do Azure? | Microsoft Docs"
description: "Introdução às informações de séries de tempo do Azure, um novo serviço de análise de dados de séries de tempo e soluções de IoT."
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 95cb26ada6f8ea39bc1a437a755f80ee7ddb7698
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-time-series-insights"></a>O que é informações de séries de tempo do Azure?

Informações de séries de tempo foi construída para armazenar, visualizar e consultar grandes quantidades de dados de séries de tempo, tal como a gerado por dispositivos de IoT.  Se pretender armazenar, gerir, consultar ou visualizar dados de séries de tempo na nuvem, informações de séries de tempo é provável que direito para si.  

Se está a criar uma aplicação, consumo interno do ou para clientes externos utilizar, informações de séries de tempo pode ser utilizadas como um back-end para indexação, armazenar e agregar dados de séries de tempo.  Pode criar uma experiência de utilizador e de visualização personalizada na parte superior.  Informações de séries de tempo expõe as APIs REST do consulta para ativar este cenário.  

Se não souber se os dados estão a série de tempo, eis o que deve conhecer.  Dados de séries de tempo representa como um recurso ou processo alterações ao longo do tempo.  É exclusivo no que tem um carimbo e a hora é mais significativa, como um eixo.  Dados de séries de tempo, normalmente, chega por ordem de tempo e, normalmente, são tratados como uma inserção em vez de uma atualização para a base de dados.  Uma vez Insights de séries de tempo de captura e armazena cada novo evento como uma linha, alteração é medida ao longo do tempo, permitindo-lhe parecer com versões anteriores e prever a alteração futura.  Em grandes volumes, armazenar, indexação, consultar, analisar e visualizar dados de séries de tempo podem ser um desafio.  

## <a name="primary-scenarios"></a>Cenários principais

- Armazenar dados de séries de tempo de uma forma escalável.  
  - O núcleo, informações de séries de tempo tem uma base de dados concebida com dados de séries de tempo em mente.  Porque é totalmente gerido e escalável, informações de séries de tempo processa o trabalho de armazenar e gerir os eventos.

- Junto de exploração de dados em tempo real.  
  - Informações de séries de tempo fornece um Explorador que visualiza todos os dados de transmissão em fluxo para um ambiente.  Pouco tempo depois de ligar uma origem de evento, os dados de eventos podem ser visualizados, explored e consultado dentro das informações de séries de tempo.  Os dados são úteis para validar se um dispositivo é a emitir dados conforme esperado e a monitorização um recurso de IoT para o estado de funcionamento, produtividade e eficácia geral.  

- Deteção de análise e anomalias causa raiz.
  - Informações de séries de tempo tem ferramentas como padrões e vistas de perspetiva para realizar e guardar a análise da causa de raiz com vários passos.  Além disso, informações de séries de tempo funciona em conjunto com alertas serviços como o Azure Stream Analytics, pelo que os alertas e anomalias detetadas podem ser visualizadas no quase em tempo real no Explorador de informações de séries de tempo.  

- Uma vista global de dados de séries de tempo de transmissão em fluxo a partir de localizações diferentes para comparação várias asset/site.
  - Pode ligar a várias origens de eventos para um ambiente de informações de séries de tempo.  Isto significa que dados de transmissão em fluxo em várias, localizações diferentes podem ser visualizados em conjunto em praticamente em tempo real.  Os utilizadores podem tirar partido desta visibilidade para partilhar os dados com líderes de negócio e para permitir a colaboração melhor com especialistas de domínio que pode aplicar-se os seus conhecimentos para ajudar a resolver problemas, aplique as melhores práticas e partilhar learnings.

- Criar uma aplicação de cliente em cima das informações de séries de tempo. 
  - Informações de séries de tempo expõe as APIs de consulta REST, permitindo-lhe criar aplicações que utilizam dados da série de tempo.

## <a name="capabilities"></a>Capacidades

- **Comece a trabalhar rapidamente:** informações de séries de tempo do Azure não necessita de nenhum preparação de dados prévio. Ligar a milhões de eventos no seu IoT Hub do Azure ou o Hub de eventos em minutos. Assim que estiver ligado, visualizar e interagir com dados de sensores rapidamente validar as suas soluções de IoT. Pode interagir com os seus dados sem escrever código.
Não há nenhum novo idioma para saber; Informações de séries de tempo fornece uma superfície de consulta granular e texto livre para utilizadores avançados e ponto e clique em exploração.
- **Quase em tempo real insights:** Insights de séries de tempo pode ingerir milhões de eventos de sensor por dia, com uma latência de um minuto. Informações de séries de tempo ajuda a obter informações sobre os seus dados de sensores, ajudando-spot tendências e anomalias, realize as estatísticas da causa de raiz e evitar períodos de indisponibilidade dispendiosos. Ao ativar a correlação cruzada entre os dados em tempo real e os dados históricos, o Time Series Insights ajuda-o a revelar tendências ocultas nos dados.
- **Criar soluções personalizadas:** dados incorporar informações de séries de tempo do Azure para as suas aplicações existentes, ou criar novas soluções personalizadas com APIs de REST de informações de série de tempo. Crie vistas personalizadas, pode partilhar para outras pessoas explorar as suas informações.
- **Escalabilidade:** Insights de séries de tempo foi concebido para suportar IoT à escala. Pode entrada de 1 milhões para 100 milhões de eventos por dia, com uma retenção predefinido span de 31 dias. Pode visualizar e analisar dados em direto fluxos no quase em tempo real, juntamente com os dados históricos. Daqui para a frente, irão aumentar as taxas de entrada e de retenção para acomodar escala empresarial.

## <a name="getting-started"></a>Introdução
Introdução demora menos de 5 minutos. 

1.  Para obter iniciado, aprovisionar um ambiente de informações de séries de tempo no portal do Azure. 
2.  Ligar uma origem de evento, como um IoT Hub do Azure ou o Hub de eventos.  
3.  Carregar os dados de referência (não é um serviço adicional).
4.  Ver os seus dados em minutos com o Explorador de informações de séries de tempo.

## <a name="time-series-insights-explorer"></a>Explorador de Insights de série de tempo
Este diagrama mostra um exemplo de séries de tempo de dados do insights visualizados através do Explorador:! [Explorador de Insights de série de tempo] (media/time-series-insights-explorer/explorer4.png)


## <a name="next-steps"></a>Passos seguintes
 - [Explorar a utilizar o Explorador de informações de séries de tempo num ambiente de demonstração](./time-series-quickstart.md)
 - [Planear o seu ambiente de informações de séries de tempo](time-series-insights-environment-planning.md)

