---
title: "Introdução às soluções pré-configuradas | Microsoft Docs"
description: "Siga este tutorial para saber como implementar uma solução pré-configurada do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: Introdução às soluções pré-configuradas
## <a name="introduction"></a>Introdução
As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam múltiplos serviços do Azure IoT para fornecer soluções ponto-a-ponto que implementam cenários de negócios comuns do IoT. A solução pré-configurada de *monitorização remota* estabelece ligação aos seus dispositivos e monitoriza-os. Pode utilizar a solução para analisar o fluxo de dados dos seus dispositivos e melhorar os resultados de negócios ao fazer com que os processos respondam automaticamente a esse fluxo de dados.

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada de monitorização remota. Também disponibiliza orientações sobre as funcionalidades básicas da solução pré-configurada. Pode aceder a muitas destas funcionalidades através do dashboard da solução pré-configurada que é implementado juntamente com a mesma:

![Dashboard da solução pré-configurada de monitorização remota][img-dashboard]

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [!NOTE]
> Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Ver o dashboard da solução
O dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver a telemetria, adicionar dispositivos e configurar regras.

1. Quando o aprovisionamento estiver concluído e o mosaico da sua solução pré-configuradas indicar **Pronto**, clique em **Iniciar** para abrir o seu portal de solução de monitorização remota num novo separador.
   
   ![Iniciar a solução pré-configurada][img-launch-solution]
2. Por predefinição, o portal de solução mostra o *dashboard de solução*. Pode selecionar outras vistas utilizando o menu da esquerda.
   
   ![Dashboard da solução pré-configurada de monitorização remota][img-dashboard]

O dashboard apresenta as seguintes informações:

* O mapa apresenta a localização de cada dispositivo ligado à solução. Quando executar a solução pela primeira vez, irá ver quatro dispositivos simulados. Os dispositivos simulados são implementados como WebJobs do Azure e a solução utiliza a API do Bing Maps para rastrear informações no mapa.
* O painel **Histórico de Telemetria** rastreia em tempo real a humidade e a telemetria de temperatura de um dispositivo selecionado e mostra os dados agregados, como a humidade máxima, mínima e média.
* O painel **Histórico de Alarme** mostra eventos de alarme recentes quando um valor de telemetria ultrapassar um limiar. Para além dos exemplos criados pela solução pré-configurada, pode ainda definir os seus próprios alarmes.
* O painel **Tarefas** apresenta informações sobre as tarefas agendadas. Pode agendar as suas próprias tarefas na página **Tarefas de gestão**.

## <a name="view-the-device-list"></a>Ver a lista de dispositivos
A *lista de dispositivos* mostra todos os dispositivos registados na solução. Na lista de dispositivos, pode ver e editar os metadados dos dispositivos, adicionar ou remover dispositivos e invocar métodos nos mesmos.

1. Clique em **Dispositivos**, no menu do lado esquerdo, para mostrar a lista de dispositivos desta solução.
   
   ![Lista de dispositivos no dashboard][img-devicelist]
2. A lista de dispositivos mostra, inicialmente, quatro dispositivos simulados criados pelo processo de aprovisionamento. Pode adicionar mais dispositivos simulados e físicos à solução.
3. Pode clicar em **Editor de colunas** para personalizar as informações mostradas na lista de dispositivos. Pode adicionar e remover colunas que apresentam valores de etiquetas e de propriedades comunicados. Também pode reorganizar e mudar o nome das colunas:
   
   ![Editor de colunas no dashboard][img-columneditor]
4. Para ver os detalhes de um dispositivo, clique no mesmo na lista de dispositivos.
   
   ![Detalhes de dispositivos no dashboard][img-devicedetails]

O painel **Detalhes do Dispositivo** contêm seis secções:

* Uma coleção de ligações que lhe permitem personalizar o ícone do dispositivo, desativá-lo, adicionar regras, invocar métodos ou enviar comandos. Para obter uma comparação dos comandos (mensagens do dispositivo para a cloud) e dos métodos (métodos diretos), veja [Cloud-to-device communications guidance (Orientação sobre comunicações da cloud para dispositivos)][lnk-c2d-guidance].
* A secção **Dispositivo Duplo - Etiquetas** permite-lhe editar os valores das etiquetas do dispositivo. Pode apresentar os valores de etiquetas na lista de dispositivos e utilizá-los para filtrá-la.
* A secção **Dispositivo Duplo - Propriedades Pretendidas** permite-lhe definir os valores das propriedades a serem enviados para o dispositivo.
* A secção **Dispositivo Duplo - Propriedades Comunicadas** mostra os valores das propriedades enviados pelo dispositivo.
* A secção **Propriedades do Dispositivo** mostra informações do registo de identidades, tal como o ID do dispositivo e as chaves de autenticação.
* A secção **Tarefas Recentes** mostra informações sobre todas as tarefas que direcionaram este dispositivo recentemente.

## <a name="customize-the-device-icon"></a>Personalizar o ícone do dispositivo

Pode personalizar o ícone do dispositivo apresentado na lista de dispositivos no painel **Detalhes do Dispositivo**, da seguinte forma:

1. Clique no ícone de lápis para abrir o painel **Editar imagem** de um dispositivo:
   
   ![Abrir o editor de imagem do dispositivo][img-startimageedit]
2. Carregue uma imagem nova ou utilize uma das existentes e clique em **Guardar**:
   
   ![Editor para editar a imagem do dispositivo][img-imageedit]
3. A imagem que selecionou é agora apresentada na coluna **Ícone** do dispositivo.

> [!NOTE]
> A imagem é armazenada no armazenamento de blobs. As etiquetas no dispositivo duplo contêm ligações para a imagem no armazenamento de blobs.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Invocar um método num dispositivo
No painel **Detalhes do Dispositivo**, pode invocar métodos no dispositivo. Quando um dispositivo é iniciado pela primeira vez, envia informações sobre os métodos que suporta à solução.

1. Clique em **Métodos**, no painel **Detalhes do Dispositivo** do dispositivo selecionado:
   
   ![Métodos de dispositivos no dashboard][img-devicemethods]
2. Selecione **Reinício**, na lista de métodos.
3. Clique em **Invocar Método**.
4. Pode ver o estado da invocação do método no histórico do mesmo.
   
   ![Estado do método no dashboard][img-pingmethod]

A solução monitoriza o estado de cada método que invoca. Quando o dispositivo concluir o método, verá uma entrada nova na tabela do histórico de métodos.

Alguns métodos iniciam tarefas assíncronas no dispositivo. Por exemplo, o método **InitiateFirmwareUpdate** inicia uma tarefa assíncrona para realizar a atualização. O dispositivo utiliza propriedades comunicadas para comunicar o estado da atualização de firmware à medida que progride.

## <a name="send-a-command-to-a-device"></a>Enviar um comando a um dispositivo
No painel **Detalhes do Dispositivo**, pode enviar comandos para o dispositivo. Quando um dispositivo é iniciado pela primeira vez, envia informações sobre os comandos que suporta à solução.

1. Clique em **Comandos**, no painel **Detalhes do Dispositivo** do dispositivo selecionado:
   
   ![Comandos de dispositivos no dashboard][img-devicecommands]
2. Selecione **PingDevice** na lista de comandos.
3. Clique em **Enviar Comando**.
4. Pode ver o estado do comando no histórico do comando.
   
   ![Estado do comando no dashboard][img-pingcommand]

A solução controla o estado de cada comando que envia. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido para **Êxito**.

## <a name="add-a-new-simulated-device"></a>Adicionar um novo dispositivo simulado
Ao implementar a solução pré-configurada, aprovisiona automaticamente quatro dispositivos de exemplo que pode ver na lista de dispositivos. Estes dispositivos são *dispositivos simulados* em execução num Trabalho Web do Azure. Os dispositivos simulados permitem-lhe testar facilmente a solução pré-configurada sem ser necessário implementar dispositivos físicos reais. Se quiser ligar um dispositivo real à solução, veja o tutorial [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm].

Os seguintes passos mostram como adicionar um dispositivo simulado à solução:

1. Navegue de volta para a lista de dispositivos.
2. Para adicionar um dispositivo, clique em **+ Adicionar Um Dispositivo**, no canto inferior esquerdo.
   
   ![Adicionar um dispositivo à solução pré-configurada][img-adddevice]
3. Clique em **Adicionar Novo** no mosaico **Dispositivo Simulado**.
   
   ![Definir novos detalhes de dispositivos no dashboard][img-addnew]
   
   Para além de criar um novo dispositivo simulado, pode ainda adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para saber mais sobre a ligação de dispositivos físicos à solução, consulte [Connect your device to the IoT Suite remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota do IoT Suite)][lnk-connect-rm].
4. Selecione **Autorizar-me a definir o meu próprio ID do Dispositivo** e introduza um nome de ID único para o dispositivo, como **mydevice_01**.
5. Clique em **Criar**.
   
   ![Guardar um novo dispositivo][img-definedevice]
6. No passo 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para regressar à lista de dispositivos.
7. Pode ver o seu dispositivo **Em execução** na lista de dispositivos.
   
    ![Ver o novo dispositivo na lista de dispositivos][img-runningnew]
8. Pode ainda ver a telemetria simulada do seu novo dispositivo no dashboard:
   
    ![Ver telemetria do novo dispositivo][img-runningnew-2]

## <a name="device-properties"></a>Propriedades do dispositivo
A solução pré-configurada de monitorização remota utiliza [dispositivos duplos][lnk-device-twin] para sincronizar os metadados do dispositivo entre dispositivos e o back-end da solução. Os dispositivos duplos são documentos JSON armazenados num Hub IoT que armazena os valores das propriedades de dispositivos individuais. Os dispositivos enviam regularmente metadados para o back-end da solução como *propriedades comunicadas* para serem armazenados no dispositivo duplo. O back-end da solução pode definir as *propriedades pretendidas* no dispositivo duplo para enviar atualizações de metadados aos dispositivos. As propriedades comunicadas mostram os valores de metadados mais recentes enviados pelo dispositivo. Para obter mais informações, veja [Registo de identidade do dispositivo, dispositivos duplos e DocumentDB][lnk-devicemetadata].

> [!NOTE]
> A solução também utiliza uma base de dados do DocumentDB para armazenar dados específicos dos dispositivos relacionados com comandos e métodos.
> 
> 

1. Navegue de volta para a lista de dispositivos.
2. Selecione o seu dispositivo novo na **Lista de Dispositivos** e, em seguida, clique em **Editar** para editar **Dispositivo Duplo - Propriedades Pretendidas**:
   
   ![Editar as propriedades pretendidas do dispositivo][img-editdevice]
3. Defina o **Nome de Propriedade Pretendida** como **Latitude** e defina o valor como **47.639521**. Em seguida, clique em **Guardar as Alterações no Registo do Dispositivo**:
   
    ![Editar a propriedade pretendida do dispositivo][img-editdevice2]
4. No painel **Detalhes do Dispositivo**, o valor novo da latitude é mostrado, inicialmente, como uma propriedade pretendida e o valor antigo como uma propriedade comunicada:
   
    ![Ver propriedades comunicadas][img-editdevice3]
5. Atualmente, os dispositivos simulados na solução pré-configurada só processam as propriedades pretendidas **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval**. Os dispositivos reais devem ler todas as propriedades pretendidas do Hub IoT, fazer as alterações à configuração das mesmas e comunicar os valores novos ao hub como propriedades comunicadas.

No painel **Detalhes do Dispositivo**, também pode editar **Dispositivo Duplo - Etiquetas** da mesma forma que edita **Dispositivo Duplo - Propriedades Pretendidas**. No entanto, ao contrário das propriedades pretendidas, as etiquetas não são sincronizadas com o dispositivo. As etiquetas só existem no dispositivo duplo no hub IoT. As etiquetas são úteis para criar filtros personalizados na lista de dispositivos.

## <a name="sort-the-device-list"></a>Ordenar a lista de dispositivos

Pode clicar junto a um cabeçalho de coluna para ordenar a lista de dispositivos. O primeiro clique ordena por ordem ascendente; o segundo, por ordem descendente:

![Ordenar a lista de dispositivos][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Na lista de dispositivos, pode criar, guardar e recarregar filtros para apresentar uma lista personalizada de dispositivos ligados ao seu hub. Para criar um filtro:

1. Clique no ícone de edição de filtro acima da lista de dispositivos:
   
   ![Abra o editor de filtros][img-editfiltericon]
2. No **Editor de filtros**, adicione os campos, os operadores e os valores para filtrar a lista de dispositivos. Pode adicionar várias cláusulas para refinar o filtro. Clique em **Filtro** para aplicá-lo:
   
   ![Criar um filtro][img-filtereditor]
3. Neste exemplo, a lista é filtrada por fabricante e número do modelo:
   
   ![Lista filtrada][img-filterelist]
4. Para guardar o seu filtro com um nome personalizado, clique no ícone **Guardar como**:
   
   ![Guardar um filtro][img-savefilter]
5. Para reaplicar um filtro que guardou anteriormente, clique no ícone **Abrir filtro guardado**:
   
   ![Abrir um filtro][img-openfilter]

Pode criar filtros com base no id dos dispositivos, no estado dos mesmos, nas propriedades pretendidas, nas propriedades comunicadas e nas etiquetas.

> [!NOTE]
> No **Editor de filtros**, pode utilizar a **Vista avançada** para editar o texto da consulta diretamente.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Adicionar uma regra para o novo dispositivo
Não existem quaisquer regras para o novo dispositivo que acabou de adicionar. Nesta secção, irá adicionar uma regra que aciona um alarme quando a temperatura comunicada pelo novo dispositivo for superior a 47 graus. Antes de começar, tenha em atenção que o histórico de telemetria para o novo dispositivo no dashboard mostra que a temperatura do dispositivo nunca excede 45 graus.

1. Navegue de volta para a lista de dispositivos.
2. Para adicionar uma regra para o dispositivo, selecione o seu dispositivo novo na **Lista de Dispositivos** e, em seguida, clique em **Adicionar regra**.
3. Crie uma regra que utiliza a **Temperatura** como o campo de dados e utiliza a **AlarmTemp** como resultado quando a temperatura for superior a 47 graus:
   
    ![Adicionar uma regra de dispositivo][img-adddevicerule]
4. Para guardar as alterações, clique em **Guardar e Ver Regras**.
5. Clique em **Comandos** no painel Detalhes do dispositivo para o novo dispositivo.
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule2]
6. Selecione **ChangeSetPointTemp** a partir da lista de comandos e defina a **SetPointTemp** para 45. Em seguida, clique em **Enviar Comando**:
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule3]
7. Navegue de solta para o dashboard de solução. Após um curto período de tempo, verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura comunicada pelo seu dispositivo novo exceder o limiar de 47 graus:
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule4]
8. Pode rever e editar todas as suas regras na página **Regras** do dashboard:
   
    ![Listar regras do dispositivo][img-rules]
9. Pode rever e editar todas as ações que podem ser executadas em resposta a uma regra na página **Ações** do dashboard:
   
    ![Listar ações do dispositivo][img-actions]

> [!NOTE]
> É possível definir ações que podem enviar um e-mail ou um SMS em resposta a uma regra ou integrar com um sistema de linha de negócio através de uma [Aplicação Lógica][lnk-logic-apps]. Para obter mais informações, veja [Connect Logic App to your Azure IoT Suite Remote Monitoring preconfigured solution (Ligar Aplicação Lógica à sua solução pré-configurada de Monitorização Remota do Azure IoT Suite)][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Desativar e remover dispositivos
Pode desativar um dispositivo e removê-lo após a desativação:

![Desativar e remover um dispositivo][img-disable]

## <a name="run-jobs"></a>Executar tarefas
Pode agendar tarefas para realizar operações em massa nos seus dispositivos. As tarefas são criadas para uma lista de dispositivos. Esta lista pode conter todos os seus dispositivos ou pode ser uma lista filtrada que criou com as [ferramentas do filtro](#filter-the-device-list) na lista de dispositivos. Uma tarefa pode invocar um método em todos os dispositivos na lista ou pode atualizar o dispositivo duplo de cada dispositivo a lista.

### <a name="create-a-job-to-invoke-a-method"></a>Criar uma tarefa para invocar um método

Os passos seguintes mostram-lhe como criar uma tarefa que invoca o método de atualização de firmware em todos os dispositivos numa lista. O método só é invocado em dispositivos que o suportem:

1. Utilize as ferramentas de filtro na lista de dispositivos para criar uma lista de dispositivos que vão receber a atualização de firmware:
   
   ![Abra o editor de filtros][img-editfiltericon]
2. Na lista filtrada, clique em **Agendador de tarefas**:
   
   ![Abrir o agendador de tarefas][img-clickjobscheduler]
3. No painel **Tarefa agendada**, clique em **Método de Invocação**.
4. Na página **Invocar Método**, introduza os detalhes do método a invocar e, em seguida, clique em **Agendar**:
   
   ![Configurar a tarefa de método][img-invokemethodjob]

O método **InitiateFirmwareUpdate** inicia uma tarefa de forma assíncrona no dispositivo e devolve imediatamente. Depois, o processo de atualização do firmware utiliza as propriedades comunicadas para comunicar o processo de atualização à medida que é executado.

### <a name="create-a-job-to-edit-the-device-twin"></a>Criar uma tarefa para editar o dispositivo duplo

Os passos seguintes mostram-lhe como criar uma tarefa que edita o dispositivo duplo em todos os dispositivos numa lista:

1. Utilize as ferramentas de filtro na lista de dispositivos para criar uma lista de dispositivos que vão receber as edições do dispositivo duplo:
   
   ![Abra o editor de filtros][img-editfiltericon]
2. Na lista filtrada, clique em **Agendador de tarefas**:
   
   ![Abrir o agendador de tarefas][img-clickjobscheduler]
3. No painel **Tarefa agendada**, clique em **Editar Dispositivo Duplo**.
4. Na página **Editar Dispositivo Duplo**, introduza os detalhes das **Propriedades Pretendidas** e das **Etiquetas** a editar e, em seguida, clique em **Agendar**:
   
   ![Configurar a tarefa de método][img-edittwinjob]

### <a name="monitor-the-job"></a>Monitorizar a tarefa
Pode monitorizar o estado das tarefas que agendar na página **Tarefas de Gestão**, O painel **Detalhes da Tarefa** apresenta informações sobre a tarefa selecionada:
   
   ![Ver o estado de tarefa][img-jobstatus]

Também pode ver informações sobre as tarefas no **Dashboard**:
   
   ![Ver tarefas no dashboard][img-jobdashboard]


## <a name="behind-the-scenes"></a>Nos bastidores
Quando implementar uma solução pré-configurada, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Pode ver estes recursos no [Portal][lnk-portal] do Azure. O processo de implementação cria um **grupo de recursos** com um nome baseado no nome que escolheu para a sua solução pré-configurada:

![Solução pré-configurada no portal do Azure][img-portal]

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Pode ainda ver o código de origem para a solução pré-configurada. Encontrará o código fonte da solução pré-configurada de monitorização remota no repositório do GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* A pasta **DeviceAdministration** contém o código de origem para o dashboard.
* A pasta **Simulator** contém o código de origem para o dispositivo simulado.
* A pasta **EventProcessor** contém o código de origem para o processo de back-end que processa a telemetria de entrada.

Quando terminar, pode eliminar a solução pré-configurada da sua subscrição do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Este site permite-lhe eliminar facilmente todos os recursos que foram aprovisionados quando criou a solução pré-configurada.

> [!NOTE]
> Para garantir que elimina tudo o que está relacionado com a solução pré-configurada, elimine-a no site [azureiotsuite.com][lnk-azureiotsuite] e não elimine o grupo de recursos no portal.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Agora que implementou uma solução pré-configurada funcional, pode ler os artigos seguintes para continuar a introdução ao IoT Suite:

* [Instruções sobre a solução pré-configurada de monitorização remota][lnk-rm-walkthrough]
* [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
