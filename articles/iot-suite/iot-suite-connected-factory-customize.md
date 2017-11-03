---
title: "Personalizar a solução de fábrica ligado - Azure | Microsoft Docs"
description: "Uma descrição de como personalizar o comportamento da fábrica de ligado a solução pré-configurada."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4505023c1869c0bb23e99dc7db1ee6e65466c2cc
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar a forma como a solução de fábrica ligado apresenta dados dos seus servidores de OPC UA

## <a name="introduction"></a>Introdução

A solução de fábrica ligado agrega e apresenta os dados dos servidores de OPC UA ligados à solução. Pode procurar e enviar comandos para os servidores de OPC UA na sua solução. Para obter mais informações sobre OPC UA, veja as [FAQ sobre a fábrica ligada](iot-suite-faq-cf.md).

Exemplos de dados agregados na solução incluem a eficiência global de equipamento (OEE) e os indicadores de desempenho de chave (KPIs) que pode ver no dashboard de fábrica, linha e níveis de estação. A seguinte captura de ecrã mostra os valores OEE e KPI para a **assemblagem** estação no **produção linha 1**, no **Munich** factory:

![Exemplo de valores OEE e KPI na solução][img-oee-kpi]

A solução permite-lhe ver informações detalhadas de itens de dados específico de servidores OPC UA, denominados *estações*. A seguinte captura de ecrã mostra rastreia do número de itens fabricados de uma estação específica:

![Plots do número de itens fabricados][img-manufactured-items]

Se clicar em um dos gráficos, pode explorar os dados, utilizando as informações de séries de tempo (TSI):

![Explore os dados através das informações de séries de tempo][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias vistas na solução.
- Como pode personalizar a forma como a solução mostra os dados.

## <a name="data-sources"></a>Origens de dados

A solução de fábrica ligado apresenta os dados dos servidores de OPC UA ligados à solução. A instalação predefinida inclui várias OPC UA servidores que executam uma simulação de fábrica. Pode adicionar os seus próprios servidores OPC UA que [ligar através de um gateway] [ lnk-connect-cf] à sua solução.

Pode procurar os itens de dados que pode enviar um servidor de OPC UA ligado à sua solução no dashboard:

1. Navegue para o **selecione um servidor de OPC UA** vista:

    ![Navegue para selecionar uma vista do servidor de OPC UA][img-select-server]

1. Selecione um servidor e clique em **Connect**. Clique em **continuar** quando aparece o aviso de segurança.

    > [!NOTE]
    > Este aviso só é apresentada uma vez para cada servidor e estabelece uma relação de confiança entre o dashboard de solução e o servidor.

1. Pode procurar os itens de dados que o servidor pode enviar para a solução. Os itens que estão a ser enviados para a solução têm uma marca de verificação verde:

    ![Itens publicados][img-published]

1. Se for um *administrador* na solução, pode optar por publicar um item de dados para o disponibilizar na solução de fábrica ligado. Como administrador, também pode alterar o valor dos itens de dados e chamar os métodos no servidor de OPC UA.

## <a name="map-the-data"></a>Os dados do mapa

A solução de fábrica ligado mapeia e agrega os itens de dados publicados do servidor de OPC UA para as várias vistas na solução. Implementa a solução de fábrica ligado à sua conta do Azure, quando Aprovisiona a solução. Um ficheiro JSON na solução de fábrica do Visual Studio ligado armazena estas informações de mapeamento. Pode ver e modificar este ficheiro de configuração JSON na fábrica de ligado solução do Visual Studio. Pode implementar a solução pode ser novamente depois de efetuar uma alteração.

Pode utilizar o ficheiro de configuração para:

- Edite as fábricas de simulada existentes, linhas de produção e estações.
- Mapear dados dos servidores de OPC UA reais que ligam à solução.

A clonagem de uma cópia do factory ligado solução do Visual Studio, utilize o seguinte comando do git:

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

O ficheiro **ContosoTopologyDescription.json** define o mapeamento de itens de dados do servidor de OPC UA para as vistas no dashboard de solução de fábrica ligado. Pode encontrar este ficheiro de configuração no **Contoso\Topology** pasta o **WebApp** projeto na solução Visual Studio.

O conteúdo do ficheiro JSON está organizado como uma hierarquia de fábrica, linha de produção e nós de estação. Esta hierarquia define a hierarquia de navegação no dashboard do factory ligado. Os valores em cada nó da hierarquia determinam as informações apresentadas no dashboard. Por exemplo, o ficheiro JSON contém os seguintes valores para a fábrica de Munich:

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

O nome, a descrição e a localização aparecem nesta vista no dashboard:

![Dados de Munich no dashboard][img-munich]

Cada fábrica, a linha de produção e a estação de ter uma propriedade de imagem. Pode encontrar estes ficheiros JPEG o **Content\img** pasta o **WebApp** projeto. Estes ficheiros de imagem a apresentar no dashboard do factory ligado.

Cada estação inclui várias propriedades de detalhado que definem o mapeamento de itens de dados OPC UA. Estas propriedades são descritas nas secções seguintes:

### <a name="opcuri"></a>OpcUri

O **OpcUri** valor é o URI de aplicação de UA OPC que identifica exclusivamente o servidor de OPC UA. Por exemplo, o **OpcUri** valor para a estação de assemblagem na linha de produção 1 na Munich o seguinte aspeto: **urn: scada2194:ua:munich:productionline0:assemblystation**.

Pode ver os URIs de servidores de OPC UA ligados no dashboard de solução:

![Ver o servidor de OPC UA URIs][img-server-uris]

### <a name="simulation"></a>Simulação

As informações de **simulação** nó é específico para a simulação de OPC UA que é executado em servidores de OPC UA aprovisionados por predefinição. Não é utilizado para um servidor de OPC UA real.

### <a name="kpi1-and-kpi2"></a>Kpi1 e Kpi2

Estes nós descrevem como os dados a partir da estação contribuem para os dois valores KPI no dashboard. Numa implementação predefinida, estes valores KPI são unidades por hora e kWh por hora. A solução calcula os valores KPI no nível de uma estação e agrega-los na linha de produção e níveis de fábrica.

Cada KPI tem um valor de destino, mínimo e máximo. Cada valor KPI também pode definir ações de alerta para a solução de fábrica ligado executar. O fragmento seguinte mostra as definições de KPI para a estação de assemblagem na linha de produção 1 na Munich:

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

A seguinte captura de ecrã mostra os dados KPI no dashboard.

![Informações de KPI no dashboard][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

O **OpcNodes** nós identificar os itens de dados publicados do servidor de OPC UA e especifique como processar os dados.

O **NodeId** valor identifica o NodeID de UA OPC específica do servidor de OPC UA. O primeiro nó na estação de assemblagem para a linha de produção 1 na Munich tem um valor **ns = 2; posso = 385**. A **NodeId** valor Especifica o item de dados para ler a partir do servidor de OPC UA e o **SymbolicName** fornece um nome amigável de utilizador a utilizar no dashboard para que os dados.

Outros valores associados a cada nó estão resumidos na tabela seguinte:

| Valor | Descrição |
| ----- | ----------- |
| Relevância  | Os valores KPI e OEE que estes dados contribuem para. |
| OpCode     | Como os dados são agregados. |
| Unidades      | As unidades para utilizar no dashboard.  |
| Visível    | Indica se deve apresentar este valor no dashboard. Alguns valores são utilizados nos cálculos mas não ser apresentados.  |
| Máximo    | O valor máximo que aciona um alerta no dashboard. |
| MaximumAlertActions | Uma ação a tomar em resposta a um alerta. Por exemplo, envie um comando para uma estação. |
| ConstValue | Um valor constante utilizado um cálculo. |

## <a name="deploy-the-changes"></a>Implementar as alterações

Quando terminar de efetuar as alterações para o **ContosoTopologyDescription.json** ficheiro, é necessário voltar a implementar a solução de fábrica ligado à sua conta do Azure.

O **do azure-iot-ligado-factory** repositório inclui um **build.ps1** script do PowerShell que pode utilizar para reconstruir e implementar a solução.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a solução de fábrica ligado pré-configurada ao ler os artigos seguintes:

* [Connected factory preconfigured solution walkthrough][lnk-rm-walkthrough] (Instruções da solução pré-configurada de fábrica de dados)
* [Implementar um gateway para a fábrica ligado][lnk-connect-cf]
* [Permissões no site azureiotsuite.com][lnk-permissions]
* [FAQ de fábrica ligada](iot-suite-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md