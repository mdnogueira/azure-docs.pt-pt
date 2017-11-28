---
title: "Perguntas mais frequentes sobre a migração de armazenamento do Azure | Microsoft Docs"
description: "Respostas a questões recorrentes sobre migrar armazenamento do Azure"
services: storage
documentationcenter: na
author: genlin
manager: timlt
editor: tysonn
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 11/16/2017
ms.author: genli
ms.openlocfilehash: 516a0487afe11ef6915a002375661a23eaf13edc
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Perguntas mais frequentes sobre a migração de armazenamento do Azure

Este artigo responde a questões recorrentes sobre a migração de armazenamento do Azure. 

## <a name="faq"></a>FAQ

**Como criar um script para copiar ficheiros de um contentor para outro?**

Para copiar ficheiros entre contentores, pode utilizar o AzCopy. Consulte o exemplo seguinte:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

Utiliza o AzCopy [API de BLOBs de cópia](https://docs.microsoft.com/rest/api/storageservices/copy-blob) para fazer a cópia para cada ficheiro no contentor.  
  
Pode utilizar qualquer máquina virtual ou o computador local que tenha acesso à Internet para executar o AzCopy. Também pode utilizar o Azure Batch agenda para fazê-lo automaticamente, mas tem mais complicou.  
  
O script de automatização foi concebido para a implementação do Azure Resource Manager, em vez de manipulação de conteúdo do armazenamento. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

**Existe qualquer cobrança envolvidos para copiar dados entre duas partilhas de ficheiros diferentes na mesma conta do storage na mesma região?**

Não. Não há sem encargos durante este processo.

**Como posso fazer a cópia de segurança a minha conta de armazenamento completa para outra conta de armazenamento?**

Não há nenhuma opção para efetuar uma cópia de segurança de uma conta de armazenamento todo diretamente. Mas pode manualmente mover o contentor nessa conta de armazenamento para outra conta utilizando o AzCopy ou no Explorador de armazenamento. Os passos seguintes mostram como utilizar o AzCopy para mover o contentor:  
 

1.  Instalar o [AzCopy](storage-use-azcopy.md) ferramenta da linha de comandos. Esta ferramenta ajuda a mover o ficheiro VHD entre contas de armazenamento.

2.  Depois de instalar o AzCopy no Windows utilizando o instalador, abra uma janela de comando POromprt e, em seguida, navegue para a pasta de instalação do AzCopy no seu computador. Por predefinição, o AzCopy é instalado para **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** ou **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.   Execute o seguinte comando para mover o contentor. Tem de substituir o texto com o valor real.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

        - /Source: forneça o URI da conta de armazenamento a origem (até contentor)  
        - /dest: forneça a conta de armazenamento de destino URI (até contentor)  
        - /sourcekey: forneça armazenamento de origem chave primária da conta, pode copiar esta chave do portal, selecionando a conta de armazenamento.  
        - /destkey: forneça armazenamento de destino chave primária da conta, pode copiar esta chave do portal, selecionando a conta de armazenamento.

Depois de executar este comando, os ficheiros de contentor são movidos para a conta de armazenamento de destino.

**A CLI do AzCopy não funciona em conjunto com o parâmetro de "Padrão" quando copiar a partir do Azure de um blob para outro.**

Pode diretamente copiar e editar o AzCopy cmd e efetuar uma verificação cruzada para se certificar de que o padrão corresponde de origem. Certifique-se também que **/S** carateres universais estão em vigor. Para obter mais informações, consulte [AzCopy parâmetros](storage-use-azcopy.md).

**Como mover dados de um contentor de armazenamento para outro?**

Para tal, siga estes passos:

1.  Crie o contentor (pasta) no blob de destino.

2.  Utilize [Azcopy](https://azure.microsoft.com/en-us/blog/azcopy-5-1-release/) para copiar o conteúdo do contentor do blob original para um contentor de Blob diferente.

**Como criar um script do PowerShell para mover dados de uma partilha de ficheiros do Azure para outro armazenamento do Azure?**

Utilize o AzCopy para mover os dados de uma partilha de ficheiros do Azure para outro armazenamento do azure. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como se carregar ficheiros grandes. csv para o armazenamento do Azure?**

Utilize o AzCopy para carregar ficheiros grandes. csv para o Storage do Azure. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Tem de mover os registos de "unidade D" para a minha conta de armazenamento do Azure todos os dias. Como automatizar este?**

Pode utilizar o AzCopy e criar uma tarefa no programador de tarefas. Carregar ficheiros para uma conta de armazenamento do Azure, utilizando um script de comandos do AzCopy. Para obter mais informações, consulte [como configurar e executar tarefas de arranque para um serviço em nuvem](../../cloud-services/cloud-services-startup-tasks.md).

**Como posso mover minha conta de armazenamento entre subscrições?**

Utilize o AzCopy para mover a sua conta de armazenamento entre subscrições. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como pode posso mover 10 TB de dados para armazenamento em noutra região?**

Utilize o AzCopy para mover os dados. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como copiar dados no local para o armazenamento do Azure?**

Utilize o AzCopy para copiar os dados. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mover dados no local para o serviço de ficheiros do Azure?**

Utilize o AzCopy para mover os dados. Para obter mais informações, para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como mapeiam uma pasta de contentor numa máquina virtual**

Utilize a partilha de ficheiros do Azure.

**Como posso fazer a cópia de segurança file storage do Azure?**

Não há nenhuma solução de cópia de segurança. No entanto ficheiros do Azure também suporta a cópia async. Por isso, pode copiar ficheiros de uma partilha para outra partilha (dentro de uma conta de armazenamento ou de uma conta de armazenamento diferente) ou para um contentor do blob (dentro de uma conta de armazenamento ou de uma conta de armazenamento diferente).
Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md).

**Como posso mover discos geridos para outra conta de armazenamento?**

Para tal, siga estes passos:

1.  Pare a máquina virtual que o disco gerido está ligado a.

2.  Copie o disco gerido VHD de uma área para outro, executando o seguinte script do PowerShell do Azure:

    ```
    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionId <ID>

    $sas = Grant-AzureRmDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzureStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzureStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Crie um disco gerido utilizando o ficheiro VHD noutra região onde copiou o VHD. Para tal, execute o seguinte script do PowerShell do Azure:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Para obter mais informações sobre como implementar uma máquina virtual a partir de um disco gerido, consulte [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Como posso transferir sobre 1-2 TB de dados a partir do portal do Azure?**

Utilize o AzCopy para transferir os dados. Para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).

**Como posso alterar a localização secundária para a região Europa para uma conta de armazenamento?**

Quando cria uma conta de armazenamento, selecione a região primária para a conta. A seleção da região secundária é com base na região primária e não pode ser alterada. Consulte [replicação de armazenamento do Azure](storage-redundancy.md).

**Onde posso obter mais informações sobre a encriptação de serviço de armazenamento do Azure (SSE)?**  
  
Consulte os seguintes artigos:

-  [Guia de segurança de armazenamento do Azure](storage-security-guide.md)

-   [Encriptação do serviço de armazenamento do Azure para dados Inativos](storage-service-encryption.md)

**Como mover ou transferir dados a partir de uma conta de armazenamento?**

Utilize o AzCopy para transferir os dados. Para obter mais informações, para obter mais informações, consulte [transferir dados com o AzCopy no Windows](storage-use-azcopy.md) e [transferir dados com o AzCopy no Linux](storage-use-azcopy-linux.md).


**Como encriptar dados numa conta de armazenamento?**

Depois de ativar a encriptação na conta de armazenamento, os dados existentes não são encriptados. Para encriptar os dados existentes, tem de carregar novamente para os dados para a conta de armazenamento.  Para tal, siga estes passos:

Utilize o AZcopy para copiar os dados para uma conta de armazenamento diferente e, em seguida, regresse a uma conta de armazenamento. Também pode utilizar [encriptação de Inativos](storage-service-encryption.md).

**Como transferir um VHD para uma máquina local, exceto utilizando a opção de transferência no portal?**

Pode utilizar [Explorador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) para transferir um VHD.

**Existem pré-requisitos para alterar a replicação de uma conta de armazenamento de GRS para LRS?**

Não. 

**Como aceder a armazenamento com redundância de ficheiros do Azure?**

Armazenamento Georredundante com acesso de leitura (RA-GRS) é necessário para aceder ao armazenamento com redundância. No entanto, os ficheiros do Azure suporta apenas LRS e GRS padrão que não permite o acesso só de leitura. 

**Como posso mover de Premium Storage para o armazenamento Standard?**

Para tal, siga estes passos:

1.  Criar uma nova conta de armazenamento Standard (ou pode utilizar uma conta de armazenamento Standard existente na sua subscrição).

2.  Transfira o AzCopy. Execute um dos seguintes comandos do AzCopy.
      
    Para copiar todo discos na conta de armazenamento:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Para copiar um disco apenas, forneça o nome do disco padrão

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
Pode demorar várias horas a concluir a operação.

Para se certificar de que a transferência foi concluída com êxito, examine o contentor de conta de armazenamento de destino no portal do Azure. Depois dos discos são copiados para a conta de armazenamento standard, pode anexá-los para a máquina virtual como um disco existente. Para obter mais informações, consulte [como anexar um disco de dados geridos para uma máquina virtual do Windows no portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Como posso converter para armazenamento Premium para uma partilha de ficheiros?**

Armazenamento Premium não é permitido numa partilha de ficheiros do Azure.

**Como atualizar a partir de um armazenamento Standard para uma conta do Premium Storage? Como posso mudar de um armazenamento Premium para uma conta de armazenamento Standard?**

- Tem de criar a conta de armazenamento de destino, copiar dados a partir da conta de origem para a conta de destino e, em seguida, elimine a conta de origem.

- Pode utilizar uma ferramenta como o AzCopy para efetuar a cópia de dados.

- Se também tiver máquinas virtuais, existem vários passos adicionais que deve tomar antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrar para o Premium Storage do Azure (discos não gerido)](storage-migration-to-premium-storage.md).

**Como posso mover de uma conta de armazenamento clássico para uma conta do Storage do Azure Resource Manager?**

1.  Pode utilizar o cmdlet Move-AzureStorageAccount.

2.  Este cmdlet tem vários passos (validar, preparar, consolidação) e pode validar a movimentação antes que efetuar.

3.  Se também tiver máquinas virtuais, existem vários passos adicionais que deve tomar antes de migrar os dados da conta de armazenamento. Para obter mais informações, consulte [migrar recursos IaaS do clássico para o Azure Resource Manager com o Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md).

**Como posso transferir dados para um computador baseado em Linux a partir de uma conta de armazenamento do Azure ou carregar dados a partir de uma máquina com Linux?**

Pode utilizar a CLI do Azure.

-   Transferir um blob único

        azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

-   Carregar um blob único: 

        azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Como posso deu outras pessoas acesso a recursos de armazenamento?**

Para conceder acesso a outras pessoas para os recursos de armazenamento:

-   Utilize um token de assinatura de acesso partilhado (SAS) para fornecer acesso a um recurso. 

-   Forneça um utilizador com a chave primária ou secundária para a conta de armazenamento. Para obter mais informações, consulte [gerir a sua conta de armazenamento](storage-create-storage-account.md#manage-your-storage-account).

-   Altere a política de acesso para permitir o acesso anónimo. Para obter mais informações, consulte [conceder permissões de utilizadores anónimos a contentores e blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**Onde está instalado o AzCopy?**

-   Se aceder AzCopy a partir de "linha de comandos de armazenamento do Microsoft Azure", escreva 'AzCopy'. A linha de comandos é instalada juntamente com o AzCopy.

-   Se instalou a versão de 32 bits, será localizado aqui: **% ProgramFiles (x86) %\\Microsoft SDKs\\Azure\\AzCopy.**

-   Se instalou a versão de 64 bits, será localizado aqui: **% ProgramFiles %\\Microsoft SDKs\\Azure\\AzCopy**.

**Para uma conta de armazenamento replicado (por exemplo, o ZRS, GRS ou RA-GRS), como posso aceder aos dados armazenados na região secundária?**

-   Se estiver a utilizar o armazenamento com redundância de zona (ZRS) ou armazenamento Georredundante (GRS), não é possível aceder aos dados da região secundária, exceto se ocorrer uma ativação pós-falha. Para obter mais informações sobre o processo de ativação pós-falha, consulte [o que pode esperar se ocorre uma ativação pós-falha de armazenamento](storage-disaster-recovery-guidance.md#what-to-expect-if-a-storage-failover-occurs).

-   Se estiver a utilizar o armazenamento Georredundante com acesso de leitura (**RA-GRS**), pode aceder aos dados da região secundária em qualquer altura. Para tal, utilize um dos seguintes métodos:  
      
    AzCopy: Acrescentar '-secundário ' para o nome da conta de armazenamento no URL para aceder ao ponto final secundário. Por exemplo:  
     
    https://storageaccountname-Secondary.blob.Core.Windows.NET/VHDs/BlobName.vhd

    SAS Token: Utilize um token SAS para aceder a dados do ponto final. Para obter mais informações, consulte [Using partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).

**Como utilizar um domínio personalizado de HTTPS com a minha conta de armazenamento? Por exemplo, como torno "https://mystorageaccountname.blob.core.windows.net/images/image.gif" aparecem como "https://www.contoso.com/images/image.gif"?**

SSL não é atualmente suportado em contas de armazenamento com domínios personalizados.
Mas pode utilizar domínios personalizados não-HTTPS. Para obter mais informações, consulte [configurar um nome de domínio personalizado para o ponto de final do Blob storage](../blobs/storage-custom-domain-name.md).

**Como utilizar o FTP para aceder a dados que está a ser uma conta de armazenamento?**

Não é possível aceder a uma conta de armazenamento diretamente através da utilização de FTP. No entanto, pode configurar uma máquina virtual do Azure e, em seguida, instalar um servidor FTP na máquina virtual. Pode fazer com o servidor FTP armazenar os ficheiros numa partilha de ficheiros do Azure ou num disco de dados que está disponível para a máquina virtual.
Se pretender apenas transferir dados sem ter de utilizar o Explorador de armazenamento ou uma aplicação semelhante, poderá conseguir utilizar um token SAS. Para obter mais informações, consulte [Using partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.