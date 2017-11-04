---
title: Criar e carregar uma imagem de FreeBSD VM | Microsoft Docs
description: "Saiba como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo FreeBSD para criar uma máquina virtual do Azure"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 0010e01d4333b96696680ec6fbbeee74b17f46a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Criar e carregar um VHD de FreeBSD para o Azure
Este artigo mostra como criar e carregar um disco rígido virtual (VHD) que contém o sistema de operativo FreeBSD. Depois de carregá-lo, pode utilizá-lo como a sua própria imagem para criar uma máquina virtual (VM) no Azure.

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre como carregar um VHD com o modelo do Resource Manager, consulte [aqui](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem os seguintes itens:

* **Uma subscrição do Azure**– se não tiver uma conta, pode criar um em apenas alguns minutos. Se tiver uma subscrição do MSDN, consulte [crédito do Azure mensal para subscritores do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Caso contrário, saiba como [criar uma conta de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Ferramentas do Azure PowerShell**– o Azure PowerShell módulo tem de ser instalado e configurado para utilizar a sua subscrição do Azure. Para transferir o módulo, consulte [Azure transfere](https://azure.microsoft.com/downloads/). Está disponível um tutorial que descreve como instalar e configurar o módulo. Utilize o [Azure transfere](https://azure.microsoft.com/downloads/) cmdlet para carregar o VHD.
* **Sistema de operativo FreeBSD instalado num ficheiro. vhd**– suportado FreeBSD instalado um sistema operativo tem de ser um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd. Por exemplo, pode utilizar uma solução de virtualização, tais como o Hyper-V para criar o ficheiro. vhd e instalar o sistema operativo. Para obter instruções sobre como instalar e utilizar o Hyper-V, consulte [instalar o Hyper-V e criar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> O formato VHDX mais recente não é suportado no Azure. Converter disco em formato VHD utilizando o Gestor de Hyper-V ou o cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Além disso, não há um [tutorial no MSDN sobre como utilizar FreeBSD com Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Esta tarefa inclui os seguintes cinco passos:

## <a name="step-1-prepare-the-image-for-upload"></a>Passo 1: Preparar a imagem para carregamento
Na máquina virtual em que instalou o sistema de operativo FreeBSD, conclua os procedimentos seguintes:

1. Ative o DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Ative SSH.

    Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque. Por predefinição está ativada após a instalação a partir do disco de FreeBSD. 
3. Configure uma consola de série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Instale o sudo.

    A conta de raiz está desativada no Azure. Isto significa que terá de utilizar o sudo de um utilizador sem privilégios para executar comandos com privilégios elevados.

        # pkg install sudo
   
5. Pré-requisitos para o agente do Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Instale o agente do Azure.

    A versão mais recente do agente do Azure pode sempre ser encontrada no [github](https://github.com/Azure/WALinuxAgent/releases). A versão 2.0.10 + oficialmente suporta FreeBSD 10 & 10.1 e a versão 2.1.4 + (incluindo 2.2.x) suporta oficialmente FreeBSD 10.2 e versões posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, vamos utilizar 2.0.16 como um exemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, vamos utilizar 2.1.4 como um exemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Depois de instalar o agente do Azure, é boa ideia Certifique-se de que está em execução:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Desaprovisionar o sistema.

    O sistema para limpá-lo e torná-lo adequado para reprovisioning retirar o aprovisionamento. O comando seguinte também elimina a última conta de utilizador aprovisionado e os dados associados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Agora pode desligar a VM.

## <a name="step-2-create-a-storage-account-in-azure"></a>Passo 2: Criar uma conta de armazenamento no Azure
Necessita de uma conta de armazenamento do Azure para carregar um ficheiro. vhd para que possam ser utilizado para criar uma máquina virtual. Pode utilizar o portal clássico do Azure para criar uma conta de armazenamento.

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com).
2. Na barra de comandos, selecione **novo**.
3. Selecione **serviços de dados** > **armazenamento** > **criação rápida**.

    ![Guia de introdução cria uma conta do storage](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Preencha os campos da seguinte forma:

   * No **URL** campo, escreva um nome de subdomínio para utilizar no URL da conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Este nome torna-se o nome de anfitrião no URL que é utilizado para resolver o Blob storage do Azure, o armazenamento de filas do Azure ou recursos de armazenamento de tabelas do Azure para a subscrição.
   * No **localização/grupo de afinidades** menu pendente, escolha o **localização ou o grupo de afinidade** para a conta de armazenamento. Um grupo de afinidades permite-lhe colocar os seus serviços em nuvem e armazenamento no mesmo centro de dados.
   * No **replicação** campo, decida se pretende utilizar **Georredundante** replicação para a conta de armazenamento. A georreplicação está ativada por predefinição. Esta opção replica os dados para uma localização secundária, sem custos para si, para que o armazenamento de ativação pós-falha para essa localização se ocorrer uma falha principais na localização principal. A localização secundária é atribuída automaticamente e não pode ser alterada. Se precisar de mais controlo sobre a localização do armazenamento baseado em nuvem devido a requisitos legais ou política organizacional, pode desativar a georreplicação. No entanto, tenha em atenção de que se ligar mais tarde georreplicação, será cobrada uma taxa de transferência de dados de uma única para replicar os dados existentes para a localização secundária. Serviços de armazenamento sem georreplicação são disponibilizados com um desconto. Obter mais detalhes sobre a gestão georreplicação de contas do storage podem ser encontrados aqui: [replicação de armazenamento do Azure](../../../storage/common/storage-redundancy.md).

     ![Introduza os detalhes de conta de armazenamento](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Selecione **criar conta de armazenamento**. Agora, a conta é apresentado em **armazenamento**.

    ![Conta de armazenamento criada com êxito](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Em seguida, crie um contentor para os ficheiros. vhd carregado. Selecione o nome da conta de armazenamento e, em seguida, selecione **contentores**.

    ![Detalhes de conta de armazenamento](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Selecione **criar um contentor**.

    ![Detalhes de conta de armazenamento](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. No **nome** campo, escreva um nome para o contentor. Em seguida, no **acesso** menu pendente, selecione o tipo de política de acesso que pretende.

    ![Nome do contentor](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > Por predefinição, o contentor é privado e só pode ser acedido pelo proprietário da conta. Para permitir acesso de leitura público para os blobs no contentor, mas não os metadados e as propriedades do contentor, utilize o **Blob público** opção. Para permitir o acesso de leitura público completa para o contentor e blobs, utilize o **contentor público** opção.
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Passo 3: Preparar a ligação para o Azure
Antes de pode carregar um ficheiro. vhd, tem de estabelecer uma ligação segura entre o computador e a sua subscrição do Azure. Pode utilizar o método do Azure Active Directory (Azure AD) ou o método de certificado para fazê-lo.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Utilize o método do Azure AD para carregar um ficheiro. vhd
1. Abra a consola do Azure PowerShell.
2. Escreva o seguinte comando:  
    `Add-AzureAccount`

    Este comando abre uma janela de início de sessão onde podem iniciar sessão com a sua conta escolar ou profissional.

    ![Janela do PowerShell](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure efetua a autenticação e guarda as informações de credenciais. Em seguida, fecha a janela.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Utilize o método de certificado para carregar um ficheiro. vhd
1. Abra a consola do Azure PowerShell.
2. Tipo: `Get-AzurePublishSettingsFile`.
3. Uma janela do browser abre e pede-lhe para transferir o ficheiro. publishsettings. Este ficheiro contém informações e um certificado para a sua subscrição do Azure.

    ![Página de transferência do browser](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Guarde o ficheiro. publishsettings.
5. Tipo: `Import-AzurePublishSettingsFile <PathToFile>`, onde `<PathToFile>` é o caminho completo para o ficheiro. publishsettings.

   Para obter mais informações, consulte [introdução aos cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para obter mais informações sobre como instalar e configurar o PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>Passo 4: Carregar o ficheiro. vhd
Ao carregar o ficheiro. vhd, pode colocá-lo em qualquer lugar no seu armazenamento de Blobs. Seguem-se alguns termos que será utilizado ao carregar o ficheiro:

* **BlobStorageURL** é o URL para a conta de armazenamento que criou no passo 2.
* **YourImagesFolder** é o contentor no armazenamento de BLOBs de onde pretende armazenar as imagens.
* **VHDName** é a etiqueta que aparece no portal clássico do Azure para identificar o disco rígido virtual.
* **PathToVHDFile** é o caminho completo e nome do ficheiro. vhd.

A partir da janela do PowerShell do Azure que utilizou no passo anterior, escreva:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Passo 5: Criar uma VM com o ficheiro. vhd carregado
Depois de carregar o ficheiro. vhd, pode adicioná-lo como uma imagem para a lista de imagens personalizadas que estão associados com a sua subscrição e crie uma máquina virtual com esta imagem personalizada.

1. A partir da janela do PowerShell do Azure que utilizou no passo anterior, escreva:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Utilize o Linux como o tipo de SO. A versão atual do PowerShell do Azure aceita apenas "Linux" ou "Windows" como um parâmetro.
   >
   >
2. Depois de concluir os passos anteriores, a nova imagem está listada quando escolhe o **imagens** separador no portal clássico do Azure.  

    ![Escolha uma imagem](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Crie uma máquina virtual a partir da galeria. Esta nova imagem está agora disponível em **as minhas imagens**.
4. Selecione a imagem de novo. Em seguida, seguir as instruções para configurar um nome de anfitrião, a palavra-passe, a chave SSH e assim sucessivamente.

    ![Imagem personalizada](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Depois de concluir o aprovisionamento, verá a VM FreeBSD em execução no Azure.

    ![Imagem de FreeBSD no azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
