<properties
    pageTitle="Introdução às soluções pré-configuradas | Microsoft Azure"
    description="Siga este tutorial para saber como implementar uma solução pré-configurada do Azure IoT Suite."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# Tutorial: Introdução às soluções pré-configuradas

## Introdução

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam múltiplos serviços do Azure IoT para fornecer soluções ponto-a-ponto que implementam cenários de negócios comuns do IoT.

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada *monitorização remota*. Também fornece orientações sobre as funcionalidades básicas da monitorização da solução pré-configurada.

Para concluir este tutorial, irá precisar de uma subscrição ativa do Azure.

> [AZURE.NOTE]  Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure][lnk_free_trial].

## Aprovisionar a solução pré-configurada de monitorização remota

1.  Inicie sessão em [azureiotsuite.com][Ink-azureiotsuite] com as credenciais da sua conta Azure e clique em **+** para criar uma nova solução.

    > [AZURE.NOTE] Se tiver problemas com as permissões necessárias para aprovisionar uma solução, consulte o artigo [Permissões no site azureiotsuite.com][lnk-permissions] para obter orientações.

2.  Clique em **Selecionar** no mosaico **Monitorização remota**.

3.  Introduza o **Nome da solução** para sua solução pré-configurada de monitorização remota.

4.  Selecione a **Região** e a **Subscrição** que pretende utilizar para aprovisionar a solução.

5.  Clique em **Criar Solução** para iniciar o processo de aprovisionamento. Normalmente, esta ação demora vários minutos para ser executada.

## Aguarde até a conclusão do processo de aprovisionamento

1. Clique no mosaico da sua solução com o estado **Aprovisionamento**.
 
2. Tenha em atenção os **Estados de aprovisionamento** uma vez que os serviços do Azure estão implementados na sua subscrição do Azure.

3. Depois de ter concluído o aprovisionamento, o estado passa para **Pronto**.

4. Clique no mosaico e irá ver os detalhes da sua solução no painel da direita.

> [AZURE.NOTE] Se tiver problemas com a implementação da solução pré-configurada, consulte o artigo [Permissões no site azureiotsuite.com][Ink-permissions] e as [FAQ][Ink-faq]. Se os problemas persistirem, crie um pedido de serviço no [Portal][Ink-portal].

Pretendia ver certos detalhes que não se encontram listados para a sua solução? Dê-nos a suas sugestões de funcionalidades através de [A Voz do Utilizador](https://feedback.azure.com/forums/321918-azure-iot).

## Veja o dashboard da solução de monitorização remota

O dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver a telemetria, adicionar dispositivos e configurar regras.

1.  Quando o aprovisionamento estiver concluído e o mosaico da sua solução pré-configuradas indicar **Pronto**, clique em **Iniciar** para abrir o seu portal de solução de monitorização remota num novo separador.

    ![][img-launch-solution]

2.  Por predefinição, o portal de solução mostra o *dashboard de solução*. Pode selecionar outras vistas utilizando o menu da esquerda.

    ![][img-dashboard]

O dashboard apresenta as seguintes informações:

- O mapa apresenta a localização de cada dispositivo ligado à solução. Quando executar a solução pela primeira vez, irá ver quatro dispositivos simulados. Os dispositivos simulados são implementados como WebJobs do Azure e a solução utiliza a API do Bing Maps para rastrear informações no mapa.
- O painel **Histórico de Telemetria** rastreia em tempo real a humidade e a telemetria de temperatura de um dispositivo selecionado e mostra os dados agregados, como a humidade máxima, mínima e média.
- O painel **Histórico de Alarme** mostra eventos de alarme recentes quando um valor de telemetria ultrapassar um limiar. Para além dos exemplos criados pela solução pré-configurada, pode ainda definir os seus próprios alarmes.

## Ver lista de dispositivos na solução

A lista de dispositivos mostra todos os dispositivos registados na solução. Pode ver e editar os metadados do dispositivo, adicionar ou remover dispositivos e enviar comandos aos dispositivos.

1.  Clique em **Dispositivos** no menu da esquerda para mostra a *lista de dispositivos* para esta solução.

    ![][img-devicelist]

2.  A lista de dispositivos mostra que existem quatro dispositivos simulados criados pelo processo de aprovisionamento.

3.  Clique num dispositivo da lista de dispositivos para ver os detalhes do dispositivo.

    ![][img-devicedetails]

O painel **Detalhes do Dispositivo** é constituído por três secções:

- A secção **Ações** lista as ações que pode efetuar no dispositivo. Se desativar o dispositivo, deixará de ser possível enviar telemetria ou receber comandos. Se desativar um dispositivo, poderá ativá-lo novamente mais tarde. Pode adicionar uma regra associada ao dispositivo que aciona um alarme quando um valor de telemetria excede um limiar. Também pode enviar um comando a um dispositivo. Quando um dispositivo estabelecer a ligação pela primeira vez, irá indicar os comandos aos quais a solução pode responder.
- A secção **Propriedades do Dispositivo** lista os metadados do dispositivo. Alguns dos metadados provêm do próprio dispositivo (como o fabricante), sendo alguns gerados pela solução (como a hora de criação). Pode editar os metadados do dispositivo a partir daqui.
- A secção **Chaves de Autenticação** lista as chaves que o dispositivo pode utilizar para autenticar-se na solução.

## Enviar um comando a um dispositivo

O painel Detalhes do dispositivo mostra todos os comandos que um dispositivo específico suporta e permite-lhe enviar comandos a um dispositivo. Quando um dispositivo é iniciado pela primeira vez, envia informações sobre os comandos que suporta à solução.

1.  Clique em **Comandos** no painel Detalhes do dispositivo para o dispositivo selecionado.

    ![][img-devicecommands]

2.  Selecione **PingDevice** na lista de comandos.

3.  Clique em **Enviar Comando**.

4.  Pode ver o estado do comando no histórico do comando.

    ![][img-pingcommand]

A solução controla o estado de cada comando que envia. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido para **Êxito**.

## Adicionar um novo dispositivo simulado

1.  Navegue de volta para a lista de dispositivos.

2.  Clique em **+ Adicionar Um Dispositivo** no canto inferior esquerdo para adicionar um novo dispositivo.

    ![][img-adddevice]

3.  Clique em **Adicionar Novo** no mosaico **Dispositivo Simulado**.

    ![][img-addnew]
    
    Para além de criar um novo dispositivo simulado, pode ainda adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para obter mais informações sobre esta funcionalidade, consulte o artigo [Ligar o seu dispositivo à solução pré-configurada de monitorização remota do IoT Suite][Ink-connecting-devices].

4.  Selecione **Autorizar-me a definir o meu próprio ID do Dispositivo** e introduza um nome de ID único para o dispositivo, como **mydevice_01**.

5.  Clique em **Criar**.

    ![][img-definedevice]

6. No passo 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para regressar à lista de dispositivos.

7. Pode ver o seu dispositivo **Em execução** na lista de dispositivos.

    ![][img-runningnew]

8. Pode ainda ver a telemetria simulada do seu novo dispositivo no dashboard:

    ![][img-runningnew-2]

## Editar os metadados do dispositivo

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione o novo dispositivo na **Lista de Dispositivos** e, em seguida, clique em **Editar** para editar as **Propriedades do Dispositivo**:

    ![][img-editdevice]

3. Desloque-se para baixo e altere os valores latitude e longitude. Em seguida, clique em **Guardar as alterações no registo do dispositivo**.

    ![][img-editdevice2]

4. Navegue de volta para o dashboard, a localização do dispositivo foi alterada no mapa:

    ![][img-editdevice3]

## Adicionar uma regra para o novo dispositivo

Não existem quaisquer regras para o novo dispositivo que acabou de adicionar. Esta secção irá adicionar uma regra que aciona um alarme quando a temperatura comunicada pelo novo dispositivo for superior a 47 graus. Antes de começar, tenha em atenção que o histórico de telemetria para o novo dispositivo no dashboard mostra que a temperatura do dispositivo nunca excede 45 graus.

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione o seu novo dispositivo na **Lista de Dispositivos** e, em seguida, clique em **Adicionar regra** para adicionar uma nova regra para o dispositivo.

3. Crie uma regra que utiliza a **Temperatura** como o campo de dados e utiliza a **AlarmTemp** como resultado quando a temperatura for superior a 47 graus:

    ![][img-adddevicerule]

4. Clique em **Guardar e Ver Regras** para guardar as alterações.

5.  Clique em **Comandos** no painel Detalhes do dispositivo para o novo dispositivo.

    ![][img-adddevicerule2]

6.  Selecione **ChangeSetPointTemp** a partir da lista de comandos e defina a **SetPointTemp** para 45. Em seguida, clique em **Enviar Comando**:

    ![][img-adddevicerule3]

7.  Navegue de solta para o dashboard de solução. Após um curto período de tempo, verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura comunicada pelo seu dispositivo novo exceder o limiar de 47 graus:

    ![][img-adddevicerule4]

8. Pode rever e editar todas as suas regras na página **Regras** do dashboard:

    ![][img-rules]

9. Pode rever e editar todas as ações que podem ser executadas em resposta a uma regra na página **Ações** do dashboard:

    ![][img-actions]

> [AZURE.NOTE] É possível definir ações que podem enviar um e-mail ou um SMS em resposta a uma regra ou integrar com um sistema de linha de negócio através de uma [Aplicação Lógica][Ink-logic-apps].

## Nos bastidores

Quando implementar uma solução pré-configurada, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Pode ver estes recursos no [Portal][Ink-portal] do Azure. O processo de implementação cria um **grupo de recursos** com um nome com base no nome que escolheu para a sua solução pré-configurada:

![][img-portal]

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos. A captura de ecrã acima mostra as definições para o IoT Hub utilizado na solução pré-configurada.

Pode ainda ver o código de origem para a solução pré-configurada. Encontrará o código de origem da solução de monitorização remota pré-configurada em [azure-iot-remote-monitoring][Ink-rmgithub]:

- A pasta **DeviceAdministration** contém o código de origem para o dashboard.
- A pasta **Simulator** contém o código de origem para o dispositivo simulado.
- A pasta **EventProcessor** contém o código de origem para o processo de back-end que processa a telemetria de entrada.

Quando tiver terminado, pode eliminar a solução pré-configurada a partir da sua subscrição do Azure no site [azureiotsuite.com][Ink-azureiotsuite] - esta ação permite-lhe eliminar facilmente todos os recursos que tenham sido aprovisionados aquando da criação da solução pré-configurada.

> [AZURE.NOTE] Para garantir elimine tudo o que estiver relacionado com a solução pré-configurada, elimine a partir do [azureiotsuite.com][Ink azureiotsuite] e não elimine o grupo de recursos no portal.

## Passos Seguintes

Agora que criou uma solução pré-configurada operacional, pode passar para as seguintes instruções:

-   [Orientações sobre a personalização de soluções pré-configuradas][Ink-customize]
-   [Descrição geral da manutenção preditiva da solução pré-configurada][Ink-predictive]

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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md



<!--HONumber=Jun16_HO2-->


