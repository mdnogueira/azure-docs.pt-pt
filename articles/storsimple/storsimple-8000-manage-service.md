---
title: "Implementar o serviço do Gestor de dispositivos do StorSimple no Azure | Microsoft Docs"
description: "Explica como criar e eliminar o dispositivo do serviço StorSimple Manager no portal do Azure e descreve como gerir a chave de registo do serviço."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: alkohli
ms.openlocfilehash: 2d6c1737edbc4e69dd7de4c8ce3d7b0899bfae51
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Implementar o serviço do Gestor de dispositivos do StorSimple para os dispositivos de série 8000 do StorSimple

## <a name="overview"></a>Descrição geral

O serviço do Gestor de dispositivos do StorSimple é executado no Microsoft Azure e se liga a diversos dispositivos StorSimple. Depois de criar o serviço, pode utilizá-lo para gerir todos os dispositivos que estão ligados ao serviço StorSimple Manager de dispositivos de uma localização única, central, deste modo, minimizando o fardo administrativo.

Este tutorial descreve os passos necessários para a criação, eliminação, a migração do serviço e a gestão da chave de registo do serviço. As informações contidas neste artigo são aplicáveis apenas a dispositivos de série 8000 do StorSimple. Para obter mais informações sobre matrizes Virtual StorSimple, aceda a [implementar um serviço StorSimple Manager de dispositivo para a matriz de Virtual StorSimple](storsimple-virtual-array-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço
Para criar um serviço do Gestor de dispositivos do StorSimple, terá de ter:

* Uma subscrição com um Enterprise Agreement
* Uma conta de armazenamento do Microsoft Azure Active Directory
* As informações de faturação que são utilizadas para gestão de acesso

Apenas as subscrições com um Enterprise Agreement são permitidas. Subscrições do Microsoft Sponsorship que foram permitidas no portal clássico do Azure não são suportadas no portal do Azure. Verá a mensagem seguinte quando utilizar uma subscrição não suportada:

![Não é válida de subscrição](./media/storsimple-8000-manage-service/subscription-not-valid.jpg)

Também pode optar por gerar uma conta do storage predefinida quando criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço funcione com diferentes subscrições, as organizações ou até mesmo as localizações de implementação. 

> [!NOTE]
> Terá de instâncias separadas do serviço do Gestor de dispositivos do StorSimple para gerir dispositivos de série 8000 do StorSimple e matrizes Virtual StorSimple.

Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Para cada serviço do Gestor de dispositivos do StorSimple, existem os seguintes atributos:

* **Nome** – o nome que foi atribuído ao seu serviço do Gestor de dispositivos do StorSimple quando foi criado. **Não é possível alterar o nome do serviço após o serviço é criado. Isto também se aplica para outras entidades, tais como os dispositivos, volumes, contentores de volume e políticas de cópia de segurança que não não possível mudar o nome no portal do Azure.**
* **Estado** – o estado do serviço, que pode ser **Active Directory**, **criar**, ou **Online**.
* **Localização** – a localização geográfica na qual o dispositivo StorSimple será implementado.
* **Subscrição** – a subscrição de faturação que está associada ao seu serviço.

## <a name="move-a-service-to-azure-portal"></a>Mover um serviço para o portal do Azure
Pode ser gerido, agora, série 8000 do StorSimple no portal do Azure. Se tiver um serviço existente para gerir os dispositivos StorSimple, recomendamos que mova o serviço para o portal do Azure. Portal clássico do Azure para o serviço StorSimple Manager não é suportado após 30 de Setembro de 2017. Se pretender mover para o novo portal do Azure, consulte [considerações para transição](#considerations-for-transition). 

> [!NOTE]
> Começando 5 de Outubro de 2017, os seus gestores de dispositivo StorSimple clássicos serão automaticamente mudada para o novo portal do Azure. Esta é uma implementação faseada e iremos atualizar, sobre a mudança através de e-mail e notificações do portais. Se tiver alguma questão, consulte [FAQ: mover para o portal do Azure](storsimple-8000-move-azure-portal-faq.md).

### <a name="considerations-for-transition"></a>Considerações para transição

Reveja o impacto da migração para o novo portal do Azure antes de mover o serviço.

> [!NOTE]
> Os cmdlets do PowerShell de gestão de serviço do Azure (ASM) existentes não são suportados depois de mover para o novo portal do Azure. Atualize os scripts para gerir os seus dispositivos através do SDK do Gestor de recursos do Azure. Para obter mais informações, visite o [baseada na utilização do Azure Resource Manager SDK scripts para gerir dispositivos StorSimple](storsimple-8000-automation-azurerm-scripts.md).
> O novo portal do Azure suporta dispositivos com a atualização 3.0 ou posterior. Se o dispositivo não estiver atualizado, recomendamos vivamente que aplique a atualização 5 logo que possível.

#### <a name="before-you-transition"></a>Antes de transição

* O dispositivo está em execução atualização 3.0 ou posterior. Se o dispositivo estiver em execução uma versão mais antiga, recomendamos vivamente que instale a atualização 5 através do método do correção. Para obter mais informações, aceda a [instalar Update 5](storsimple-8000-install-update-5.md). Se utilizar uma aplicação de nuvem do StorSimple (8010/8020), não é possível atualizar uma aplicação de nuvem. Utilize a versão mais recente do software para criar um novo dispositivo de nuvem com atualização 5.0 e, em seguida, efetuar a ativação pós-falha para o novo dispositivo de nuvem criado.

* Assim que são transferidas para o novo portal do Azure, não é possível utilizar o portal clássico do Azure para gerir o dispositivo StorSimple.

* A transição não acontece e não existe nenhum período de inatividade para o dispositivo.

* Todos os gestores do dispositivo StorSimple sob a subscrição especificada são transferidos.

#### <a name="during-the-transition"></a>Durante a transição

* Não é possível gerir o seu dispositivo do portal.
* Operações como cópias de segurança em camadas e agendadas continuam a ocorrer.
* Não elimine os gestores de dispositivos do StorSimple antigo enquanto a transição está em curso.

#### <a name="after-the-transition"></a>Após a transição

* Já não pode gerir os seus dispositivos no portal clássico.

* Os cmdlets do PowerShell de gestão de serviço do Azure (ASM) existentes não são suportados. Atualize os scripts para gerir os seus dispositivos através do Gestor de recursos do Azure. Para scripts de exemplo utilizando o Gestor de recursos do SDK, consulte o [storsimpledevicemgmttools github](https://github.com/anoobbacker/storsimpledevicemgmttools).

* A configuração do serviço e dispositivo são retidos. Todos os volumes e as cópias de segurança também são transferidas para o portal do Azure.

### <a name="begin-transition"></a>Começar a transição

Execute os seguintes passos para transitar o seu serviço para o portal do Azure.

1. Aceda ao seu serviço StorSimple Manager existente no portal do Azure.
    ![Mais serviços](./media/storsimple-8000-manage-service/service-browse01.png) ![Gestor de dispositivos selecione](./media/storsimple-8000-manage-service/service-browse02.png)

2. Verá uma notificação a informar que o serviço StorSimple Manager de dispositivo está agora disponível no portal do Azure. No portal do Azure, o serviço é referido como o serviço do Gestor de dispositivos do StorSimple.
    ![Notificação de migração](./media/storsimple-8000-manage-service/service-transition1.jpg)
    
    1. Certifique-se de que consultou o impacto da migração completo.
    2. Reveja a lista de gestores de dispositivo do StorSimple que serão movidos do portal clássico.

3. Clique em **migrar**. A transição começa e demora alguns minutos a concluir.

Depois de concluída a transição, pode gerir os seus dispositivos através do serviço StorSimple Manager de dispositivos no portal do Azure. Se não vir uma opção para migrar para o portal do Azure, mas pretender mover, pode [submeter um pedido](https://aka.ms/ss8000-cx-signup).

No portal do Azure, são suportados apenas StorSimple dispositivos a executar a atualização 3.0 e superior. Os dispositivos que executam versões mais antigas têm suporte limitado. Depois de ter migrado para o portal do Azure, utilize a tabela seguinte para compreender as operações são suportadas em dispositivos com versões anteriores ao 3.0 de atualização.

| Operação                                                                                                                       | Suportado      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registar um dispositivo                                                                                                               | Sim            |
| Configurar definições de dispositivos como geral, rede e segurança                                                                | Sim            |
| Procurar, transferir e instalar atualizações                                                                                             | Sim            |
| Desativar o dispositivo                                                                                                               | Sim            |
| Eliminar o dispositivo                                                                                                                   | Sim            |
| Criar, modificar e eliminar um contentor de volume                                                                                   | Não             |
| Criar, modificar e eliminar um volume                                                                                             | Não             |
| Criar, modificar e eliminar uma política de cópia de segurança                                                                                      | Não             |
| Efetuar uma cópia de segurança manual                                                                                                            | Não             |
| Efetuar uma cópia de segurança agendada                                                                                                         | Não aplicável |
| Restaurar a partir de um backupset                                                                                                        | Não             |
| Clonar num dispositivo com a atualização 3.0 e posteriores <br> O dispositivo de origem está a executar versão antes da atualização 3.0.                                | Sim            |
| Clonar num dispositivo com versões anteriores ao 3.0 de atualização                                                                          | Não             |
| Ativação pós-falha como dispositivo de origem <br> (a partir de um dispositivo com a versão anterior à atualização 3.0 num dispositivo com a atualização 3.0 e posterior)                                                               | Sim            |
| Ativação pós-falha como dispositivo de destino <br> (para um dispositivo a executar a versão do software antes da atualização 3.0)                                                                                   | Não             |
| Limpar um alerta                                                                                                                  | Sim            |
| Ver as políticas de cópia de segurança, o catálogo de cópias de segurança, volumes, contentores de volume, gráficos de monitorização, tarefas e alertas criados no portal clássico | Sim            |
| Ativar e desativar os controladores de dispositivo                                                                                              | Sim            |


## <a name="delete-a-service"></a>Eliminar um serviço

Antes de eliminar um serviço, certifique-se de que não existem dispositivos ligados a estiver a utilizar. Se o serviço está em utilização, desative os dispositivos ligados. A operação de desativar irá sever a ligação entre o dispositivo e o serviço, mas manter os dados de dispositivo na nuvem.

> [!IMPORTANT]
> Depois de um serviço é eliminado, não é possível reverter a operação. Qualquer dispositivo que estava a utilizar o serviço tem de ser repor as predefinições de fábrica antes de poder ser utilizada com outro serviço. Neste cenário, os dados locais no dispositivo, bem como a configuração, são perdidos.

Execute os seguintes passos para eliminar um serviço.

### <a name="to-delete-a-service"></a>Para eliminar um serviço

1. Pesquisa para o serviço que pretende eliminar. Clique em **recursos** ícone e, em seguida, introduza os termos de licenciamento adequados para procurar. Nos resultados da pesquisa, clique no serviço que pretende eliminar.

    ![Para eliminar o serviço de pesquisa](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Isto leva-o para o painel de serviço do Gestor de dispositivos do StorSimple. Clique em **Eliminar**.

    ![Eliminar o serviço](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Clique em **Sim** na notificação de confirmação. Pode demorar alguns minutos para que o serviço será eliminado.

    ![Confirmar eliminação](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço

Depois de ter criado com êxito um serviço, terá de registar o dispositivo StorSimple com o serviço. Para registar o primeiro dispositivo StorSimple, terá da chave de registo do serviço. Para registar dispositivos adicionais com um serviço StorSimple, terá da chave de registo e a chave de encriptação de dados do serviço (o que é gerado no primeiro dispositivo durante o registo). Para mais informações sobre a chave de encriptação de dados do serviço, consulte [segurança do StorSimple](storsimple-8000-security.md). Pode obter a chave de registo acedendo **chaves** no painel do Gestor de dispositivos do StorSimple.

Execute os passos seguintes para obter a chave de registo do serviço.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Manter a chave de registo do serviço numa localização segura. Terá esta chave, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais este serviço. Depois de obter a chave de registo do serviço, tem de configurar o seu dispositivo através do Windows PowerShell para StorSimple interface.

Para obter mais informações sobre como utilizar esta chave de registo, consulte [passo 3: configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo do serviço
Terá de voltar a gerar uma chave de registo do serviço, se for necessário para executar a rotação da chave ou se a lista de administradores de serviço foi alterada. Quando voltar a gerar a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que foram registados já não são afetados por este processo.

Execute os seguintes passos para voltar a gerar uma chave de registo do serviço.

### <a name="to-regenerate-the-service-registration-key"></a>A regenerar a chave de registo do serviço
1. No **Gestor de dispositivos do StorSimple** painel, aceda a **gestão &gt;**  **chaves**.
    
    ![Painel de chaves](./media/storsimple-8000-manage-service/regenregkey2.png)

2. No **chaves** painel, clique em **regenerar**.

    ![Clique em voltar a gerar](./media/storsimple-8000-manage-service/regenregkey3.png)
3. No **chave de registo do serviço de voltar a gerar** painel, reveja a ação é necessária quando as chaves são regeneradas. Todos os dispositivos posteriores que são registados com este serviço utilizam a nova chave de registo. Clique em **regenerar** para confirmar. Será notificado depois de concluída a regeneração.

    ![Confirmar voltar a gerar](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Será apresentada uma nova chave de registo do serviço.

5. Copie esta chave e guarde-a para registar os novos dispositivos com este serviço.



## <a name="change-the-service-data-encryption-key"></a>Alterar a chave de encriptação de dados do serviço
Chaves de encriptação de dados de serviço são utilizadas para encriptar dados de cliente confidencial, tais como credenciais de conta de armazenamento, que são enviados do serviço StorSimple Manager para o dispositivo StorSimple. Terá de alterar estas chaves periodicamente se a organização de TI tem uma política de rotação da chave nos dispositivos de armazenamento. O processo de alteração de chave pode ser ligeiramente diferente consoante esteja há um único ou vários dispositivos geridos pelo serviço StorSimple Manager. Para obter mais informações, aceda a [StorSimple segurança e proteção de dados](storsimple-8000-security.md).

Alterar a chave de encriptação de dados do serviço é um processo do passo 3:

1. Utilizar scripts do Windows PowerShell para o Azure Resource Manager, autorize um dispositivo para alterar a chave de encriptação de dados do serviço.
2. Com o Windows PowerShell para StorSimple, inicie a alteração de chave de encriptação do serviço dados.
3. Se tiver mais do que um dispositivo StorSimple, atualize a chave de encriptação de dados do serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passo 1: Script de utilize o Windows PowerShell para autorizar um dispositivo para alterar a chave de encriptação de dados do serviço
Normalmente, o administrador do dispositivo irá solicitar que o administrador de serviço autorizar um dispositivo para alterar as chaves de encriptação de dados do serviço. O administrador de serviço, em seguida, irá autorizar o dispositivo para alterar a chave.

Este passo é realizado através do script do Azure Resource Manager com base. O administrador de serviço pode selecionar um dispositivo que é elegível para ser autorizado. O dispositivo, em seguida, está autorizado a iniciar o processo de alteração de chave de encriptação de dados do serviço. 

Para obter mais informações sobre como utilizar o script, aceda a [autorizar ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Os dispositivos que podem estar autorizados a alterar as chaves de encriptação de dados do serviço?
Pode ser autorizado para iniciar a alterações de chave de encriptação de dados de serviço, um dispositivo tem de cumprir os seguintes critérios:

* O dispositivo tem de estar online para ser elegível para autorização de alteração de chave de encriptação de dados de serviço.
* Podem autorizar o mesmo dispositivo novamente após 30 minutos, se a alteração da chave não foi iniciada.
* Podem autorizar um dispositivo diferente, desde que a alteração da chave não foi iniciada pelo dispositivo anteriormente autorizado. Depois do novo dispositivo tenha sido autorizado, o dispositivo antigo não é possível iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto o rollover da chave de encriptação de dados do serviço está em curso.
* Podem autorizar um dispositivo quando alguns dos dispositivos registados com o serviço tem distribuída a encriptação enquanto outros utilizadores não têm. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Utilize o Windows PowerShell para StorSimple iniciar a alteração de chave de encriptação de dados serviço
Este passo é executado no Windows PowerShell para StorSimple interface no dispositivo StorSimple autorizado.

> [!NOTE]
> Não existem operações podem ser efetuadas no portal do Azure do seu serviço StorSimple Manager até que o rollover da chave esteja concluída.
> 
> 

Se estiver a utilizar a consola de série do dispositivo para ligar a interface do Windows PowerShell, execute os seguintes passos.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração de chave de encriptação de dados serviço
1. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois do cmdlet foi concluída com êxito, irá receber uma nova chave de encriptação de dados do serviço. Copie e guarde esta chave para utilização no passo 3 deste processo. Esta chave será utilizada para atualizar todos os restantes dispositivos registados com o serviço StorSimple Manager.
   
   > [!NOTE]
   > Este processo pode ser iniciado no prazo de quatro horas de autorização de um dispositivo StorSimple.
   > 
   > 
   
   Esta nova chave, em seguida, é enviada para o serviço para fazer o push para todos os dispositivos que são registados com o serviço. Um alerta, em seguida, serão apresentados no dashboard do serviço. O serviço irá desativar todas as operações em dispositivos registados, e o administrador do dispositivo, em seguida, tem de atualizar a chave de encriptação de dados do serviço nos outros dispositivos. No entanto, a e/s (anfitriões enviar dados para a nuvem) não serão interrompidos.
   
   Se tiver um único dispositivo registado ao seu serviço, o processo de rollover está agora concluído e pode ignorar o passo seguinte. Se tiver vários dispositivos registados ao seu serviço, avance para o passo 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de encriptação de dados do serviço em outros dispositivos StorSimple
Estes passos devem ser executados na interface do Windows PowerShell do dispositivo StorSimple, se tiver vários dispositivos registados no serviço StorSimple Manager. A chave que obteve no passo 2 tem de ser utilizada para atualizar todos os restantes dispositivo StorSimple registado com o serviço StorSimple Manager.

Execute os seguintes passos para atualizar a encriptação de dados do serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de encriptação de dados do serviço em dispositivos físicos
1. Utilize o Windows PowerShell para StorSimple para ligar à consola do. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de encriptação de dados de serviço que obteve no [passo 2: Utilize o Windows PowerShell para StorSimple iniciar a alteração de chave de encriptação de dados serviço](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de encriptação de dados do serviço em todas as aplicações de nuvem 8010/8020
1. Transferir e configurar [atualização CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) script do PowerShell. 
2. Abra o PowerShell e a linha de comandos, escreva:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Este script irá garantir que essa chave de encriptação de dados do serviço está definido em todos os dispositivos 8010/8020 nuvem sob o Gestor de dispositivos.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [processo de implementação do StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Saiba mais sobre [gerir a conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço do Gestor de dispositivos do StorSimple para administrar o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
