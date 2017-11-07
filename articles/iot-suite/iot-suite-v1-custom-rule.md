---
title: Criar uma regra personalizada no Azure IoT Suite | Microsoft Docs
description: "Como criar uma regra personalizada numa solução pré-configuradas do IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Criar uma regra personalizada na solução pré-configurada de monitorização remota

## <a name="introduction"></a>Introdução

As soluções pré-configuradas, pode configurar [regras que é acionam quando uma telemetria valor para um dispositivo atinge um limiar específico][lnk-builtin-rule]. [Solução pré-configurada de telemetria de dinâmica de utilização com a monitorização remota] [ lnk-dynamic-telemetry] descreve como adicionar valores de telemetria personalizada, tal como *ExternalTemperature* à sua solução. Este artigo mostra como criar uma regra personalizada para tipos de telemetria dinâmico na sua solução.

Este tutorial utiliza um dispositivo simulado de Node.js simple para gerar telemetria dinâmica para enviar para o solução pré-configurada de back-end. Em seguida, adicionar regras personalizadas no **RemoteMonitoring** solução do Visual Studio e implementar este back-end personalizado à sua subscrição do Azure.

Para concluir este tutorial, precisa de:

* Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk_free_trial].
* [NODE.js] [ lnk-node] versão 0.12 ou posterior para criar um dispositivo simulado.
* Visual Studio 2015 ou Visual Studio 2017 para modificar a solução pré-configurada de back-end com as novas regras de.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

Anote o nome de solução que escolheu para a sua implementação. Terá este nome de solução mais tarde no tutorial.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

Pode parar a aplicação de consola do Node.js quando tiver verificado que está a enviar **ExternalTemperature** telemetria para a solução pré-configurada. Mantenha a janela de consola aberta porque executar esta aplicação de consola do Node.js novamente depois de adicionar a regra personalizada para a solução.

## <a name="rule-storage-locations"></a>Localizações de armazenamento de regra

Informações sobre regras é persistente em duas localizações:

* **DeviceRulesNormalizedTable** tabela – esta tabela armazena uma referência normalizada às regras definidas pelo portal de solução. Quando o portal de solução apresenta as regras de dispositivos, consulta esta tabela para as definições da regra.
* **DeviceRules** blob – este blob armazena todas as regras definidas para todos os dispositivos de registado e estão definidos como uma referência de entrada para as tarefas do Azure Stream Analytics.
 
Quando atualizar uma regra existente ou definir uma nova regra no portal de solução, a tabela e o blob são atualizadas para refletir as alterações. A definição da regra apresentada no portal do provém do arquivo de tabela e a definição da regra referenciada pelas tarefas do Stream Analytics provém do blob. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Atualizar a solução de RemoteMonitoring Visual Studio

Os passos seguintes mostram como modificar a solução de RemoteMonitoring Visual Studio para incluir uma nova regra que utiliza o **ExternalTemperature** telemetria enviada do dispositivo simulado:

1. Se ainda não o tiver feito, clone o **azure-iot-remote-monitoring** repositório para uma localização adequada no seu computador local utilizando o seguinte comando do Git:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. No Visual Studio, abra o ficheiro de RemoteMonitoring.sln da sua cópia local do **azure-iot-remote-monitoring** repositório.

3. Abra o ficheiro Infrastructure\Models\DeviceRuleBlobEntity.cs e adicionar um **ExternalTemperature** propriedade da seguinte forma:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. No mesmo ficheiro, adicione um **ExternalTemperatureRuleOutput** propriedade da seguinte forma:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Abra o ficheiro Infrastructure\Models\DeviceRuleDataFields.cs e adicione o seguinte **ExternalTemperature** propriedade depois existente **humidade** propriedade:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. No mesmo ficheiro, atualize o **_availableDataFields** método para incluir **ExternalTemperature** da seguinte forma:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Abra o ficheiro Infrastructure\Repository\DeviceRulesRepository.cs e modificar o **BuildBlobEntityListFromTableRows** método da seguinte forma:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Reconstruir e voltar a implementar a solução.

Agora pode implementar a solução atualizada à sua subscrição do Azure.

1. Abra uma linha de comandos elevada e navegue para a raiz da sua cópia local do repositório azure-iot-remote-monitoring.

2. Para implementar a sua solução atualizada, execute o seguinte comando substituindo **{nome da implementação}** com o nome da implementação da solução pré-configurada que apontou anteriormente:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Atualizar a tarefa de Stream Analytics

Quando a implementação estiver concluída, pode atualizar a tarefa de Stream Analytics para utilizar as novas definições de regra.

1. No portal do Azure, navegue para o grupo de recursos contém recursos da sua solução pré-configurada. Este grupo de recursos tem o mesmo nome que especificou para a solução durante a implementação.

2. Navegue para o {nome da implementação}-tarefa do Stream Analytics de regras. 

3. Clique em **parar** para parar a tarefa de Stream Analytics a execução. (Tem de aguardar a tarefa de transmissão em fluxo parar antes de poder editar a consulta).

4. Clique em **consulta**. Editar a consulta para incluir o **SELECIONE** instrução para **ExternalTemperature**. O exemplo seguinte mostra a consulta concluída com o novo **SELECIONE** instrução:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Clique em **guardar** para alterar a consulta de regra atualizado.

6. Clique em **iniciar** para iniciar a tarefa de Stream Analytics executar de novo.

## <a name="add-your-new-rule-in-the-dashboard"></a>Adicionar a nova regra no dashboard

Agora pode adicionar o **ExternalTemperature** regra para um dispositivo no dashboard de solução.

1. Navegue para o portal de solução.

2. Navegue para o **dispositivos** painel.

3. Localize o dispositivo personalizado que criou que envia **ExternalTemperature** telemetria e o **detalhes do dispositivo** painel, clique em **Adicionar regra**.

4. Selecione **ExternalTemperature** no **campo de dados**.

5. Definir **limiar** para 56. Em seguida, clique em **guardar e ver regras**.

6. Regressar ao dashboard para ver o histórico de alarme.

7. Na janela da consola que deixadas abertas, iniciar a aplicação de consola do Node.js para começar a enviar **ExternalTemperature** dados de telemetria.

8. Tenha em atenção que o **histórico de alarme** tabela mostra os novos alarmes quando a nova regra é acionada.
 
## <a name="additional-information"></a>Informações adicionais

Alterar o operador  **>**  for mais complexo e vai mais além os passos descritos neste tutorial. Enquanto pode alterar a tarefa de Stream Analytics para utilizar qualquer operador pretender, ao refletir que operador no portal de solução é uma tarefa mais complexa. 

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a criar regras personalizadas, pode saber mais sobre as soluções pré-configuradas:

- [Ligar aplicação lógica à sua solução do Azure IoT Suite monitorização remota pré-configurada][lnk-logic-app]
- [Solução pré-configurada de monitorização remota os metadados de informações do dispositivo][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md