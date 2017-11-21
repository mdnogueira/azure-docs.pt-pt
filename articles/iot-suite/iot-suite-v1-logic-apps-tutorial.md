---
title: Azure IoT Suite e as Logic Apps | Microsoft Docs
description: Um tutorial sobre como ligar Logic Apps para o Azure IoT Suite para o processo empresarial.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4a1db86f4b715533dfea545365eaf66de0574c5e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>Tutorial: Ligar aplicação lógica à sua solução do Azure IoT Suite monitorização remota pré-configurada
O [Microsoft Azure IoT Suite] [ lnk-internetofthings] solução pré-configurada de monitorização remota é uma excelente forma de começar a trabalhar rapidamente com um conjunto de funcionalidades de ponto-a-ponto que exemplifies uma solução de IoT. Este tutorial explica como adicionar aplicação lógica para o Microsoft Azure IoT Suite solução pré-configurada de monitorização remota. Estes passos demonstram como pode tirar ainda mais a sua solução de IoT ao ligar a um processo empresarial.

*Se pretender para instruções sobre como aprovisionar a solução pré-configurada de uma monitorização remota, consulte o artigo [Tutorial: introdução ao soluções pré-configuradas do IoT][lnk-getstarted].*

Antes de começar este tutorial, deve:

* Aprovisione a solução pré-configurada de monitorização remota na sua subscrição do Azure.
* Crie uma conta do SendGrid que lhe permite enviar um e-mail que aciona o processo de negócio. Pode inscrever-se para uma conta de avaliação gratuita em [SendGrid](https://sendgrid.com/) clicando **Experimente gratuitamente**. Depois de ter registado para a sua conta de avaliação gratuita, terá de criar um [chave de API](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) no SendGrid que concede permissões para enviar correio. É necessário esta chave de API mais tarde no tutorial.

Para concluir este tutorial, terá de Visual Studio 2015 ou Visual Studio 2017 para modificar as ações na solução pré-configurada de back-end.

Partindo do princípio de que já tenha sido já aprovisionada monitorização remota solução pré-configurada, navegue para o grupo de recursos para essa solução no [portal do Azure][lnk-azureportal]. O grupo de recursos tem o mesmo nome como o nome de solução que escolheu quando aprovisionou a sua solução de monitorização remota. No grupo de recursos, pode ver todos os recursos do Azure aprovisionados para a sua solução. A seguinte captura de ecrã mostra um exemplo **grupo de recursos** solução pré-configurada do painel para uma monitorização remota:

![](media/iot-suite-v1-logic-apps-tutorial/resourcegroup.png)

Para começar, configure a aplicação lógica para utilizar com a solução pré-configurada.

## <a name="set-up-the-logic-app"></a>Configurar a aplicação lógica
1. Clique em **adicionar** na parte superior do painel do grupo de recursos no portal do Azure.
2. Procurar **aplicação lógica**, selecione-o e, em seguida, clique em **criar**.
3. Preencha o **nome** e utilizar o mesmo **subscrição** e **grupo de recursos** que utilizou quando aprovisionou a sua solução de monitorização remota. Clique em **Criar**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/createlogicapp.png)
4. Quando tiver concluído a sua implementação, pode ver que a aplicação lógica está listada como um recurso no seu grupo de recursos.
5. Clique na aplicação lógica para navegar para o painel de aplicação lógica, selecione o **aplicação lógica em branco** modelo para abrir o **Designer de aplicações lógicas**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappsdesigner.png)
6. Selecione **pedido**. Esta ação Especifica que um pedido HTTP recebido com um JSON específico formatado atos de payload como um acionador.
7. Cole o seguinte código para o esquema de JSON de corpo do pedido:
   
    ```json
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > Pode copiar o URL para o post HTTP depois de guardar a aplicação lógica, mas primeiro tem de adicionar uma ação.
   > 
   > 
8. Clique em **+ novo passo** sob o acionador manual. Em seguida, clique em **adicionar uma ação**.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappcode.png)
9. Procurar **SendGrid - enviar e-mail** e clique no mesmo.
   
    ![](media/iot-suite-v1-logic-apps-tutorial/logicappaction.png)
10. Introduza um nome para a ligação, tal como **SendGridConnection**, introduza o **chave de API do SendGrid** que criou quando configurar a conta do SendGrid e clique em **criar**.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridconnection.png)
11. Adicionar endereços de e-mail lhe pertence a duas o **de** e **para** campos. Adicionar **alerta de monitorização remota [DeviceId]** para o **requerente** campo. No **corpo da mensagem** campo, adicione **dispositivo [DeviceId] apresentou [measurementName] com o valor [measuredValue].** Pode adicionar **[DeviceId]**, **[measurementName]**, e **[measuredValue]** clicando no **pode inserir dados dos passos anteriores** secção.
    
    ![](media/iot-suite-v1-logic-apps-tutorial/sendgridaction.png)
12. Clique em **guardar** no menu superior.
13. Clique em de **pedido** acionador e copie o **Post de Http para este URL** valor. Este URL é necessário mais tarde no tutorial.

> [!NOTE]
> As Logic Apps permitem-lhe executar [vários tipos de ação] [ lnk-logic-apps-actions] incluindo ações no Office 365. 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>Configurar a tarefa de Web EventProcessor
Nesta secção, ligar a solução pré-configurada para a aplicação lógica que criou. Para concluir esta tarefa, adicione o URL para acionar a aplicação lógica para a ação que é acionado quando um valor de sensor dispositivo excede um limiar.

1. Utilize o cliente de git para clonar a versão mais recente do [azure-iot-remote-monitoring repositório do github][lnk-rmgithub]. Por exemplo:
   
    ```cmd
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. No Visual Studio, abra o **RemoteMonitoring.sln** partir da cópia local do repositório.
3. Abra o **ActionRepository.cs** ficheiros o **infraestrutura\\repositório** pasta.
4. Atualização do **actionIds** dicionário com o **Post de Http para este URL** que anotou da sua aplicação lógica da seguinte forma:
   
    ```csharp
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. Guarde as alterações na solução e sair do Visual Studio.

## <a name="deploy-from-the-command-line"></a>Implementar a partir da linha de comandos
Nesta secção, implementar a versão atualizada da solução de monitorização remota para substituir a versão atualmente em execução no Azure.

1. Seguir o [dev configuração] [ lnk-devsetup] instruções para configurar o ambiente para a implementação.
2. Para implementar localmente, siga o [implementação local] [ lnk-localdeploy] instruções.
3. Para implementar na nuvem e atualizar a implementação de nuvem existente, siga o [implementação na nuvem] [ lnk-clouddeploy] instruções. Utilize o nome da implementação original como o nome da implementação. Por exemplo, se a implementação original foi chamada **demologicapp**, utilize o seguinte comando:
   
   ```cmd
   build.cmd cloud release demologicapp
   ```
   
   Quando executa o script de compilação, lembre-se de que utilizam a mesma conta do Azure, subscrição, região e instância do Active Directory que utilizou quando aprovisionou a solução.

## <a name="see-your-logic-app-in-action"></a>Ver a sua aplicação lógica em ação
A solução pré-configurada de monitorização remota tem duas regras configurar por predefinição, quando Aprovisiona uma solução. Ambas as regras são no **SampleDevice001** dispositivo:

* Temperatura > 38.00
* Humidade > 48.00

Os acionadores de regra de temperatura de **elevar alarme** ação e a humidade regra acionadores a **SendMessage** ação. Partindo do princípio de que o mesmo URL que utilizou para ambas as ações a **ActionRepository** classe, os acionadores da aplicação lógica para a regra. Ambas as regras de utilizam SendGrid para enviar um e-mail para o **para** endereço com os detalhes do alerta.

> [!NOTE]
> A aplicação lógica continua a acionar sempre que é cumprido o limiar. Para evitar e-mails desnecessários, pode desativar as regras no seu portal de solução ou desativar a aplicação lógica no [portal do Azure][lnk-azureportal].
> 
> 

Para além de receber e-mails, também pode ver quando executa a aplicação lógica no portal:

![](media/iot-suite-v1-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>Passos seguintes
Agora que utilizou uma aplicação lógica para ligar a solução pré-configurada a um processo empresarial, pode saber mais sobre as opções para personalizar as soluções pré-configuradas:

* [Utilizar telemetria dinâmica com a solução pré-configurada de monitorização remota][lnk-dynamic]
* [Metadados de informações de dispositivos na solução pré-configurada de monitorização remota][lnk-devinfo]

[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-v1-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
