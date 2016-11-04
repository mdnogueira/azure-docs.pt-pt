---
title: Introdução às soluções pré-configuradas | Microsoft Docs
description: Siga este tutorial para saber como implementar uma solução pré-configurada do Azure IoT Suite.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: dobett

---
# Tutorial: Introdução às soluções pré-configuradas
## Introdução
As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam múltiplos serviços do Azure IoT para fornecer soluções ponto-a-ponto que implementam cenários de negócios comuns do IoT. A solução pré-configurada de *monitorização remota* estabelece ligação aos seus dispositivos e monitoriza-os. Pode utilizar a solução para analisar o fluxo de dados dos seus dispositivos e melhorar os resultados de negócios ao fazer com que os processos respondam automaticamente a esse fluxo de dados.

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada de monitorização remota. Também fornece orientações sobre as funcionalidades básicas da solução de monitorização remota. Pode aceder a muitas destas funcionalidades através do dashboard da solução pré-configurada que é implementado juntamente com a mesma:

![Dashboard da solução pré-configurada de monitorização remota][img-dashboard]

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [!NOTE]
> Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Ver o dashboard da solução
O dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver a telemetria, adicionar dispositivos e configurar regras.

1. Quando o aprovisionamento estiver concluído e o mosaico da sua solução pré-configuradas indicar **Pronto**, clique em **Iniciar** para abrir o seu portal de solução de monitorização remota num novo separador.
   
   ![Iniciar a solução pré-configurada][img-launch-solution]
2. Por predefinição, o portal de solução mostra o *dashboard de solução*. Pode selecionar outras vistas utilizando o menu da esquerda.
   
   ![Dashboard da solução pré-configurada de monitorização remota][img-dashboard]

O dashboard apresenta as seguintes informações:

* O mapa apresenta a localização de cada dispositivo ligado à solução. Quando executar a solução pela primeira vez, irá ver quatro dispositivos simulados. Os dispositivos simulados são implementados como WebJobs do Azure e a solução utiliza a API do Bing Maps para rastrear informações no mapa.
* O painel **Histórico de Telemetria** rastreia em tempo real a humidade e a telemetria de temperatura de um dispositivo selecionado e mostra os dados agregados, como a humidade máxima, mínima e média.
* O painel **Histórico de Alarme** mostra eventos de alarme recentes quando um valor de telemetria ultrapassar um limiar. Para além dos exemplos criados pela solução pré-configurada, pode ainda definir os seus próprios alarmes.

## Ver a lista de dispositivos
A lista de dispositivos mostra todos os dispositivos registados na solução. Pode ver e editar os metadados do dispositivo, adicionar ou remover dispositivos e enviar comandos aos dispositivos.

1. Clique em **Dispositivos** no menu da esquerda para mostra a *lista de dispositivos* para esta solução.
   
   ![Lista de dispositivos no dashboard][img-devicelist]
2. A lista de dispositivos mostra que existem quatro dispositivos simulados criados pelo processo de aprovisionamento.
3. Clique num dispositivo da lista de dispositivos para ver os detalhes do dispositivo.
   
   ![Detalhes de dispositivos no dashboard][img-devicedetails]

O painel **Detalhes do Dispositivo** é constituído por três secções:

* A secção **Ações** lista as ações que pode efetuar no dispositivo. Se desativar o dispositivo, deixa de ser possível enviar telemetria ou receber comandos. Se desativar um dispositivo, poderá ativá-lo novamente mais tarde. Pode adicionar uma regra associada ao dispositivo que aciona um alarme quando um valor de telemetria excede um limiar. Também pode enviar um comando a um dispositivo. Quando um dispositivo estabelecer a ligação pela primeira vez, irá indicar os comandos aos quais a solução pode responder.
* A secção **Propriedades do Dispositivo** lista os metadados do dispositivo. Alguns dos metadados provêm do próprio dispositivo (como o fabricante), sendo alguns gerados pela solução (como a hora de criação). Pode editar os metadados do dispositivo a partir daqui.
* A secção **Chaves de Autenticação** lista as chaves que o dispositivo pode utilizar para autenticar-se na solução.

## Enviar um comando a um dispositivo
O painel Detalhes do dispositivo mostra todos os comandos que um dispositivo específico suporta e permite-lhe enviar comandos a um dispositivo. Quando um dispositivo é iniciado pela primeira vez, envia informações sobre os comandos que suporta à solução.

1. Clique em **Comandos** no painel Detalhes do dispositivo para o dispositivo selecionado.
   
   ![Comandos de dispositivos no dashboard][img-devicecommands]
2. Selecione **PingDevice** na lista de comandos.
3. Clique em **Enviar Comando**.
4. Pode ver o estado do comando no histórico do comando.
   
   ![Estado do comando no dashboard][img-pingcommand]

A solução controla o estado de cada comando que envia. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido para **Êxito**.

## Adicionar um novo dispositivo simulado
Ao implementar a solução pré-configurada, aprovisiona automaticamente os quatro dispositivos de exemplo que pode ver na lista de dispositivos. Estes dispositivos são *dispositivos simulados* em execução num Trabalho Web do Azure. Os dispositivos simulados permitem-lhe testar facilmente a solução pré-configurada sem ser necessário implementar dispositivos físicos reais. Se quiser ligar um dispositivo real à solução, veja o tutorial [Ligar o seu dispositivo à solução pré-configurada de monitorização remota][lnk-connect-rm].

Os seguintes passos mostram como adicionar um dispositivo simulado à solução:

1. Navegue de volta para a lista de dispositivos.
2. Clique em **+ Adicionar Um Dispositivo** no canto inferior esquerdo para adicionar um dispositivo.
   
   ![Adicionar um dispositivo à solução pré-configurada][img-adddevice]
3. Clique em **Adicionar Novo** no mosaico **Dispositivo Simulado**.
   
   ![Definir novos detalhes de dispositivos no dashboard][img-addnew]
   
   Para além de criar um novo dispositivo simulado, pode ainda adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para saber mais sobre a ligação de dispositivos físicos à solução, consulte [Ligar o seu dispositivo à solução pré-configurada de monitorização remota do IoT Suite][lnk-connect-rm].
4. Selecione **Autorizar-me a definir o meu próprio ID do Dispositivo** e introduza um nome de ID único para o dispositivo, como **mydevice_01**.
5. Clique em **Criar**.
   
   ![Guardar um novo dispositivo][img-definedevice]
6. No passo 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para regressar à lista de dispositivos.
7. Pode ver o seu dispositivo **Em execução** na lista de dispositivos.
   
    ![Ver o novo dispositivo na lista de dispositivos][img-runningnew]
8. Pode ainda ver a telemetria simulada do seu novo dispositivo no dashboard:
   
    ![Ver telemetria do novo dispositivo][img-runningnew-2]

## Editar os metadados do dispositivo
Quando um dispositivo estabelece ligação à solução pela primeira vez, este envia os respetivos metadados para a solução. Quando edita os metadados do dispositivo através do dashboard da solução, este envia os novos valores de metadados para o dispositivo e armazena os novos valores na base de dados DocumentDB da solução. Para obter mais informações, veja [Registo de identidade do dispositivo e DocumentDB][lnk devicemetadata].

1. Navegue de volta para a lista de dispositivos.
2. Selecione o novo dispositivo na **Lista de Dispositivos** e, em seguida, clique em **Editar** para editar as **Propriedades do Dispositivo**:
   
   ![Editar metadados do dispositivo][img-editdevice]
3. Desloque-se para baixo e altere os valores latitude e longitude. Em seguida, clique em **Guardar as alterações no registo do dispositivo**.
   
    ![Editar metadados do dispositivo][img-editdevice2]
4. Navegue de volta para o dashboard, a localização do dispositivo foi alterada no mapa:
   
    ![Editar metadados do dispositivo][img-editdevice3]

## Adicionar uma regra para o novo dispositivo
Não existem quaisquer regras para o novo dispositivo que acabou de adicionar. Nesta secção, irá adicionar uma regra que aciona um alarme quando a temperatura comunicada pelo novo dispositivo for superior a 47 graus. Antes de começar, tenha em atenção que o histórico de telemetria para o novo dispositivo no dashboard mostra que a temperatura do dispositivo nunca excede 45 graus.

1. Navegue de volta para a lista de dispositivos.
2. Selecione o seu novo dispositivo na **Lista de Dispositivos** e, em seguida, clique em **Adicionar regra** para adicionar uma regra para o dispositivo.
3. Crie uma regra que utiliza a **Temperatura** como o campo de dados e utiliza a **AlarmTemp** como resultado quando a temperatura for superior a 47 graus:
   
    ![Adicionar uma regra de dispositivo][img-adddevicerule]
4. Clique em **Guardar e Ver Regras** para guardar as alterações.
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

## Outras funcionalidades
Através do portal de solução, pode pesquisar dispositivos com características específicas, tais como um número de modelo:

![Pesquisar um dispositivo][img-search]

Pode desativar um dispositivo e removê-lo após a desativação:

![Desativar e remover um dispositivo][img-disable]

## Nos bastidores
Quando implementar uma solução pré-configurada, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Pode ver estes recursos no [Portal][lnk-portal] do Azure. O processo de implementação cria um **grupo de recursos** com um nome baseado no nome que escolheu para a sua solução pré-configurada:

![Solução pré-configurada no portal do Azure][img-portal]

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Pode ainda ver o código de origem para a solução pré-configurada. Encontrará o código fonte da solução pré-configurada de monitorização remota no repositório do GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* A pasta **DeviceAdministration** contém o código de origem para o dashboard.
* A pasta **Simulator** contém o código de origem para o dispositivo simulado.
* A pasta **EventProcessor** contém o código de origem para o processo de back-end que processa a telemetria de entrada.

Quando terminar, pode eliminar a solução pré-configurada da sua subscrição do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Este site permite-lhe eliminar facilmente todos os recursos que foram aprovisionados quando criou a solução pré-configurada.

> [!NOTE]
> Para garantir que elimina tudo o que está relacionado com a solução pré-configurada, elimine no site [azureiotsuite.com][lnk-azureiotsuite] em vez de eliminar o grupo de recursos no portal.
> 
> 

## Passos Seguintes
Agora que implementou uma solução pré-configurada funcional, pode ler os artigos seguintes para continuar a introdução ao IoT Suite:

* [Instruções sobre a solução pré-configurada de monitorização remota][lnk-rm-walkthrough]
* [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm]
* [Permissions on the azureiotsuite.com site (Permissões no site azureiotsuite.com)][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
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
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md



<!--HONumber=Sep16_HO3-->


