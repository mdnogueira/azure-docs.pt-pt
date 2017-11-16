---
title: "Portal preparação para a matriz Virtual StorSimple | Microsoft Docs"
description: "Primeiro tutorial para implementar matriz virtual StorSimple envolve a preparação do portal do Azure"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6685c5ab7768176a0c8e7084c8512d5345732d9a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>Implementar StorSimple Virtual matriz - preparar o portal do Azure

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Descrição geral

Este é o primeiro artigo da série de tutoriais de implementação necessários para implementar completamente a matriz de virtual como um servidor de ficheiros ou um servidor de iSCSI utilizando o modelo do Resource Manager. Este artigo descreve a preparação necessária para criar e configurar o serviço StorSimple Manager do dispositivo antes de aprovisionamento de uma matriz de virtual. Este artigo também contém ligações enviados para uma lista de verificação de configuração de implementação e configuração de pré-requisitos.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que reveja a lista de verificação de configuração de implementação antes de começar. A preparação de portal demora menos de 10 minutos.

As informações publicadas neste artigo aplica-se para a implementação de matrizes Virtual StorSimple no portal do Azure e na nuvem do Microsoft Azure Government.

### <a name="get-started"></a>Introdução
O fluxo de trabalho de implementação é composta por preparar o portal, uma matriz virtual no seu ambiente virtualizado de aprovisionamento e concluir a configuração. Para começar a utilizar com a implementação de matriz Virtual StorSimple como um servidor de ficheiros ou um servidor de iSCSI, tem de referir-se os seguintes recursos tabulated.

#### <a name="deployment-articles"></a>Artigos de implementação

Para implementar a matriz de Virtual StorSimple, consulte os artigos seguintes na sequência prevista.

| **#** | **Neste passo** | **Fazê-lo...** | **E utilizar estes documentos.** |
| --- | --- | --- | --- |
| 1. |**Configurar o portal do Azure** |Criar e configurar o serviço StorSimple Manager do dispositivo antes de aprovisionamento de uma matriz de Virtual StorSimple. |[Preparar o portal](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**Aprovisionar a matriz Virtual** |Para o Hyper-V, Aprovisione e ligar a uma matriz de Virtual StorSimple num sistema anfitrião com Hyper-V no Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2. <br></br> <br></br> Para VMware, Aprovisione e ligar a uma matriz de Virtual StorSimple num sistema anfitrião com o VMware ESXi 5.0, 5.5 ou 6.0.<br></br> |[Aprovisionar uma matriz virtual no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprovisionar uma matriz virtual do VMware](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Configurar a matriz Virtual** |Para o servidor de ficheiros, execute a configuração inicial, registar o servidor de ficheiros do StorSimple e concluir a configuração do dispositivo. Em seguida, pode aprovisionar partilhas SMB. <br></br> <br></br> Para o servidor de iSCSI, execute a configuração inicial, registar o servidor de iSCSI StorSimple e concluir a configuração do dispositivo. Em seguida, pode aprovisionar volumes de iSCSI. |[Configurar a matriz virtual como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Configurar matriz virtual como servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Agora pode começar a configurar o portal do Azure.

## <a name="configuration-checklist"></a>Lista de verificação de configuração

A lista de verificação de configuração descreve as informações que precisa para recolher antes de configurar o software na sua matriz de Virtual StorSimple. A preparar a estas informações antecedência ajuda a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Dependendo se a matriz de Virtual StorSimple é implementada como um servidor de ficheiros ou um servidor de iSCSI, precisa de um dos seguintes listas de verificação.

* Transferir o [lista de verificação do StorSimple matriz Virtual ficheiro servidor configuração](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Transferir o [iSCSI da matriz Virtual StorSimple lista de verificação de configuração de servidor](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Pré-requisitos

Aqui pode encontrar os pré-requisitos de configuração para o serviço StorSimple Manager de dispositivo, a matriz de Virtual StorSimple e a rede de centro de dados.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple

Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* A subscrição do Microsoft Azure deve estar ativada para o serviço do Gestor de dispositivos do StorSimple.

### <a name="for-the-storsimple-virtual-array"></a>Para a matriz de Virtual StorSimple

Antes de implementar uma matriz de virtual, certifique-se de que:

* Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2008 R2 ou posterior ou VMware (ESXi 5.0, 5.5 ou 6.0) que pode ser utilizado para um aprovisionar um dispositivo.
* O sistema anfitrião é capaz de dedicar os seguintes recursos para aprovisionar a matriz de virtual:
  
  * Um mínimo de 4 núcleos.
  * Pelo menos 8 GB de RAM. Se pretender configurar a matriz virtual como servidor de ficheiros, 8 GB suporta ficheiros de milhões de 2. Terá de 16 GB de RAM para 2-4 milhões de ficheiros de suporte.
  * Uma interface de rede.
  * Um disco virtual 500 GB para os dados de sistema.

### <a name="for-the-datacenter-network"></a>Para a rede de centro de dados

Antes de começar, certifique-se de que:

* A rede no seu centro de dados está configurada de acordo com os requisitos de rede para o dispositivo StorSimple. Para obter mais informações, consulte o [requisitos de sistema de matriz Virtual StorSimple](storsimple-ova-system-requirements.md).
* A matriz de Virtual StorSimple tem uma banda de Internet de Mbps 5 dedicado (ou mais) disponível em todas as vezes. Esta largura de banda não deve ser partilhada com outras aplicações.

## <a name="step-by-step-preparation"></a>Preparação passo a passo

Utilize as seguintes instruções passo a passo para preparar o seu portal para o serviço do Gestor de dispositivos do StorSimple.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço

Uma única instância do serviço Gestor de dispositivos do StorSimple pode gerir várias matrizes de Virtual StorSimple. Execute os seguintes passos para criar uma instância do serviço Gestor de Dispositivos do StorSimple. Se tiver um serviço StorSimple Manager de dispositivo existente para gerir as matrizes de virtuais, ignore este passo e vá para [passo 2: obter a chave de registo do serviço](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#optional-step-configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se tiver ativado a criação automática de uma conta do Storage, vá para [Passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço

Assim que o serviço Gestor de Dispositivos do StorSimple estiver ativo e em execução, será necessário obter a chave de registo do serviço. Utilize esta chave para registar e ligar o dispositivo StorSimple ao serviço.

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A chave de registo do serviço é utilizada para registar todos os dispositivos de Gestor de dispositivos do StorSimple que tem de registar com o serviço do Gestor de dispositivos do StorSimple.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Passo 3: Transferir a imagem de matriz virtual

Depois de ter a chave de registo do serviço, terá de transferir a imagem de matriz virtual adequados para Aprovisionar uma matriz virtual no sistema anfitrião. As imagens de matriz virtual são específicas do sistema operativo e podem ser transferidas a partir da página de início rápido no portal do Azure.

> [!IMPORTANT]
> O software em execução na matriz Virtual StorSimple só pode ser utilizado com o serviço do Gestor de dispositivos do StorSimple.
> 
> 

Execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Para obter a imagem de matriz virtual

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/). 
2. No portal do Azure, clique em **Procurar > gestores de dispositivos do StorSimple**.
3. Selecione um serviço StorSimple Manager de dispositivo existente. No **Gestor de dispositivos do StorSimple** painel, clique em **início rápido**. 
4. Clique na ligação correspondente para a imagem que pretende transferir a partir do Microsoft Download Center. Os ficheiros de imagem são aproximadamente 4.8 GB.
   
   * VHDX para Hyper-V no Windows Server 2012 e versões posteriores
   * VHD para o Hyper-V no Windows Server 2008 R2 e versões posteriores
   * VMDK para VMWare ESXi 5.0, 5.5 ou 6.0
5. Transfira e deszipe o ficheiro para uma unidade local, fazer uma nota de onde está localizado o ficheiro deszipado.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Passo opcional: configurar uma nova conta de armazenamento para o serviço

Este passo é opcional e deve ser efetuado apenas se não tiver ativado a criação automática de uma conta de armazenamento com o serviço.

Se precisar de criar uma conta de armazenamento do Azure numa região diferente, consulte [como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter instruções passo a passo.

Execute os seguintes passos no [portal do Azure](https://ms.portal.azure.com/) na página de serviço do Gestor de dispositivos do StorSimple para adicionar uma conta de armazenamento existente do Microsoft Azure.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço do Gestor de dispositivos

1. Navegue até ao seu serviço do Gestor de dispositivos, selecione e faça duplo clique. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro de **configuração** secção.
3. Clique em **Adicionar**.
4. No **adicionar uma conta de armazenamento** painel, efetue o seguinte procedimento:
   
    1. Para **subscrição**, selecione **atual**.
   
    2. Forneça o nome da sua conta de armazenamento do Azure.
   
    3. Selecione **ativar** para criar um canal seguro para a comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **desativar** apenas se estiver a utilizar uma nuvem privada.
   
    4. Clique em **Adicionar**. Será notificado depois da conta de armazenamento é criada com êxito.<br></br>
   
     ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Passo seguinte

O passo seguinte é para Aprovisionar uma máquina virtual para a matriz de Virtual StorSimple. Consoante o sistema operativo anfitrião, consulte as instruções detalhadas:

* [Aprovisionar uma matriz de Virtual StorSimple no Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Aprovisionar uma matriz de Virtual StorSimple no VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

