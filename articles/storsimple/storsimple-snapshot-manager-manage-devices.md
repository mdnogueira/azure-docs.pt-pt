---
title: Gerir dispositivos com o Snapshot Manager do StorSimple | Microsoft Docs
description: Descreve como utilizar o snap-in MMC de Snapshot Manager do StorSimple para ligar e gerir dispositivos StorSimple.
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utilize o Snapshot Manager do StorSimple para ligar e gerir dispositivos StorSimple
## <a name="overview"></a>Descrição geral
Pode utilizar nós no Snapshot Manager do StorSimple **âmbito** painel para verificar se os dados de dispositivo importados do StorSimple e atualizar os dispositivos de armazenamento ligado. Além disso, ao clicar no **dispositivos** nós, pode ver uma lista de dispositivos ligados e as informações de estado correspondente no **resultados** painel.

![Dispositivos ligados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Ligada do StorSimple Snapshot Manager do dispositivo** 

Dependendo do seu **vista** seleções, o **resultados** painel apresenta as seguintes informações sobre cada dispositivo. (Para obter mais informações sobre como configurar uma vista, aceda a [menu Ver](storsimple-use-snapshot-manager.md#view-menu).

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |O nome do dispositivo, tal como foi configurada no portal clássico do Azure |
| Modelo |O número de modelo do dispositivo |
| Versão |A versão do software instalado no dispositivo |
| Estado |Indica se o dispositivo está disponível |
| Última sincronização |Data e hora quando o dispositivo última sincronização |
| Número de série |O número de série do dispositivo |

Se, faça duplo clique o **dispositivos** no nó de **âmbito** painel, pode selecionar as seguintes ações:

* Adicionar ou substituir um dispositivo
* Ligar um dispositivo e certifique-se de importações
* Atualizar dispositivos ligados

Se clicar no **dispositivos** nome de nó e, em seguida, contexto de um dispositivo no **resultados** painel, pode selecionar as seguintes ações:

* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo
* Eliminar uma configuração de dispositivo
* Alterar uma palavra-passe do dispositivo

> [!NOTE]
> Todas estas ações também estão disponíveis no **ações** painel.


Este tutorial explica como utilizar o Snapshot Manager do StorSimple para ligar e gerir dispositivos e executar as seguintes tarefas:

* Adicionar ou substituir um dispositivo
* Ligar um dispositivo e certifique-se de importações
* Atualizar dispositivos ligados
* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo individual
* Eliminar uma configuração de dispositivo
* Alterar uma palavra-passe do dispositivo expirado
* Substituir um dispositivo com falhas

> [!NOTE]
> Para obter informações gerais sobre como utilizar a interface Snapshot Manager do StorSimple, aceda a [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo
Utilize o procedimento seguinte para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique com botão direito do **dispositivos** nó e, em seguida, clique **configurar um dispositivo**. O **configurar um dispositivo** é apresentada a caixa de diálogo.
   
    ![Configurar um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. No **dispositivo** pendente caixa, selecione o endereço IP do dispositivo ou dispositivo virtual. 
4. No **palavra-passe** texto caixa, escreva a palavra-passe do Snapshot Manager do StorSimple que criou para o dispositivo no portal clássico do Azure. Clique em **OK**. Pesquisa para o dispositivo que identificou o Snapshot Manager do StorSimple. 
   
   * Se o dispositivo estiver disponível, o Snapshot Manager do StorSimple adiciona uma ligação.
   * Se o dispositivo estiver indisponível por qualquer motivo, o Snapshot Manager do StorSimple devolve uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar o **configurar um dispositivo** caixa de diálogo.

## <a name="connect-a-device-and-verify-imports"></a>Ligar um dispositivo e certifique-se de importações
Utilize o procedimento seguinte para ligar um dispositivo StorSimple e certifique-se de que todos os grupos existentes de volume que associou cópias de segurança são importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Para ligar um dispositivo e certifique-se de importações
1. Para ligar um dispositivo para Snapshot Manager do StorSimple, siga as instruções em Adicionar ou substituir um dispositivo. Quando estabelece ligação a um dispositivo, o Snapshot Manager do StorSimple responde da seguinte forma:
   
   * Se o dispositivo estiver indisponível por qualquer motivo, o Snapshot Manager do StorSimple devolve uma mensagem de erro. 
   
   * Se o dispositivo estiver disponível, o Snapshot Manager do StorSimple adiciona uma ligação. Quando o dispositivo é selecionado, é apresentado no **resultados** painel e o campo de estado indica que o dispositivo é **disponível**. Snapshot Manager do StorSimple importa quaisquer grupos de volume configurados para o dispositivo, desde que os grupos de volume ter associados a cópias de segurança. Políticas de cópia de segurança não foram importadas. Grupos de volume que não dispõe de cópias de segurança associadas não foram importados.
2. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
3. Clique com o botão direito no nó superior no **âmbito** painel e, em seguida, clique em **alternar importações apresentar**.
   
    ![Apresentação de importações selecione Ativar/desativar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. O **alternar importações apresentar** aparece a caixa de diálogo, que mostra o estado dos grupos de volume importados e cópias de segurança. Clique em **OK**.

Depois dos grupos de volume e cópias de segurança importadas com êxito, pode utilizar o Snapshot Manager do StorSimple para geri-los, tal como iria gerir grupos de volume e cópias de segurança que criar e configurar com Snapshot Manager do StorSimple. 

## <a name="refresh-connected-devices"></a>Atualizar dispositivos ligados
Utilize o procedimento seguinte para sincronizar os dispositivos StorSimple ligados com o Snapshot Manager do StorSimple.

#### <a name="to-refresh-connected-devices"></a>Para atualizar os dispositivos ligados
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, faça duplo clique **dispositivos**e, em seguida, clique em **atualizar dispositivos**. Este sincroniza os dispositivos ligados com o Snapshot Manager do StorSimple para que possa visualizar os grupos de volume e cópias de segurança, incluindo quaisquer adições recentes. 
   
    ![Atualizar os dispositivos do StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

O **atualizar dispositivos** ação obtém novos grupos de volume e quaisquer cópias de segurança associadas de dispositivos ligados. Ao contrário do **reanalisar volumes** ação disponível para o **Volumes** nó, **atualizar dispositivos** não restaurar o registo de cópia de segurança.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo
Utilize o procedimento seguinte para autenticar um dispositivo StorSimple com o Snapshot Manager do StorSimple.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique em **dispositivos**.
3. No **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **autenticar**.
4. O **autenticar** é apresentada a caixa de diálogo. Escreva a palavra-passe do dispositivo e, em seguida, clique em **OK**.
   
    ![Caixa de diálogo de autenticação](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Ver detalhes do dispositivo
Utilize o procedimento seguinte para ver os detalhes de um dispositivo StorSimple e, se necessário, ressincronizar o dispositivo com o Snapshot Manager do StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Para ver e ressincronizar detalhes do dispositivo
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique em **dispositivos**.
3. No **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **detalhes**.

4. o **detalhes do dispositivo** é apresentada a caixa de diálogo. Esta caixa apresenta o nome, modelo, versão, número de série, estado, o destino iSCSI nome qualificado (IQN) e última sincronização data e a hora.

* Clique em **ressincronizar** para sincronizar o dispositivo.
* Clique em **OK** ou **Cancelar** para fechar a caixa de diálogo.
  
  ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual
Utilize o procedimento seguinte para sincronizar novamente um dispositivo StorSimple individual com Snapshot Manager do StorSimple.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No **âmbito** painel, clique em **dispositivos**. 
3. No **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **atualizar dispositivo**. Sincroniza o dispositivo com o Snapshot Manager do StorSimple.

## <a name="delete-a-device-configuration"></a>Eliminar uma configuração de dispositivo
Utilize o procedimento seguinte para eliminar uma configuração de dispositivo StorSimple individuais do Snapshot Manager do StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Para eliminar uma configuração de dispositivo
1. Clique no ícone de ambiente de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique em **dispositivos**. 
3. No **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **eliminar**. 
4. É apresentada a mensagem seguinte. Clique em **Sim** para eliminar a configuração ou clique em **não** para cancelar a eliminação.
   
    ![Eliminar a configuração de dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma palavra-passe do dispositivo expirado
Tem de introduzir uma palavra-passe para autenticar um dispositivo StorSimple com o Snapshot Manager do StorSimple. Configurar esta palavra-passe ao utilizar a interface do Windows PowerShell para configurar o dispositivo. No entanto, pode expirar a palavra-passe. Se isto acontecer, pode utilizar o portal clássico do Azure para alterar a palavra-passe. Em seguida, porque o dispositivo foi configurado no Snapshot Manager do StorSimple antes da palavra-passe expirou, tem de autenticar novamente o dispositivo no Snapshot Manager do StorSimple.

#### <a name="to-change-the-expired-password"></a>Para alterar a palavra-passe expirada
1. No portal clássico do Azure, inicie o serviço StorSimple Manager.
2. Clique em **dispositivos** > **configurar** para o dispositivo.
3. Desloque para baixo para a secção de Snapshot Manager do StorSimple. Introduza uma palavra-passe é de 14 a 15 carateres. Certifique-se de que a palavra-passe contiver uma combinação de carateres em maiúsculas, minúsculas, numérico e especiais.
4. Volte a introduzir a palavra-passe para confirmá-la.
5. Clique em **Guardar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Para voltar a autenticar o dispositivo
1. Inicie o Snapshot Manager do StorSimple.
2. No **âmbito** painel, clique em **dispositivos**. É apresentada uma lista de dispositivos configurados no **resultados** painel.
3. Selecione o dispositivo, rato e, em seguida, clique em **autenticar**.
4. No **autenticar** janela, introduza a palavra-passe nova.
5. Selecione o dispositivo, rato e selecione **atualizar dispositivo**. Sincroniza o dispositivo com o Snapshot Manager do StorSimple.

## <a name="replace-a-failed-device"></a>Substituir um dispositivo com falhas
Se um dispositivo StorSimple falha e é substituído por um dispositivo de modo de espera (ativação pós-falha), utilize os seguintes passos para ligar para o novo dispositivo e ver as cópias de segurança associadas.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para ligar a um novo dispositivo após a ativação pós-falha
1. Reconfigure a ligação de iSCSI para o novo dispositivo. Para obter instruções, consulte "passo 7: montar, inicializar e formatar um volume" no [implementar o dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se o novo dispositivo StorSimple tem o mesmo endereço IP que o antigo, poderá conseguir ligar-se a configuração antiga.


1. Pare o serviço de gestão do StorSimple da Microsoft:
   
   1. Inicie o Gestor de servidor.
   2. No Dashboard do Gestor de servidores, no **ferramentas** menu, selecione **serviços**.
   3. No **serviços** janela, selecione o **serviço de gestão da Microsoft StorSimple**.
   4. No painel direito, em **serviço de gestão da Microsoft StorSimple**, clique em **parar o serviço**.
2. Remova as informações de configuração relacionados com o dispositivo antigo:
   
   1. No Explorador de ficheiros, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Elimine os ficheiros na pasta BACatalog.
3. Reinicie o serviço de gestão do StorSimple da Microsoft:
   
   1. No Dashboard do Gestor de servidores, no **ferramentas** menu, selecione **serviços**.
   2. No **serviços** janela, selecione o **serviço de gestão da Microsoft StorSimple**.
   3. No painel direito, em **serviço de gestão da Microsoft StorSimple**, clique em **reinicie o serviço**.
4. Inicie o Snapshot Manager do StorSimple.
5. Para configurar o novo dispositivo StorSimple, conclua os passos no passo 2: ligar um dispositivo StorSimple no [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).
6. Clique com o botão direito no nó de nível superior no **âmbito** painel (Snapshot Manager do StorSimple no exemplo) e, em seguida, clique em **alternar importações apresentar**. 
7. É apresentada uma mensagem quando os grupos de volume importados e cópias de segurança são visíveis no Snapshot Manager do StorSimple. Clique em **OK**.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar Snapshot Manager do StorSimple para ver e gerir volumes](storsimple-snapshot-manager-manage-volumes.md).

