---
title: "Implementar, monitorizar módulos do Azure IoT Edge | Microsoft Docs"
description: "Gerir os módulos que são executadas em dispositivos de limite"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e7007bd6cca24dc4c2573fb274cecbf88ecfa374
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Implementar e monitorizar os módulos de limite de IoT à escala - pré-visualização

Limite de IoT do Azure permite-lhe mover analytics para o limite e fornece uma interface de nuvem, para que possa gerir e monitorizar os dispositivos de IoT Edge sem ter de aceder fisicamente cada um deles. A capacidade para gerir remotamente os dispositivos é cada vez mais importante como soluções de Internet das coisas estão a crescer maiores e mais complexas. Limite de IoT do Azure foi concebido para suportar os objetivos de negócio, independentemente do quantos dispositivos a adicionar.

Pode gerir os dispositivos individuais e implementá-las módulos um de cada vez. No entanto, se pretender efetuar alterações nos dispositivos em grande escala, pode criar um **implementação IoT Edge**. As implementações são dinâmicos processos que permitem-lhe implementar vários módulos em vários dispositivos de uma só vez, controlar o estado e estado de funcionamento dos módulos e efetuar alterações quando for necessário. 

## <a name="identify-devices-using-tags"></a>Identificar dispositivos utilizando as etiquetas

Antes de poder criar uma implementação, tem de ser capazes de especificar quais os dispositivos que pretende afetar. Limite do Azure IoT identifica dispositivos utilizando **etiquetas** no dispositivo duplo. Cada dispositivo pode ter várias etiquetas e pode defini-las qualquer forma que faz sentido para a sua solução. Por exemplo, se gerir um campus dos edifícios inteligentes, pode adicionar as seguintes etiquetas para um dispositivo:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Para obter mais informações sobre dispositivos duplos e etiquetas, consulte [compreender e utilizar dispositivos duplos no IoT Hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Criar uma implementação

1. Iniciar sessão para o [portal do Azure] [ lnk-portal] e navegue até ao seu IoT hub. 
1. Selecione **IoT Edge (pré-visualização)**.
1. Selecione **criar limite implementação**.

Existem cinco passos para criar uma implementação. As secções seguintes guiá-lo através de cada um deles. 

### <a name="step-1-label-deployment"></a>Passo 1: Implementação de etiqueta

1. Permitir a implementação de um ID exclusivo. Evite espaços e os seguintes carateres inválidos: `& ^ [ ] { } \ | " < > /`.
1. Adicione etiquetas para ajudar a monitorizar as implementações. As etiquetas são **nome**, **valor** pares que descrevem a sua implementação. Por exemplo, `HostPlatform, Linux` ou `Version, 3.0.1`.
1. Selecione **seguinte** mover para o passo dois. 

### <a name="step-2-add-modules"></a>Passo 2: Adicionar módulos

Existem dois tipos de módulos que pode adicionar a uma implementação. O primeiro é um módulo baseado num serviço do Azure, como a conta de armazenamento ou o Stream Analytics. O segundo é um módulo baseado nos seu próprio código. Pode adicionar vários módulos de qualquer tipo para uma implementação. 

>[!NOTE]
>O Azure Machine Learning e as funções do Azure não suportam a implementação de serviço do Azure automatizada ainda. Utilize a implementação do módulo personalizado para adicionar manualmente esses serviços à sua implementação. 

Para adicionar um módulo a partir de um Azure service, siga estes passos:
1. Selecione **módulo IoT limite do serviço de Azure adicionar**.
1. Utilize os menus de lista pendente para selecionar as instâncias de serviço do Azure que pretende implementar.
1. Selecione **guardar** para adicionar os módulos para a implementação. 

Para adicionar código personalizado como um módulo ou adicionar manualmente um módulo de serviço do Azure, siga estes passos:
1. Selecione **Adicionar módulo de limite de IoT personalizado**.
1. Atribua o módulo de um **nome**.
1. Para o **imagem** campo, introduza a imagem de contentor do Docker para este módulo: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
1. Utilize os menus de lista pendente em **SO** e **arquitetura** para identificar as propriedades do contentor Docker que representa este módulo. 
1. Especificar qualquer **criar opções** que deve ser transmitido ao contentor. Para obter mais informações, consulte [docker criar][lnk-docker-create].
1. Utilize o menu pendente para selecionar um **reiniciar política**. Escolha uma das seguintes opções: 
   * **Sempre** -o módulo for reiniciado sempre se encerrar por qualquer motivo.
   * **Nunca** -o módulo for reiniciado nunca se encerrar por qualquer motivo.
   * **Falha no** -o módulo for reiniciado se falhar, mas não se encerrar corretamente. 
   * **Num mau** -o módulo for reiniciado caso de falhas ou devolve um Estado de mau estado de funcionamento. Está a funcionar para cada módulo para implementar a função do Estado de funcionamento. 
1. Utilize o menu pendente para selecionar o arranque **estado** para o módulo. Escolha uma das seguintes opções:
   * **Executar** -esta é a opção predefinida. O módulo iniciará a executar imediatamente depois da implementação.
   * **Parado** -depois da implementação, o módulo permanecerá inativo até chamado após a iniciar por utilizador ou outro módulo.
1. Selecione **duplo de módulo de edição** se pretender adicionar quaisquer etiquetas ou propriedades pretendidas para o módulo. 
1. Selecione **guardar** para adicionar o módulo para a implementação. 

Depois de ter todos os módulos para uma implementação configurado, selecione **seguinte** mover para o terceiro passo.

### <a name="step-3-specify-routes-optional"></a>Passo 3: Especificar rotas (opcionais)

Rotas definem como módulos comunicam entre si numa implementação. Especificar as rotas para a sua implementação, em seguida, selecione **seguinte** mover para o quarto passo. 

### <a name="step-4-target-devices"></a>Passo 4: Dispositivos de destino

Utilize a propriedade de etiquetas dos seus dispositivos para os dispositivos específicos que devem receber esta implementação de destino. 

Uma vez que as implementações de vários podem ter como destino o mesmo dispositivo, deverá dar-cada implementação de um número de prioridade. Se alguma vez existir um conflito, a implementação com a prioridade mais alta wins. Se duas implementações com o mesmo número de prioridade, que foi criada mais recentemente wins. 

1. Introduza um número inteiro positivo para a implementação **prioridade**.
1. Introduza um **condição de destino** para determinar quais os dispositivos que irão ser segmentados com esta implementação. A condição baseia-se nas etiquetas do dispositivo duplo e deve corresponder ao formato de expressão. Por exemplo, `tags.environment='test'`. 
1. Selecione **seguinte** para avançar para o passo final.

### <a name="step-5-review-template"></a>Passo 5: Rever modelo

Reveja as informações de implementação, em seguida, selecione **submeter**.

## <a name="monitor-a-deployment"></a>Monitorizar uma implementação

Para ver os detalhes de uma implementação e monitorizar os dispositivos a executá-lo, utilize os seguintes passos:

1. Iniciar sessão para o [portal do Azure] [ lnk-portal] e navegue até ao seu IoT hub. 
1. Selecione **IoT Edge (pré-visualização)**.
1. Selecione **implementações de limite de IoT**. 

   ![Ver as implementações de limite de IoT][1]

1. Inspecione a lista de implementação. Para cada implementação, pode ver os seguintes detalhes:
   * **ID** -o nome da implementação.
   * **Condição de destino** -tag utilizado para definir os dispositivos visados.
   * **Prioridade** -o número de prioridade atribuído à implementação.
   * **Estado do agente de limite de IoT** -o número de dispositivos que receberam a implementação e os respetivos Estados de funcionamento. 
   * **Mau estado de funcionamento módulos** -o número de módulos na implementação do relatório de erros. 
   * **Hora de criação** -timestamp de quando a implementação foi criada. Este timestamp é utilizado para dividir ties quando duas implementações têm a mesma prioridade. 
1. Selecione a implementação que pretende monitorizar.  
1. Inspecione os detalhes da implementação. Pode utilizar os separadores para ver detalhes específicos sobre os dispositivos que receberam a implementação: 
   * **Direcionados** -os dispositivos de limite que correspondem à condição de destino. 
   * **Aplicar** - direcionados dispositivos de limite que não são visados por outra implementação de maior prioridade. Estes são os dispositivos que, na verdade, recebem a implementação. 
   * **Relatórios êxito** - aplicadas a dispositivos de limite que comunicou o novamente ao serviço que os módulos foram implementados com êxito. 
   * **Falha de relatórios** - os dispositivos de limite aplicados que comunicou de volta para o serviço que um ou mais módulos não foram implementados com êxito. Para continuar a investigar o erro, terá de ligar remotamente a esses dispositivos e ver os ficheiros de registo. 
   * **Relatório de módulos mau estado de funcionamento** - os dispositivos de limite aplicados que comunicou de volta para o serviço que um ou mais módulos foram implementados com êxito, mas agora estiverem a comunicar erros. 

## <a name="modify-a-deployment"></a>Modificar uma implementação

Quando modifica uma implementação, as alterações imediatamente são replicadas para todos os dispositivos visados. 

Se atualizar a condição de destino, ocorrem as seguintes atualizações:
* Se um dispositivo não cumpre a condição de destino antigo, mas cumpre a condição de destino novo e esta implementação é a prioridade mais elevada para que o dispositivo, esta implementação é aplicada ao dispositivo. 
* Se um dispositivo atualmente em execução nesta implementação já não satisfaz a condição de destino, desinstala esta implementação e demora na implementação de prioridade mais alta seguinte. 
* Se um dispositivo atualmente em execução nesta implementação já não satisfaz a condição de destino e não satisfaz a condição de destino de todas as outras implementações, nenhuma alteração ocorre no dispositivo. O dispositivo continua em execução os respectivos módulos atuais no respetivo estado atual, mas não está a ser gerido como parte desta implementação já. Assim que cumpre a condição de destino de qualquer outra implementação, desinstala esta implementação e demora no novo. 

Para modificar uma implementação, utilize os seguintes passos: 

1. Iniciar sessão para o [portal do Azure] [ lnk-portal] e navegue até ao seu IoT hub. 
1. Selecione **IoT Edge (pré-visualização)**.
1. Selecione **implementações de limite de IoT**. 

   ![Ver as implementações de limite de IoT][1]

1. Selecione a implementação que pretende modificar. 
1. Efetue atualizações para os seguintes campos: 
   * Condição de destino 
   * Etiquetas 
   * Prioridade 
1. Selecione **Guardar**.
1. Siga os passos no [monitorizar uma implementação] [ anchor-monitor] para ver as alterações a implementar. 

## <a name="delete-a-deployment"></a>Eliminar uma implementação

Quando elimina uma implementação, todos os dispositivos de colocar as suas implementações de maior prioridade seguinte. Se os seus dispositivos não cumprem a condição de destino de qualquer outra implementação, os módulos não são removidos quando a implementação é eliminada. 

1. Iniciar sessão para o [portal do Azure] [ lnk-portal] e navegue até ao seu IoT hub. 
1. Selecione **IoT Edge (pré-visualização)**.
1. Selecione **implementações de limite de IoT**. 

   ![Ver as implementações de limite de IoT][1]

1. Utilize a caixa de verificação para selecionar a implementação que pretende eliminar. 
1. Selecione **Eliminar**.
1. Uma linha de comandos informar que esta ação irá eliminar esta implementação e reverter o estado anterior para todos os dispositivos.  Isto significa que uma implementação com uma prioridade mais baixa será aplicada.  Não se for aplicada nenhuma outra implementação, sem módulos serão removidos. Se os clientes pretenderem efetuar este procedimento, tem de criar uma implementação com zero módulos e implementá-la nos dispositivos. Selecione **Sim** se pretender continuar. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [implementar módulos para dispositivos de limite][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/view-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
