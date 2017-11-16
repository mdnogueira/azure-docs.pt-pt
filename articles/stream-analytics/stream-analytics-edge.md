---
title: "O Azure Stream Analytics no limite de IoT (pré-visualização)"
description: "Criar tarefas de limite no Azure Stream Analytics e implementá-las runnning de dispositivos do Azure IoT Edge."
keywords: fluxo de dados, iot, edge
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/15/2017
ms.author: jeanb
ms.openlocfilehash: 6e94758581bd510e58a709a53e30c11a5c1f1b62
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>O Azure Stream Analytics no limite de IoT (pré-visualização)

> [!IMPORTANT]
> Esta funcionalidade está em pré-visualização. Não é recomendada a utilização em produção.
 
Azure Stream Analytics (ASA) no limite de IoT proporciona aos programadores implementar quase em tempo real intelligence analítico próximo dispositivos IoT para que eles podem desbloquear pleno partido dos dados gerados pelo dispositivo. Concebido para os clientes que necessitam de latência baixa, a resiliência, uma utilização eficiente da largura de banda e de conformidade, as empresas podem agora implementar a lógica de controlo próximo as operações industriais e complementar a análise de macrodados efetuada na nuvem.  
O Azure Stream Analytics no limite de IoT é executado dentro do [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework e a implementação e gestão de tarefas do ASA podem ser realizadas utilizar o IoT Hub depois da tarefa é criada no ASA.
Esta funcionalidade está em pré-visualização, se tiver qualquer pergunta ou comentários, pode utilizar [este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) contactar a equipa de produto. 

## <a name="scenarios"></a>Cenários
![Diagrama de alto nível](media/stream-analytics-edge/ASAedge_highlevel.png) Eis alguns cenários típicos para as quais ASA em execução na margem é particularmente interessante:
* **Comando de latência baixa e controlo**: por exemplo, segurança sistemas são necessários para responder a dados operacionais, com latência ultra-baixa de fabrico. Com ASA no limite do IoT, pode analisar dados de sensores em quase em tempo real e comandos de problema quando detetar anomalias para parar uma máquina ou acionador alertas.
*   **Limitada a conectividade à nuvem**: missão sistemas críticos, como equipamento de extração remoto, vessels ligados ou desagregação offshore, tem de analisar e reagir aos dados, mesmo quando a conectividade de nuvem é intermitente. Com ASA, a lógica de transmissão em fluxo é executado independentemente a conectividade de rede e pode escolher que enviar para a nuvem para processamento adicional ou o armazenamento.
* **Largura de banda limitada**: O volume de dados produzidos por motores de jet ou carros ligados podem ser tão grandes que dados tem de ser filtrados ou pré-processados antes de a enviar para a nuvem. Utilizar ASA, pode filtrar ou agregar os dados que têm de ser enviadas para a nuvem.
* **Conformidade**: a conformidade regulamentar pode necessitar de alguns dados localmente anónimas ou agregado antes de a ser enviados para a nuvem. Com ASA, 

## <a name="edge-jobs-in-azure-stream-analytics"></a>Limite as tarefas do Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>O que é uma tarefa "edge"?

Executar tarefas do ASA Edge como módulos dentro [tempo de execução do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). São compostos por duas partes:
1.  Uma parte de nuvem que é responsável pela definição de tarefa: os utilizadores definir entradas, saída, consulta e outras definições (eventos fora de ordem, etc.) na nuvem.
2.  ASA no módulo de limite de IoT que é executada localmente. O motor ASA complexas processamento de eventos não contém e recebe a definição de tarefa a partir da nuvem. 

ASA utiliza IoT Hub para implementar as tarefas de edge para dispositivos. Obter mais informações sobre [implementação de limite de IoT que pode ser vista aqui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Tarefa de limite](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Instruções de instalação
Os passos de alto nível são descritos na seguinte tabela. São fornecidos mais detalhes nas seguintes secções.
|      |Passo   | Local     | Notas   |
| ---   | ---   | ---       |  ---      |
| 1   | **Criar uma tarefa de limite do ASA**   | Portal do Azure      |  Criar uma nova tarefa, selecione **Edge** como **ambiente de alojamento**. <br> Estas tarefas são criado/geridos a partir da nuvem e execute os seus próprios dispositivos de limite de IoT.     |
| 2   | **Criar um contentor de armazenamento**   | Portal do Azure       | Os contentores de armazenamento são utilizados para guardar a definição de tarefa onde possam ser acedidos pelos seus dispositivos de IoT. <br>  Pode reutilizar qualquer contentor de armazenamento existente.     |
| 3   | **Configurar o ambiente de limite de IoT no seu dispositivo (s)**   | Dispositivos      | Instruções para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Implementar ASA nos seus dispositivos de IoT Edge**   | Portal do Azure      |  Definição de tarefa do ASA é exportada para o contentor de armazenamento que criou anteriormente.       |
Pode seguir [neste tutorial passo a passo](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) para implementar o seu primeiro emprego ASA no limite de IoT.

#### <a name="create-an-asa-edge-job"></a>Criar uma tarefa do ASA Edge
1. A partir do portal do Azure, crie uma nova "tarefa de Stream Analytics". [Ligação direta para criar uma nova tarefa ASA aqui](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).
2. No ecrã da criação, selecione **Edge** como **ambiente de alojamento** (ver a imagem seguinte) ![criação da tarefa](media/stream-analytics-edge/ASAEdge_create.png)
3. Definição de tarefa
    1. **Definir a entrada Stream(s)**. Defina um ou vários fluxos de entrada para a tarefa.
    2. Defina os dados de referência (opcionais).
    3. **Definir saída Stream(s)**. Defina um ou vários fluxos saídas a tarefa. 
    4. **Definir a consulta**. Defina a consulta do ASA na nuvem utilizando o editor de inline. O compilador verifica automaticamente, a sintaxe ativada para o limite do ASA. Também pode testar a sua consulta através do carregamento de dados de exemplo. 
4. Configurar as definições opcionais
    1. **Ordenação de evento**. Pode configurar uma política de fora de ordem no portal. Está disponível documentação [aqui](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Região**. Defina o formato de internalization.


#### <a name="create-a-storage-container"></a>Criar um contentor de armazenamento
Um contentor de armazenamento é necessário para exportar a consulta do ASA compilado e a configuração da tarefa. É utilizado para configurar a imagem do ASA Docker com a sua consulta específica. 
1. Siga [estas instruções](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) para criar uma conta do storage a partir do portal do Azure. Pode manter todas as opções predefinidas para utilizar esta conta com ASA.
2. A conta de armazenamento acabada de criar, crie um contentor de armazenamento de BLOBs:
    1. Clique em "Blobs", em seguida, "+ contentor". 
    2. Introduza um nome e manter o contentor como "Privado"


> [!Note]
> Quando uma implementação é criada, ASA exporta a definição de tarefa para um contentor de armazenamento. Esta definição de tarefa permaneça a mesma durante a duração de uma implementação. Como consequence, se pretender atualizar uma tarefa em execução na extremidade, terá de editar a tarefa no ASA e, em seguida, crie uma nova implementação do IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurar o ambiente de limite de IoT no seu dispositivo (s)
Tarefas de limite podem ser implementadas em dispositivos com o Azure IoT Edge.
Para tal, tem de seguir estes passos:
- Criar um Hub Iot;
- Instalar o runtime do Docker e limite de IoT nos seus dispositivos de limite;
- Configurar os seus dispositivos como "Dispositivos da IoT Edge" no IoT Hub.

Estes passos são descritos na documentação do limite de IoT para [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) ou [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Implementação do ASA nos seus dispositivos de IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Adicionar ASA para a implementação
- No portal do Azure, abra o IoT Hub, navegue para o Explorador de limite de IoT e abrir o painel do dispositivo.
- Selecione **definir módulos**, em seguida, selecione **módulo Edge IoT do serviço de importação do Azure**.
- Selecione a subscrição e a tarefa de limite de ASA que criou. Em seguida, selecione a sua conta de armazenamento. Clique em Guardar.
![Adicionar módulo ASA na sua implementação](media/stream-analytics-edge/set_module.png)


> [!Note]
> Durante este passo, ASA solicita acesso ao contentor de armazenamento selecionado e, em seguida, cria uma pasta denominada "EdgeJobs". Para cada implementação é criada uma nova subpasta na pasta "EdgeJobs".
> Para implementar o seu trabalho para dispositivos de limite, ASA cria uma assinatura de acesso partilhado (SAS) para o ficheiro de definição de tarefa. A chave SAS em segurança é transmitida para os dispositivos de limite de IoT a utilizar o dispositivo duplo. A expiração desta chave é três anos a partir do dia da respetiva criação.


Para obter mais detalhes sobre implementações de limite de IoT, consulte o artigo [nesta página](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurar rotas
Limite de IoT fornece uma forma de forma declarativa encaminhar mensagens entre módulos e entre módulos e IoT Hub. A sintaxe completa é descrita [aqui](https://docs.microsoft.com/azure/iot-edge/module-composition).
Nomes de entradas e saídas criadas na tarefa ASA podem ser utilizados como pontos finais para o encaminhamento.  

###### <a name="example"></a>Exemplo
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Este exemplo mostra as rotas para o cenário descrito na imagem seguinte. Contém uma tarefa de limite chamada "**ASA**", com uma entrada com o nome "**temperatura**"e uma saída com o nome"**alerta**".
![Exemplo de encaminhamento](media/stream-analytics-edge/RoutingExample.png)

Neste exemplo, define as rotas seguintes:
- Cada mensagem do **tempSensor** é enviado para o módulo com o nome **ASA** para a entrada com o nome **temperatura**,
- Todas as saídas da **ASA** módulo são enviados para o IoT Hub ligado a este dispositivo ($upstream),
- Todas as saídas da **ASA** módulo são enviados para o **controlo** ponto final do **tempSensor**.


## <a name="technical-information"></a>Informações técnicas
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Limitações atuais para tarefas de limite, em comparação comparadas as tarefas de nuvem
O objetivo é ter paridade entre contorno trabalhos e tarefas de nuvem. A maioria das funcionalidades do nosso linguagem de consulta SQL já é suportada.
No entanto as seguintes funcionalidades não são suportadas ainda para tarefas de limite:
* As funções definidas pelo utilizador (UDF) e agregações definidas pelo utilizador (UDA).
* Funções do Azure ML.
* Utilizar mais do que 14 agregados num único passo.
* Formato AVRO de entrada/saída. Neste momento, apenas CSV e JSON são suportadas.
* Compressão de JSON de entrada.
* Os seguintes operadores SQL:
    * AnomalyDetection
    * Operadores de Geoespacial:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * PARTIÇÃO
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Requisitos de hardware e de tempo de execução
Para executar ASA num limite de IoT, terá de dispositivos que podem ser executados [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA e limite de IoT do Azure utilizar **Docker** contentores para disponibilizar uma solução portátil que é executada em vários anfitriões SO (Windows, Linux).

ASA no limite de IoT é disponibilizada como imagens do Windows e Linux, em execução no x86-64 ou arquiteturas ARM. 


### <a name="input-and-output"></a>Entrada e saída
#### <a name="input-and-output-streams"></a>Entrada e fluxos de saída
Tarefas do ASA Edge podem aproveitar entradas e saídas outros módulos em execução em dispositivos de limite de IoT. Para estabelecer a ligação de origem e de módulos específicos, pode definir a configuração de encaminhamento no momento da implementação. Mais informações são descritas no [a documentação de composição do módulo de limite de IoT](https://docs.microsoft.com/azure/iot-edge/module-composition).

Para entradas e saídas, são suportados os formatos CSV e JSON.

Para cada entrada e de fluxo de saída cria na sua tarefa do ASA, é criado um ponto final correspondente no seu módulo implementado. Estes pontos finais podem ser utilizados em rotas da sua implementação.



##### <a name="reference-data"></a>Dados de referência
Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto de dados finito que são estáticos ou abrandamento alterar natureza. É utilizado para efetuar uma pesquisa de ou para correlacionar com o fluxo de dados. Para tornar a utilização de dados de referência na sua tarefa do Azure Stream Analytics, normalmente, utilizará um [associação de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) na sua consulta. Para obter mais informações, consulte o [documentação ASA sobre dados de referência](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Para poder utilizar os dados de referência para ASA no limite do Iot, tem de seguir estes passos: 
1. Criar uma nova entrada para a sua tarefa
2. Escolha **referência a dados** como o **tipo de origem**.
3. Defina o caminho de ficheiro. O caminho do ficheiro deve ser um **absoluto** caminho do ficheiro no dispositivo ![criação de dados de referência](media/stream-analytics-edge/ReferenceData.png)
4. Ativar **partilhado unidades** a configuração de Docker e certifique-se a unidade está ativada antes de iniciar a implementação.

Para obter mais informações, consulte [Docker documentação de Windows aqui](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> De momento, apenas os dados de referência local são suportados.




## <a name="license-and-third-party-notices"></a>Licença e avisos de terceiros
* [O Azure Stream Analytics, licença de pré-visualização do limite de IoT](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Aviso de terceiros para o Azure Stream Analytics na pré-visualização do limite de IoT](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Obter ajuda
Para mais assistência, tente o [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Passos seguintes

* [Obter mais informações sobre o limite de Iot do Azure](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA tutorial IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Enviar comentários à equipa de utilizar este inquérito](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
