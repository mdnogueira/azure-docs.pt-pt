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
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 466825ab78a5ac9773d8beff69cca90ff9db6c01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Introdução às soluções pré-configuradas

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam múltiplos serviços do Azure IoT para fornecer soluções ponto-a-ponto que implementam cenários de negócios comuns do IoT. A solução pré-configurada de *monitorização remota* estabelece ligação aos seus dispositivos e monitoriza-os. Pode utilizar a solução para analisar o fluxo de dados dos seus dispositivos e melhorar os resultados de negócios ao fazer com que os processos respondam automaticamente a esse fluxo de dados.

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada de monitorização remota. Também disponibiliza orientações sobre as funcionalidades básicas da solução pré-configurada. Pode aceder a muitas destas funcionalidades através do *dashboard* da solução pré-configurada que é implementado juntamente com a mesma:

![Dashboard da solução pré-configurada de monitorização remota][img-dashboard]

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

> [!NOTE]
> Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Azure Free Trial (Avaliação Gratuita do Azure)][lnk_free_trial].

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Descrição geral do cenário

Ao implementar a solução pré-configurada de monitorização remota, a mesma é pré-preenchida com recursos que permitem-lhe seguir um cenário típico de monitorização remota. Neste cenário, vários dispositivos ligados à solução estão a denunciar valores de temperatura inesperados. As secções seguintes mostram como:

* Identificar os dispositivos que estão a enviar valores de temperatura inesperados.
* Configurar estes dispositivos para enviar telemetria mais detalhada.
* Resolver o problema ao atualizar o firmware nestes dispositivos.
* Certificar-se de que a ação resolveu o problema.

Uma funcionalidade chave deste cenário é que pode efetuar todas estas ações remotamente a partir do dashboard da solução. Não precisa de acesso físico aos dispositivos.

## <a name="view-the-solution-dashboard"></a>Ver o dashboard da solução

O dashboard de solução permite-lhe gerir a solução implementada. Por exemplo, pode ver a telemetria, adicionar dispositivos e configurar regras.

1. Quando o aprovisionamento estiver concluído e o mosaico da sua solução pré-configurada indicar **Pronto**, escolha **Iniciar** para abrir o seu portal de solução de monitorização remota num novo separador.

    ![Iniciar a solução pré-configurada][img-launch-solution]

1. Por predefinição, o portal de solução mostra o *dashboard*. Pode navegar para outras áreas do portal de solução com o menu no lado esquerdo da página.

    ![Dashboard da solução pré-configurada de monitorização remota][img-menu]

O dashboard apresenta as seguintes informações:

* Um mapa que apresenta a localização de cada dispositivo ligado à solução. Quando executar a solução pela primeira vez, irá ver 25 dispositivos simulados. Os dispositivos simulados são implementados como WebJobs do Azure e a solução utiliza a API do Bing Maps para rastrear informações no mapa. Consulte as [FAQ][lnk-faq] para saber como tornar o mapa dinâmico.
* Um painel **Histórico de Telemetria** que rastreia em tempo real a humidade e a telemetria de temperatura de um dispositivo selecionado e mostra os dados agregados, como a humidade máxima, mínima e média.
* Um painel **Histórico de Alarme** que mostra eventos de alarme recentes quando um valor de telemetria ultrapassar um limiar. Para além dos exemplos criados pela solução pré-configurada, pode ainda definir os seus próprios alarmes.
* Um painel **Tarefas** que apresenta informações sobre as tarefas agendadas. Pode agendar as suas próprias tarefas na página **Tarefas de gestão**.

## <a name="view-alarms"></a>Ver alarmes

O painel do histórico de alarme mostra-lhe cinco dispositivos que estão a denunciar valores de telemetria mais elevados do que o esperado.

![Histórico de alarme TODO no dashboard de solução][img-alarms]

> [!NOTE]
> Estes alarmes são gerados por uma regra que está incluída na solução pré-configurada. Esta regra gera um alerta quando o valor de temperatura enviado por um dispositivo excede 60. Pode definir as suas próprias regras e ações ao escolher [Regras](#add-a-rule) e [Ações](#add-an-action) no menu do lado esquerdo.

## <a name="view-devices"></a>Ver dispositivos

A lista de *dispositivos* mostra todos os dispositivos registados na solução. Na lista de dispositivos, pode ver e editar os metadados dos dispositivos, adicionar ou remover dispositivos e invocar métodos nos mesmos. Pode filtrar e ordenar a lista de dispositivos na lista de dispositivos. Também pode personalizar as colunas mostradas na lista de dispositivos.

1. Escolha **Dispositivos** para mostrar a lista de dispositivos desta solução.

   ![Ver a lista de dispositivos no portal da solução][img-devicelist]

1. A lista de dispositivos mostra, inicialmente, 25 dispositivos simulados criados pelo processo de aprovisionamento. Pode adicionar mais dispositivos simulados e físicos à solução.

1. Para ver os detalhes de um dispositivo, escolha um dispositivo na lista de dispositivos.

   ![Ver detalhes dos dispositivos no portal da solução][img-devicedetails]

O painel **Detalhes do Dispositivo** contêm seis secções:

* Uma coleção de ligações que lhe permitem personalizar o ícone do dispositivo, desativá-lo, adicionar regras, invocar métodos ou enviar comandos. Para obter uma comparação dos comandos (mensagens do dispositivo para a cloud) e dos métodos (métodos diretos), veja [Cloud-to-device communications guidance (Orientação sobre comunicações da cloud para dispositivos)][lnk-c2d-guidance].
* A secção **Dispositivo Duplo - Etiquetas** permite-lhe editar os valores das etiquetas do dispositivo. Pode apresentar os valores de etiquetas na lista de dispositivos e utilizá-los para filtrá-la.
* A secção **Dispositivo Duplo - Propriedades Pretendidas** permite-lhe definir os valores das propriedades a serem enviados para o dispositivo.
* A secção **Dispositivo Duplo - Propriedades Comunicadas** mostra os valores das propriedades enviados pelo dispositivo.
* A secção **Propriedades do Dispositivo** mostra informações do registo de identidades, tal como o ID do dispositivo e as chaves de autenticação.
* A secção **Tarefas Recentes** mostra informações sobre todas as tarefas que direcionaram este dispositivo recentemente.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Pode utilizar um filtro para apresentar apenas os dispositivos que estão a enviar valores de temperatura inesperados. A solução pré-configurada de monitorização remota inclui o filtro **Dispositivos em mau estado de funcionamento** para mostrar os dispositivos com um valor de temperatura média superior a 60. Também pode [criar os seus próprios filtros](#add-a-filter).

1. Escolha **Abrir filtro guardado** para apresentar uma lista dos filtros disponíveis. Em seguida, escolha **Dispositivos em mau estado de funcionamento** para aplicar o filtro:

    ![Apresentar a lista de filtros][img-unhealthy-filter]

1. A lista de dispositivos mostra agora apenas os dispositivos com um valor de temperatura média superior a 60.

    ![Ver a lista de dispositivos filtrados que mostra os dispositivos em mau estado de funcionamento][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Atualizar as propriedades pretendidas

Identificou um conjunto de dispositivos que poderão necessitar de remediação. No entanto, decide que a frequência de dados de 15 segundos não é suficiente para um diagnóstico claro do problema. Altere a frequência de telemetria para cinco segundos, para lhe fornecer mais pontos de dados para diagnosticar melhor o problema. Pode emitir esta alteração de configuração para os seus dispositivos remotos a partir do portal da solução. Pode efetuar a alteração uma vez, avaliar o impacto e, em seguida, tomar medidas relacionadas com os resultados.

Siga estes passos para executar uma tarefa que altera a propriedade **TelemetryInterval** pretendida para os dispositivos afetados. Quando os dispositivos receberem o novo valor da propriedade **TelemetryInterval**, eles alteram a configuração para enviar telemetria a cada cinco segundos, em vez de a cada 15 segundos:

1. Enquanto estiver a ser mostrada a lista de dispositivos em mau estado de funcionamento na lista de dispositivos, escolha **Agendador de Tarefas** e, em seguida, em **Editar Dispositivo Duplo**.

1. Atribua à tarefa o nome **Alterar intervalo de telemetria**.

1. Altere o valor do nome **Propriedade Pretendida** **desired.Config.TelemetryInterval** para cinco segundos.

1. Escolha **Agenda**.

    ![Alterar a propriedade TelemetryInterval para cinco segundos][img-change-interval]

1. Pode monitorizar o progresso da tarefa na página **Tarefas de Gestão** do portal.

> [!NOTE]
> Se pretender alterar um valor de propriedade pretendida relativa a um dispositivo, utilize a secção **Propriedades Pretendidas** no painel **Detalhes do Dispositivo**, em vez de executar uma tarefa.

Esta tarefa define o valor da propriedade pretendida **TelemetryInterval** no dispositivo duplo para todos os dispositivos selecionados pelo filtro. Os dispositivos obtêm este valor do dispositivo duplo e atualizam o respetivo comportamento. Quando um dispositivo obtém e processa uma propriedade pretendida a partir de um dispositivo duplo, define a propriedade de valor comunicado correspondente.

## <a name="invoke-methods"></a>Invocar métodos

Enquanto a tarefa é executada, vai reparar que, na lista de dispositivos em mau estado de funcionamento, todos estes dispositivos têm versões de firmware antigo (anterior à versão 1.6).

![Ver a versão de firmware comunicada para os dispositivos em mau estado de funcionamento][img-old-firmware]

Esta versão de firmware poderá ser a origem do problema de valores de temperatura inesperados, uma vez que sabe que outros dispositivos em bom estado de funcionamento foram atualizados recentemente para a versão 2.0. Pode utilizar o filtro incorporado **Dispositivos com firmware antigo** para identificar todos os dispositivos com versões de firmware antigas. No portal, pode então atualizar remotamente todos os dispositivos que ainda executam versões de firmware antigas:

1. Escolha **Abrir filtro guardado** para apresentar uma lista dos filtros disponíveis. Em seguida, escolha **Dispositivos com firmware antigo** para aplicar o filtro:

    ![Apresentar a lista de filtros][img-old-filter]

1. A lista de dispositivos mostra agora apenas os dispositivos com versões de firmware antigas. Esta lista inclui os cinco dispositivos identificados pelo filtro **Dispositivos em mau estado de funcionamento** e três dispositivos adicionais:

    ![Ver a lista de dispositivos filtrados que mostra dispositivos antigos][img-filtered-old-list]

1. Escolha **Agendador de Tarefas** e **Invocar Método**.

1. Defina o **Nome da Tarefa** para **Atualização de firmware para a versão 2.0**.

1. Escolha **InitiateFirmwareUpdate** como o **Método**.

1. Defina o parâmetro **FwPackageUri** para **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Escolha **Agenda**. A predefinição é que a tarefa seja executada agora.

    ![Criar tarefa para atualizar o firmware dos dispositivos selecionados][img-method-update]

> [!NOTE]
> Se pretender invocar um método num dispositivo individual, selecione **Métodos** no painel **Detalhes do Dispositivo**, em vez de executar uma tarefa.

Esta tarefa invoca o método direto **InitiateFirmwareUpdate** em todos os dispositivos selecionados pelo filtro. Os dispositivos respondem imediatamente ao Hub IoT e, em seguida, iniciam o processo de atualização de firmware de modo assíncrono. Os dispositivos fornecem informações de estado sobre o processo de atualização de firmware através dos valores de propriedade comunicados, conforme mostrado nas capturas de ecrã seguintes. Escolha o ícone **Atualizar** para atualizar as informações nas listas de dispositivos e de tarefas:

![Lista de tarefas que mostra a lista de atualização de firmware em execução][img-update-1]
![Lista de dispositivos que mostra o estado de atualização de firmware][img-update-2]
![Lista de tarefas que mostra a lista de atualização de firmware concluída][img-update-3]

> [!NOTE]
> Num ambiente de produção, pode agendar as tarefas para serem executadas durante uma janela de manutenção designada.

## <a name="scenario-review"></a>Revisão do cenário

Neste cenário, identificou um problema potencial em alguns dos seus dispositivos remotos, ao utilizar o histórico de alarme no dashboard e um filtro. Em seguida, utilizou o filtro e uma tarefa para configurar remotamente os dispositivos para fornecer mais informações para ajudar a diagnosticar o problema. Finalmente, utilizou um filtro e uma tarefa para agendar a manutenção nos dispositivos afetados. Se voltar ao dashboard, pode verificar que já não existem quaisquer alarmes despoletados por dispositivos na sua solução. Pode utilizar um filtro para verificar que o firmware está atualizado em todos os dispositivos na sua solução e que não existem mais dispositivos em mau estado de funcionamento:

![Filtro que mostra que todos os dispositivos têm firmware atualizado][img-updated]

![Filtro que mostra que todos os dispositivos estão em bom estado de funcionamento][img-healthy]

## <a name="other-features"></a>Outras funcionalidades

As secções seguintes descrevem algumas funcionalidades adicionais da solução pré-configurada de monitorização remota que não são descritas como parte do cenário anterior.

### <a name="customize-columns"></a>Personalizar colunas

Pode personalizar as informações mostradas na lista de dispositivos, ao escolher **Editor de colunas**. Pode adicionar e remover colunas que apresentam valores de etiquetas e de propriedades comunicados. Também pode reorganizar e mudar o nome das colunas:

   ![Editor de colunas na lista de dispositivos][img-columneditor]

### <a name="customize-the-device-icon"></a>Personalizar o ícone do dispositivo

Pode personalizar o ícone do dispositivo apresentado na lista de dispositivos no painel **Detalhes do Dispositivo**, da seguinte forma:

1. Escolha o ícone de lápis para abrir o painel **Editar imagem** de um dispositivo:

   ![Abrir o editor de imagem do dispositivo][img-startimageedit]

1. Carregue uma imagem nova ou utilize uma das existentes e escolha **Guardar**:

   ![Editor para editar a imagem do dispositivo][img-imageedit]

1. A imagem que selecionou é agora apresentada na coluna **Ícone** do dispositivo.

> [!NOTE]
> A imagem é armazenada no armazenamento de blobs. As etiquetas no dispositivo duplo contêm ligações para a imagem no armazenamento de blobs.

### <a name="add-a-device"></a>Adicionar um dispositivo

Ao implementar a solução pré-configurada, aprovisiona automaticamente 25 dispositivos de exemplo que pode ver na lista de dispositivos. Estes dispositivos são *dispositivos simulados* em execução num Trabalho Web do Azure. Os dispositivos simulados permitem-lhe testar facilmente a solução pré-configurada sem ser necessário implementar dispositivos físicos reais. Se quiser ligar um dispositivo real à solução, veja o tutorial [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm].

Os seguintes passos mostram como adicionar um dispositivo simulado à solução:

1. Navegue de volta para a lista de dispositivos.

1. Para adicionar um dispositivo, escolha **+ Adicionar Um Dispositivo**, no canto inferior esquerdo.

   ![Adicionar um dispositivo à solução pré-configurada][img-adddevice]

1. Escolha **Adicionar Novo** no mosaico **Dispositivo Simulado**.

   ![Definir novos detalhes de dispositivos no dashboard][img-addnew]

   Para além de criar um novo dispositivo simulado, pode ainda adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para saber mais sobre a ligação de dispositivos físicos à solução, consulte [Connect your device to the IoT Suite remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota do IoT Suite)][lnk-connect-rm].

1. Selecione **Autorizar-me a definir o meu próprio ID do Dispositivo** e introduza um nome de ID único para o dispositivo, como **mydevice_01**.

1. Escolha **Criar**.

   ![Guardar um novo dispositivo][img-definedevice]

1. No passo 3 de **Adicionar um dispositivo simulado**, escolha **Concluído** para regressar à lista de dispositivos.

1. Pode ver o seu dispositivo **Em execução** na lista de dispositivos.

    ![Ver o novo dispositivo na lista de dispositivos][img-runningnew]

1. Pode ainda ver a telemetria simulada do seu novo dispositivo no dashboard:

    ![Ver telemetria do novo dispositivo][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Desativar e eliminar um dispositivo

Pode desativar um dispositivo e removê-lo após a desativação:

![Desativar e remover um dispositivo][img-disable]

### <a name="add-a-rule"></a>Adicionar uma regra

Não existem quaisquer regras para o novo dispositivo que acabou de adicionar. Nesta secção, irá adicionar uma regra que aciona um alarme quando a temperatura comunicada pelo novo dispositivo for superior a 47 graus. Antes de começar, tenha em atenção que o histórico de telemetria para o novo dispositivo no dashboard mostra que a temperatura do dispositivo nunca excede 45 graus.

1. Navegue de volta para a lista de dispositivos.

1. Para adicionar uma regra para o dispositivo, selecione o seu dispositivo novo na **Lista de Dispositivos** e, em seguida, escolha **Adicionar regra**.

1. Crie uma regra que utiliza a **Temperatura** como o campo de dados e utiliza a **AlarmTemp** como resultado quando a temperatura for superior a 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule]

1. Para guardar as alterações, escolha **Guardar e Ver Regras**.

1. Escolha **Comandos** no painel Detalhes do dispositivo para o novo dispositivo.

    ![Adicionar uma regra de dispositivo][img-adddevicerule2]

1. Selecione **ChangeSetPointTemp** a partir da lista de comandos e defina a **SetPointTemp** para 45. Em seguida, escolha **Enviar Comando**:

    ![Adicionar uma regra de dispositivo][img-adddevicerule3]

1. Navegue de volta para o dashboard. Após um curto período de tempo, verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura comunicada pelo seu dispositivo novo exceder o limiar de 47 graus:

    ![Adicionar uma regra de dispositivo][img-adddevicerule4]

1. Pode rever e editar todas as suas regras na página **Regras** do dashboard:

    ![Listar regras do dispositivo][img-rules]

1. Pode rever e editar todas as ações que podem ser executadas em resposta a uma regra na página **Ações** do dashboard:

    ![Listar ações do dispositivo][img-actions]

> [!NOTE]
> É possível definir ações que podem enviar um e-mail ou um SMS em resposta a uma regra ou integrar com um sistema de linha de negócio através de uma [Aplicação Lógica][lnk-logic-apps]. Para obter mais informações, veja [Connect Logic App to your Azure IoT Suite Remote Monitoring preconfigured solution (Ligar Aplicação Lógica à sua solução pré-configurada de Monitorização Remota do Azure IoT Suite)][lnk-logicapptutorial].

### <a name="manage-filters"></a>Gerir filtros

Na lista de dispositivos, pode criar, guardar e recarregar filtros para apresentar uma lista personalizada de dispositivos ligados ao seu hub. Para criar um filtro:

1. Escolha o ícone de edição de filtro acima da lista de dispositivos:

    ![Abra o editor de filtros][img-editfiltericon]

1. No **Editor de filtros**, adicione os campos, os operadores e os valores para filtrar a lista de dispositivos. Pode adicionar várias cláusulas para refinar o filtro. Escolha **Filtro** para aplicar o filtro:

    ![Criar um filtro][img-filtereditor]

1. Neste exemplo, a lista é filtrada por fabricante e número do modelo:

    ![Lista filtrada][img-filterelist]

1. Para guardar o seu filtro com um nome personalizado, escolha o ícone **Guardar como**:

    ![Guardar um filtro][img-savefilter]

1. Para reaplicar um filtro que guardou anteriormente, escolha o ícone **Abrir filtro guardado**:

    ![Abrir um filtro][img-openfilter]

Pode criar filtros com base no id dos dispositivos, no estado dos mesmos, nas propriedades pretendidas, nas propriedades comunicadas e nas etiquetas. Pode adicionar as suas etiquetas personalizadas a um dispositivo na secção **Etiquetas** do painel **Detalhes do Dispositivo** ou executar uma tarefa para atualizar etiquetas em vários dispositivos.

> [!NOTE]
> No **Editor de filtros**, pode utilizar a **Vista avançada** para editar o texto da consulta diretamente.

### <a name="commands"></a>Comandos

No painel **Detalhes do Dispositivo**, pode enviar comandos para o dispositivo. Quando um dispositivo é iniciado pela primeira vez, envia informações sobre os comandos que suporta à solução. Para uma discussão sobre as diferenças entre comandos e métodos, veja [Opções de cloud para dispositivo Hub IoT do Azure][lnk-c2d-guidance].

1. Escolha **Comandos**, no painel **Detalhes do Dispositivo** do dispositivo selecionado:

   ![Comandos de dispositivos no dashboard][img-devicecommands]

1. Selecione **PingDevice** na lista de comandos.

1. Escolha **Enviar Comando**.

1. Pode ver o estado do comando no histórico do comando.

   ![Estado do comando no dashboard][img-pingcommand]

A solução controla o estado de cada comando que envia. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido para **Êxito**.

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

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou uma solução pré-configurada funcional, pode ler os artigos seguintes para continuar a introdução ao IoT Suite:

* [Instruções sobre a solução pré-configurada de monitorização remota][lnk-rm-walkthrough]
* [Connect your device to the remote monitoring preconfigured solution (Ligar o seu dispositivo à solução pré-configurada de monitorização remota)][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
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
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md