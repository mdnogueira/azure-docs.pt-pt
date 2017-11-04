---
title: Migrar VMs para o armazenamento Premium do Azure | Microsoft Docs
description: "Migre as suas VMs existentes para o Premium Storage do Azure. Armazenamento Premium oferece suporte de disco de elevado desempenho, baixa latência para cargas de trabalho de I/O intensivo em execução em máquinas de virtuais do Azure."
services: storage
documentationcenter: na
author: yuemlu
manager: tadb
editor: tysonn
ms.assetid: 272250b3-fd4e-41d2-8e34-fd8cc341ec87
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: yuemlu
ms.openlocfilehash: cb46c3f2809fa86fea7a8370d4c417f04040b74c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="migrating-to-azure-premium-storage-unmanaged-disks"></a>Migrar para o armazenamento do Azure Premium (discos não geridos)

> [!NOTE]
> Este artigo descreve como migrar uma VM que utiliza discos padrão não geridos para uma VM que utiliza discos premium não gerido. Recomendamos que utilize discos gerida do Azure para novas VMs e que converta os discos não geridos anteriores em discos geridos. Gerido identificador discos as contas de armazenamento subjacente, pelo que não tem. Para obter mais informações, consulte a nossa [descrição geral de discos geridos](../../virtual-machines/windows/managed-disks-overview.md).
>

Armazenamento Premium do Azure fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais em execução I/O intensivo cargas de trabalho. Pode tirar partido da velocidade e o desempenho destes discos ao migrar discos VM da sua aplicação para o Premium Storage do Azure.

O objetivo deste guia é ajudar os utilizadores novos do armazenamento do Azure Premium melhor preparar a fazerem uma transição tranquila do seu sistema atual para o Premium Storage. O guia aborda três componentes chave neste processo:

* [Planear a migração para o Premium Storage](#plan-the-migration-to-premium-storage)
* [Preparar e copiar os discos rígidos virtuais (VHDs) para o Premium Storage](#prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage)
* [Criar a máquina de Virtual do Azure utilizando o armazenamento Premium](#create-azure-virtual-machine-using-premium-storage)

Pode migrar VMs de outras plataformas para o Premium Storage do Azure ou migrar VMs do Azure existentes de armazenamento Standard para armazenamento Premium. Este guia aborda os passos para ambos os dois cenários. Siga os passos especificados na secção relevante dependendo do seu cenário.

> [!NOTE]
> Pode encontrar uma descrição geral das funcionalidades e preços do Premium Storage no armazenamento Premium: [armazenamento de elevado desempenho para cargas de trabalho de Máquina Virtual de Azure](../../virtual-machines/windows/premium-storage.md). Recomendamos que migrar qualquer disco da máquina virtual que requerem o IOPS elevado para o Premium Storage do Azure para o melhor desempenho para a sua aplicação. Se o disco não necessita de IOPS elevado, pode limitar os custos, mantendo-lo no armazenamento padrão, que armazena dados de disco da máquina virtual em unidades de disco rígido (HDDs) em vez de SSDs.
>

A concluir o processo de migração na íntegra pode necessitar de ações adicionais antes e depois dos passos fornecidos neste guia. Os exemplos incluem configurar redes virtuais ou pontos finais ou efetuar alterações de código dentro da própria aplicação que podem necessitar de algum período de indisponibilidade na sua aplicação. Estas ações são exclusivas para cada aplicação e, deve ser concluído, juntamente com os passos fornecidos neste guia para fazer a transição de completa para o armazenamento Premium como totalmente integrada quanto possível.

## <a name="plan-the-migration-to-premium-storage"></a>Planear a migração para o Premium Storage
Esta secção garante que está pronto para seguir os passos de migração neste artigo e ajuda-o a tomar a decisão de melhor em tipos de disco e de VM.

### <a name="prerequisites"></a>Pré-requisitos
* Precisa de uma subscrição do Azure. Se não tiver uma, pode criar um mês [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) subscrição ou visite [preços do Azure](https://azure.microsoft.com/pricing/) para mais opções.
* Para executar os cmdlets do PowerShell, terá do módulo do PowerShell do Microsoft Azure. Veja [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para obter as instruções de instalação e do ponto de instalação.
* Quando planeia utilizar VMs do Azure em execução no armazenamento Premium, terá de utilizar as VMs com capacidade de armazenamento Premium. Pode utilizar discos Standard e Premium Storage com VMs com capacidade de armazenamento Premium. No futuro, os discos de armazenamento Premium estará disponíveis com mais tipos VM. Para obter mais informações sobre todos os tamanhos e tipos de disco da VM do Azure disponíveis, consulte [tamanhos das virtual machines](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [tamanhos para serviços em nuvem](../../cloud-services/cloud-services-sizes-specs.md).

### <a name="considerations"></a>Considerações
VM do Azure suporta anexar vários discos de armazenamento Premium, para que as aplicações podem ter até 256 TB de armazenamento por VM. Armazenamento Premium, as suas aplicações podem alcançar 80.000 (operações de entrada/saída por segundo) de IOPS por VM e 2000 MB por segundo débito de disco por VM com latências extremamente baixa para operações de leitura. Tem opções numa variedade de discos e as VMs. Esta secção é ajudar a localizar uma opção que melhor se adapta às sua carga de trabalho.

#### <a name="vm-sizes"></a>Tamanhos de VM
As especificações de tamanho da VM do Azure estão listadas na [tamanhos das virtual machines](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Reveja as características de desempenho de máquinas virtuais que funcionam com o Premium Storage e escolha o tamanho VM mais adequado que melhor se adapta às sua carga de trabalho. Certifique-se de que existe largura de banda suficiente disponível na VM para o tráfego de disco da unidade.

#### <a name="disk-sizes"></a>Tamanhos de disco
Existem cinco tipos de discos que podem ser utilizados com a VM e cada um tem IOPs específicos e débito limites. Tenha em consideração estes limites quando escolher o tipo de disco para a VM com base nas necessidades da sua aplicação em termos de capacidade, desempenho, escalabilidade e carrega das horas de ponta.

| Tipo de discos Premium  | P10   | P20   | P30            | P40            | P50            | 
|:-------------------:|:-----:|:-----:|:--------------:|:--------------:|:--------------:|
| Tamanho do disco           | 128 GB| 512 GB| 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 
| IOPs por disco       | 500   | 2300  | 5000           | 7500           | 7500           | 
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

Consoante a carga de trabalho, determine se a discos de dados adicionais são necessários para a VM. Pode anexar vários discos de dados persistentes à VM. Se for necessário, pode stripe nos discos para aumentar a capacidade e o desempenho do volume. (Ver o disco Striping [aqui](../../virtual-machines/windows/premium-storage-performance.md#disk-striping).) Se stripe utilizando discos de dados de armazenamento Premium [espaços de armazenamento][4], deve configurá-lo com uma coluna para cada disco que é utilizado. Caso contrário, o desempenho global do volume repartido pode ser menor que o esperado devido a distribuição desigual de tráfego entre os discos. Para VMs com Linux, pode utilizar o *mdadm* utilitário para alcançar o mesmo. Consulte o artigo [configurar RAID de Software no Linux](../../virtual-machines/linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais detalhes.

#### <a name="storage-account-scalability-targets"></a>Objetivos de escalabilidade da conta de armazenamento
Os seguintes destinos de escalabilidade, para além de ter contas de armazenamento Premium o [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md). Se os requisitos de aplicação excedem os destinos de escalabilidade de uma única conta de armazenamento, crie a sua aplicação para utilizar várias contas de armazenamento e os dados de partição entre essas contas de armazenamento.

| Capacidade total de conta | Largura de banda total de uma conta de armazenamento localmente redundante |
|:--- |:--- |
| Capacidade do disco: 35TB<br />Capacidade de instantâneos: 10 TB |Até 50 gigabits por segundo para entrada + saída |

Para obter mais informações sobre as especificações de Premium Storage, consulte [metas de desempenho ao utilizar o Premium Storage e escalabilidade](../../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets).

#### <a name="disk-caching-policy"></a>Política de colocação em cache em disco
Por predefinição, o disco de política de colocação em cache é *só de leitura* para todos os dados discos Premium, e *leitura e escrita* para o disco de sistema operativo Premium ligados à VM. Esta definição de configuração é recomendada para alcançar o desempenho ideal para IOs a aplicação. Pesado de escrita ou só de escrita para discos de dados (por exemplo, ficheiros de registo do SQL Server), desative a colocação em cache no disco, de modo a que pode alcançar um melhor desempenho de aplicações. As definições de cache existente para discos de dados podem ser atualizadas com [Portal do Azure](https://portal.azure.com) ou *- HostCaching* parâmetro do *AzureDataDisk conjunto* cmdlet.

#### <a name="location"></a>Localização
Escolha uma localização onde o Premium Storage do Azure está disponível. Consulte [serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas em localizações disponíveis. VMs localizadas na mesma região que a conta de armazenamento que armazena os discos para a VM irão dar-muito melhor desempenho do que o se estiverem em regiões separadas.

#### <a name="other-azure-vm-configuration-settings"></a>Outras definições de configuração de VM do Azure
Ao criar uma VM do Azure, será pedido para configurar determinadas definições de VM. Lembre-se de que alguns corrigidas as definições para a duração da VM, enquanto as pode modificar ou adicionar outras pessoas mais tarde. Reveja estas definições de configuração de VM do Azure e certifique-se de que estes estão configurados corretamente para corresponder aos seus requisitos de carga de trabalho.

### <a name="optimization"></a>Otimização
[Armazenamento Premium do Azure: Estruturar para desempenho elevado](../../virtual-machines/windows/premium-storage-performance.md) fornece diretrizes para a criação de aplicações de elevado desempenho com o Premium Storage do Azure. Pode seguir as diretrizes combinadas com as melhores práticas aplica-se a tecnologias utilizadas pela sua aplicação.

## <a name="prepare-and-copy-virtual-hard-disks-VHDs-to-premium-storage"></a>Preparar e copiar os discos rígidos virtuais (VHDs) para o Premium Storage
A seguinte secção fornece orientações para preparar os VHDs da VM e copiar VHDs ao Storage do Azure.

* [Cenário 1: "Posso estou migrar VMs do Azure existentes para o Premium Storage do Azure."](#scenario1)
* [Cenário 2: "Posso estou a migrar VM de outras plataformas para Premium Storage do Azure."](#scenario2)

### <a name="prerequisites"></a>Pré-requisitos
Para preparar os VHDs para migração, terá de:

* Uma subscrição do Azure, uma conta do storage e um contentor nessa conta de armazenamento para os quais pode copiar o VHD. Tenha em atenção que a conta de armazenamento de destino pode ser uma conta Standard ou Premium, consoante os requisitos.
* Uma ferramenta para generalizar o VHD, se pretender criar várias instâncias VM a partir do mesmo. Por exemplo, sysprep para Windows ou virt-sysprep para Ubuntu.
* Uma ferramenta para carregar o ficheiro VHD para a conta de armazenamento. Consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md) ou utilize um [Explorador de armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Este guia descreve a copiar o VHD utilizando a ferramenta do AzCopy.

> [!NOTE]
> Se escolher a opção de cópia síncrona com AzCopy, para um desempenho ideal, copie o VHD, executando uma destas ferramentas a partir de uma VM do Azure que se encontra na mesma região que a conta de armazenamento de destino. Se estiver a copiar um VHD de uma VM do Azure numa região diferente, o desempenho pode ser mais lento.
>
> Para copiar uma grande quantidade de dados através de largura de banda limitada, considere [utilizando o serviço de importação/exportação do Azure para transferir dados para o Blob Storage](../storage-import-export-service.md); Isto permite-lhe transferir os seus dados através do envio unidades de disco rígido para um datacenter do Azure. Pode utilizar o serviço importar/exportar do Azure para copiar dados para uma conta de armazenamento standard apenas. Depois dos dados na sua conta de armazenamento standard, pode utilizar o [API de BLOBs de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx) ou AzCopy para transferir os dados para a sua conta de armazenamento premium.
>
> Tenha em atenção que o Microsoft Azure só suporta ficheiros VHD de tamanho fixo. Os ficheiros VHDX ou VHDs dinâmicos não são suportados. Se tiver um VHD dinâmico, pode convertê-la para a utilização de tamanho fixo de [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) cmdlet.
>
>

### <a name="scenario1"></a>Cenário 1: "Posso estou migrar VMs do Azure existentes para o Premium Storage do Azure."
Se estiver a migrar VMs do Azure existentes, pare a VM, preparar os VHDs por tipo de VHD que pretende e, em seguida, copie o VHD com o AzCopy ou PowerShell.

A VM tem de ser totalmente baixo para migrar um estado limpo. Haverá um período de indisponibilidade enquanto a migração for concluída.

#### <a name="step-1-prepare-vhds-for-migration"></a>Passo 1. Preparar os VHDs para migração
Se estiver a migrar VMs do Azure existentes para o Premium Storage, poderá ser o VHD:

* Uma imagem do sistema de operativo generalizado
* Um disco de exclusiva do sistema operativo
* Um disco de dados

A seguir Vamos guiá-estes 3 cenários para preparar o VHD.

##### <a name="use-a-generalized-operating-system-vhd-to-create-multiple-vm-instances"></a>Utilizar um VHD de sistema operativo generalizado para criar várias instâncias VM
Se estiver a carregar um VHD que será utilizado para criar várias instâncias de VM do Azure genéricas, tem primeiro de generalizar VHD um utilitário de sysprep a utilizar. Isto aplica-se a um VHD que está no local ou na nuvem. Sysprep remove quaisquer informações específicas de máquina do VHD.

> [!IMPORTANT]
> Tirar um instantâneo ou cópia de segurança a VM antes de generalizá-lo. Sysprep em execução será interrompida e desalocar a instância VM. Siga os passos abaixo para sysprep um VHD do SO Windows. Tenha em atenção que executar o comando Sysprep requerem a encerrar a máquina virtual. Para obter mais informações sobre o Sysprep, consulte [descrição geral do Sysprep](http://technet.microsoft.com/library/hh825209.aspx) ou [referência técnica do Sysprep](http://technet.microsoft.com/library/cc766049.aspx).
>
>

1. Abra uma janela de linha de comandos como administrador.
2. Introduza o seguinte comando para abrir o Sysprep:

    ```
    %windir%\system32\sysprep\sysprep.exe
    ```

3. Na ferramenta de preparação do sistema, selecione a experiência de sistema introduza Out-of-Box (OOBE), selecione a caixa de verificação Generalize, selecione **encerramento**e, em seguida, clique em **OK**, conforme mostrado na imagem abaixo. Sysprep será generalizar o sistema operativo e encerrar o sistema.

    ![][1]

Para uma VM com Ubuntu, utilize virt sysprep para alcançar o mesmo. Consulte [virt sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) para obter mais detalhes. Consulte também algumas de open source [aprovisionamento do servidor Linux software](http://www.cyberciti.biz/tips/server-provisioning-software.html) para outros sistemas de operativos Linux.

##### <a name="use-a-unique-operating-system-vhd-to-create-a-single-vm-instance"></a>Utilizar um VHD de sistema operativo exclusivo para criar uma única instância VM
Se tiver uma aplicação em execução na VM que requer que os dados específicos do computador, não generalize o VHD. Um VHD generalizado não pode ser utilizado para criar uma instância de VM do Azure exclusiva. Por exemplo, se tiver o controlador de domínio no seu VHD, executar o sysprep tornarão ineficaz como um controlador de domínio. Reveja as aplicações que executem a VM e o impacto da execução sysprep nos mesmos antes de generalizar o VHD.

##### <a name="register-data-disk-vhd"></a>Registar o VHD do disco de dados
Se tiver discos de dados no Azure a serem migradas, deve certificar-se as VMs que utilizem estes discos de dados estão a ser encerradas.

Siga os passos descritos abaixo para copiar o VHD para o Premium Storage do Azure e registá-lo como um disco de dados de aprovisionamento.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para manter os seus VHDs. Quando planear onde pretende armazenar os seus VHDs, considere os seguintes pontos:

* O destino de conta de armazenamento Premium.
* A localização da conta de armazenamento tem de ser igual à capacidade Azure VMs do Premium Storage, irá criar na fase final. Pode copiar para uma nova conta de armazenamento ou estiver a planear utilizar a mesma conta de armazenamento com base nas suas necessidades.
* Copiar e guardar a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Para discos de dados, pode optar por manter alguns discos de dados numa conta do standard storage (por exemplo, os discos que possuam armazenamento cooler), mas recomendamos vivamente que mover todos os dados de carga de trabalho de produção utilizar o armazenamento premium.

#### <a name="copy-vhd-with-azcopy-or-powershell"></a>Passo 3. Copie o VHD com o PowerShell ou o AzCopy
Terá de localizar a chave de conta de contentor caminho e o armazenamento, para processar uma destas duas opções. Chave de conta de armazenamento e caminho do contentor pode ser encontrada na **Portal do Azure** > **armazenamento**. O URL de contentor serão como "https://myaccount.blob.core.windows.net/mycontainer/".

##### <a name="option-1-copy-a-vhd-with-azcopy-asynchronous-copy"></a>Opção 1: Copiar um VHD com o AzCopy (cópia assíncrona)
Utilizar o AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, isto pode demorar tempo. Lembre-se verificar os limites de entrada/saída da conta de armazenamento ao utilizar esta opção. Consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter mais detalhes.

1. Transfira e instale o AzCopy a partir daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o Azure PowerShell e avance para a pasta onde o AzCopy é instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD de "Origem" a "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /Pattern:abc.vhd
    ```

    Seguem-se as descrições dos parâmetros utilizados no comando AzCopy:

   * **/ Origem:  *&lt;origem&gt;:***  localização da pasta ou URL do contentor de armazenamento que contém o VHD.
   * **/ SourceKey:  *&lt;chave da conta de origem&gt;:***  chave de conta do Storage da conta de armazenamento de origem.
   * **/ Dest:  *&lt;destino&gt;:***  URL do contentor de armazenamento para copiar o VHD em.
   * **/ DestKey:  *&lt;chave da conta de dest&gt;:***  chave de conta do Storage da conta de armazenamento de destino.
   * **/ Padrão:  *&lt;nome de ficheiro&gt;:***  especifique o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar o AzCopy ferramenta, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

##### <a name="option-2-copy-a-vhd-with-powershell-synchronized-copy"></a>Opção 2: Copiar um VHD com o PowerShell (sincronizado cópia)
Também pode copiar o ficheiro VHD utilizando o cmdlet do PowerShell AzureStorageBlobCopy de início. Utilize o seguinte comando no Azure PowerShell para copiar o VHD. Substitua os valores na <> com valores correspondentes da sua conta de armazenamento de origem e de destino. Para utilizar este comando, tem de ter um contentor denominado vhds na sua conta de armazenamento de destino. Se o contentor não existir, crie uma antes de executar o comando.

```powershell
$sourceBlobUri = <source-vhd-uri>

$sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>

$destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>

Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer <dest-container> -DestBlob <dest-disk-name> -DestContext $destinationContext
```

Exemplo:

```powershell
C:\PS> $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"

C:\PS> $sourceContext = New-AzureStorageContext  –StorageAccountName "sourceaccount" -StorageAccountKey "J4zUI9T5b8gvHohkiRg"

C:\PS> $destinationContext = New-AzureStorageContext  –StorageAccountName "destaccount" -StorageAccountKey "XZTmqSGKUYFSh7zB5"

C:\PS> Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext
```

### <a name="scenario2"></a>Cenário 2: "Posso estou a migrar VM de outras plataformas para Premium Storage do Azure."
Se estiver a migrar VHD de não - Storage do Azure na nuvem para o Azure, primeiro tem de exportar o VHD para um diretório local. Ter o caminho de origem completo do diretório local onde o VHD é armazenado à mão e, em seguida, utilizar o AzCopy carregá-lo para o Storage do Azure.

#### <a name="step-1-export-vhd-to-a-local-directory"></a>Passo 1. Exportar o VHD para um diretório local
##### <a name="copy-a-vhd-from-aws"></a>Copiar um VHD do AWS
1. Se estiver a utilizar o AWS, exporte a instância de EC2 a um VHD num registo de Amazon S3. Siga os passos descritos na documentação do Amazon para exportar o Amazon EC2 instâncias instalar a ferramenta de interface de linha de comandos (CLI) do Amazon EC2 e execute o comando Criar instância-exportar tarefas para exportar a instância de EC2 para um ficheiro VHD. Certifique-se de utilizar **VHD** para o disco &#95; imagem &#95; Variável de formato ao executar o **criar instância-exportação-tarefas** comando. O ficheiro exportado de VHD é guardado no registo do Amazon S3 que designar durante esse processo.

    ```
    aws ec2 create-instance-export-task --instance-id ID --target-environment TARGET_ENVIRONMENT \
      --export-to-s3-task DiskImageFormat=DISK_IMAGE_FORMAT,ContainerFormat=ova,S3Bucket=BUCKET,S3Prefix=PREFIX
    ```

2. Transfira o ficheiro VHD a partir do registo de S3. Selecione o ficheiro VHD, em seguida, **ações** > **transferir**.

    ![][3]

##### <a name="copy-a-vhd-from-other-non-azure-cloud"></a>Copiar um VHD de outra nuvem não do Azure
Se estiver a migrar VHD de não - Storage do Azure na nuvem para o Azure, primeiro tem de exportar o VHD para um diretório local. Copie o caminho de origem completo do diretório local onde o VHD é armazenado.

##### <a name="copy-a-vhd-from-on-premises"></a>Copiar um VHD no local
Se estiver a migrar o VHD de um ambiente no local, terá do caminho de origem completo onde o VHD é armazenado. O caminho de origem pode ser uma partilha de ficheiro ou localização do servidor.

#### <a name="step-2-create-the-destination-for-your-vhd"></a>Passo 2. Criar o destino para o VHD
Crie uma conta de armazenamento para manter os seus VHDs. Quando planear onde pretende armazenar os seus VHDs, considere os seguintes pontos:

* A conta de armazenamento de destino pode ser armazenamento standard ou premium, consoante os requisitos da aplicação.
* A região da conta de armazenamento tem de ser igual à capacidade Azure VMs do Premium Storage, irá criar na fase final. Pode copiar para uma nova conta de armazenamento ou estiver a planear utilizar a mesma conta de armazenamento com base nas suas necessidades.
* Copiar e guardar a chave de conta de armazenamento da conta de armazenamento de destino para a próxima fase.

Recomendamos vivamente que mover todos os dados de carga de trabalho de produção utilizar o armazenamento premium.

#### <a name="step-3-upload-the-vhd-to-azure-storage"></a>Passo 3. Carregar o VHD para o armazenamento do Azure
Agora que tem o VHD no diretório local, pode utilizar o AzCopy ou AzurePowerShell para carregar o ficheiro. vhd para o Storage do Azure. Ambas as opções são fornecidas aqui:

##### <a name="option-1-using-azure-powershell-add-azurevhd-to-upload-the-vhd-file"></a>Opção 1: Utilizar o Azure PowerShell Add-AzureVhd para carregar o ficheiro. vhd

```powershell
Add-AzureVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo>
```

Um exemplo <Uri> poderá ser ***"https://storagesample.blob.core.windows.net/mycontainer/blob1.vhd"***. Um exemplo <FileInfo> poderá ser ***"C:\path\to\upload.vhd"***.

##### <a name="option-2-using-azcopy-to-upload-the-vhd-file"></a>Opção 2: Utilizar o AzCopy para carregar o ficheiro. vhd
Utilizar o AzCopy, pode carregar facilmente o VHD através da Internet. Dependendo do tamanho dos VHDs, isto pode demorar tempo. Lembre-se verificar os limites de entrada/saída da conta de armazenamento ao utilizar esta opção. Consulte [metas de desempenho e escalabilidade do Storage do Azure](storage-scalability-targets.md) para obter mais detalhes.

1. Transfira e instale o AzCopy a partir daqui: [versão mais recente do AzCopy](http://aka.ms/downloadazcopy)
2. Abra o Azure PowerShell e avance para a pasta onde o AzCopy é instalado.
3. Utilize o seguinte comando para copiar o ficheiro VHD de "Origem" a "Destino".

    ```azcopy
    AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>
    ```

    Exemplo:

    ```azcopy
    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /SourceKey:key1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /DestKey:key2 /BlobType:page /Pattern:abc.vhd
    ```

    Seguem-se as descrições dos parâmetros utilizados no comando AzCopy:

   * **/ Origem:  *&lt;origem&gt;:***  localização da pasta ou URL do contentor de armazenamento que contém o VHD.
   * **/ SourceKey:  *&lt;chave da conta de origem&gt;:***  chave de conta do Storage da conta de armazenamento de origem.
   * **/ Dest:  *&lt;destino&gt;:***  URL do contentor de armazenamento para copiar o VHD em.
   * **/ DestKey:  *&lt;chave da conta de dest&gt;:***  chave de conta do Storage da conta de armazenamento de destino.
   * **/ BlobType: página:** Especifica que o destino for um blob de página.
   * **/ Padrão:  *&lt;nome de ficheiro&gt;:***  especifique o nome de ficheiro do VHD para copiar.

Para obter detalhes sobre como utilizar o AzCopy ferramenta, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).

##### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
Também pode carregar um VHD para a sua conta do storage através de um meio o seguinte:

* [Blob de cópia de armazenamento do Azure API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
* [Blobs de carregamento de Explorador de armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
* [Referência de API de REST de serviço de importação/exportação de armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)

> [!NOTE]
> Recomendamos que utilize o serviço de importação/exportação se estimado tempo de carregamento tem mais de 7 dias. Pode utilizar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo de unidade de tamanho e a transferência de dados.
>
> Importar/exportar podem ser utilizado para copiar para uma conta de armazenamento standard. Terá de copiar a partir de armazenamento standard para a conta do premium storage utilizando uma ferramenta como o AzCopy.
>
>

## <a name="create-azure-virtual-machine-using-premium-storage"></a>Criar as VMs do Azure a utilizar o Premium Storage
Depois do VHD é carregado ou copiado para a conta de armazenamento pretendido, siga as instruções nesta secção para registar o VHD como uma imagem do SO ou o disco do SO dependendo do seu cenário e, em seguida, crie uma instância VM a partir do mesmo. O disco de dados VHD pode ser anexado à VM quando for criado.
Um script de migração de exemplo é fornecido no final desta secção. Este script simple não corresponde a todos os cenários. Terá de atualizar o script para corresponder ao seu cenário específico. Para ver se este script aplica-se ao seu cenário, consulte abaixo [um exemplo de Script de migração](#a-sample-migration-script).

### <a name="checklist"></a>Lista de verificação
1. Aguardar até que todos os discos VHD copiar está concluída.
2. Certifique-se de que armazenamento Premium está disponível na região que estiver a migrar.
3. Decida a série VM nova que irá utilizar. Deve ser um com capacidade de armazenamento Premium, e o tamanho deve ser dependentes na disponibilidade na região e com base nas suas necessidades.
4. Decida o tamanho da VM exato que irá utilizar. Tamanho da VM tem de ser suficientemente grande para suportar o número de discos de dados que tiver. Por exemplo, Se tiver 4 discos de dados, a VM tem de ter núcleos de 2 ou mais. Além disso, considere o processamento de energia, memória e as necessidades de largura de banda de rede.
5. Crie uma conta do Premium Storage na região de destino. Esta é a conta que irá utilizar para a nova VM.
6. Ter à mão, incluindo a lista de discos e blobs VHD correspondentes os detalhes da VM atuais.

Prepare a sua aplicação para o período de indisponibilidade. Para efetuar uma migração limpa, terá de parar todas as o processamento no sistema atual. Apenas, em seguida, pode obtê-lo para o estado consistente que pode migrar para a plataforma de novo. Duração do período de indisponibilidade dependerá da quantidade de dados nos discos para migrar.

> [!NOTE]
> Se estiver a criar uma VM do Azure Resource Manager a partir de um disco de VHD especializadas, consulte [este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para a implementação de VM de Gestor de recursos com o disco existente.
>
>

### <a name="register-your-vhd"></a>Registar o VHD
Para criar uma VM a partir de VHD de SO ou para anexar um disco de dados para uma nova VM, primeiro tem de registá-los. Siga os passos abaixo, dependendo do cenário o VHD.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD de sistema operativo para criar várias instâncias de VM do Azure
Depois de imagem do SO generalizada VHD é carregada para a conta de armazenamento, registe-o como um **imagem de VM do Azure** , de modo a que possa criar uma ou mais instâncias VM a partir do mesmo. Utilize os seguintes cmdlets PowerShell para registar o VHD como imagem do SO de VM do Azure. Forneça o URL de conclusão contentor onde o VHD foi copiado para.

```powershell
Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows
```

Copie e guarde o nome desta nova imagem de VM do Azure. No exemplo acima, é *OSImageName*.

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD de sistema operativo exclusivo para criar uma única instância de VM do Azure
Depois do VHD de SO exclusivo é carregado para a conta de armazenamento, registe-o como um **disco do SO Azure** , para que possa criar uma instância VM a partir do mesmo. Utilize estes cmdlets do PowerShell para registar o VHD como um disco de SO do Azure. Forneça o URL de conclusão contentor onde o VHD foi copiado para.

```powershell
Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"
```

Copie e guarde o nome deste novo disco de SO do Azure. No exemplo acima, é *OSDisk*.

#### <a name="data-disk-vhd-to-be-attached-to-new-azure-vm-instances"></a>VHD ligado ao novas instâncias de VM do Azure de disco de dados
Depois do disco de dados VHD é carregado para a conta de armazenamento, registe-o como um disco de dados do Azure para que podem ser anexado a sua nova série DS, série série DSv2 ou instância de VM de Azure série GS.

Utilize estes cmdlets do PowerShell para registar o VHD como um disco de dados do Azure. Forneça o URL de conclusão contentor onde o VHD foi copiado para.

```powershell
Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"
```

Copie e guarde o nome deste novo disco de dados do Azure. No exemplo acima, é *DataDisk*.

### <a name="create-a-premium-storage-capable-vm"></a>Criar uma VM com capacidade de armazenamento Premium
Uma vez a imagem do SO ou estão registados disco do SO, crie um novo série DS, série DSv2 ou série GS VM. Irá utilizar a imagem do sistema operativo ou o nome de disco de sistema operativo que registado. Selecione o tipo VM da camada de armazenamento Premium. No exemplo abaixo, estamos a utilizar o *Standard_DS2* tamanho da VM.

> [!NOTE]
> Atualize o tamanho de disco para se certificar de que corresponda ao seu capacidade e requisitos de desempenho e os tamanhos de disco do Azure disponíveis.
>
>

Siga os passo os cmdlets do PowerShell abaixo para criar a nova VM. Em primeiro lugar, defina os parâmetros comuns:

```powershell
$serviceName = "yourVM"
$location = "location-name" (e.g., West US)
$vmSize ="Standard_DS2"
$adminUser = "youradmin"
$adminPassword = "yourpassword"
$vmName ="yourVM"
$vmSize = "Standard_DS2"
```

Em primeiro lugar, crie um serviço em nuvem em que irá alojar as suas VMs novas.

```powershell
New-AzureService -ServiceName $serviceName -Location $location
```

Em seguida, dependendo do seu cenário, crie a instância de VM do Azure a partir da imagem do SO ou o disco do SO que registado.

#### <a name="generalized-operating-system-vhd-to-create-multiple-azure-vm-instances"></a>Generalizado VHD de sistema operativo para criar várias instâncias de VM do Azure
Criar a um ou mais nova VM do Azure de série de DS instâncias utilizando o **imagem do SO Azure** que registado. Especificar este nome de imagem do SO na configuração de VM ao criar a nova VM conforme mostrado abaixo.

```powershell
$OSImage = Get-AzureVMImage –ImageName "OSImageName"

$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

New-AzureVM -ServiceName $serviceName -VM $vm
```

#### <a name="unique-operating-system-vhd-to-create-a-single-azure-vm-instance"></a>VHD de sistema operativo exclusivo para criar uma única instância de VM do Azure
Criar um novo DS série VM do Azure instância utilizando o **disco do SO Azure** que registado. Especifique o nome do disco do SO na configuração de VM ao criar a nova VM, conforme mostrado abaixo.

```powershell
$OSDisk = Get-AzureDisk –DiskName "OSDisk"

$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

New-AzureVM -ServiceName $serviceName –VM $vm
```

Especifique outras informações de VM do Azure, tal como um serviço em nuvem, a região, a conta de armazenamento, o conjunto de disponibilidade e a política de colocação em cache. Tenha em atenção que a instância VM tem de ser localizada conjuntamente com o sistema de operativo associado ou discos de dados, pelo que a conta de serviço, região e armazenamento em nuvem selecionado deve ser a mesma localização que os VHDs subjacentes destes discos.

### <a name="attach-data-disk"></a>Anexar disco de dados
Por último, se tiver registado disco de dados VHDs, anexe-as para o novo armazenamento Premium com capacidade de VM do Azure.

Utilize o seguinte cmdlet do PowerShell para ligar o disco de dados para a nova VM e especificar a política de colocação em cache. No exemplo abaixo, a política de colocação em cache está definida como *ReadOnly*.

```powershell
$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

Update-AzureVM  -VM $vm
```

> [!NOTE]
> Poderão existir passos adicionais necessários para suportar a sua aplicação que não é ser abrangida neste guia.
>
>

### <a name="checking-and-plan-backup"></a>A verificar e planear a cópia de segurança
Depois da nova VM se encontra em execução, aceder ao mesmo utilizando o mesmo id de início de sessão e palavra-passe é da VM original e certifique-se de que tudo está a funcionar conforme esperado. Todas as definições, incluindo volumes repartidos, estarão presentes na nova VM.

O último passo consiste em Planear a cópia de segurança e a agenda de manutenção para a nova VM com base nas necessidades da aplicação.

### <a name="a-sample-migration-script"></a>Um exemplo de script de migração
Se tiver várias VMs para migrar, automatização mediante scripts do PowerShell irá ser útil. Segue-se um script de exemplo que automatiza a migração de uma VM. Tenha em atenção que o script abaixo é apenas um exemplo e não existirem alguns pressupostos feitos sobre os discos da VM atuais. Terá de atualizar o script para corresponder ao seu cenário específico.

Os pressupostos são:

* Está a criar VMs clássicas do Azure.
* Os discos de SO de origem e os discos de dados de origem estão na mesma conta de armazenamento e o mesmo contentor. Se os discos de SO e discos de dados não estiverem no mesmo local, pode utilizar o AzCopy ou do Azure PowerShell para copiar VHDs através de contas de armazenamento e de contentores. Consulte o passo anterior: [VHD de cópia com o PowerShell ou o AzCopy](#copy-vhd-with-azcopy-or-powershell). Editar este script para satisfazer o seu cenário é outra opção, mas recomendamos que utilize o AzCopy ou PowerShell, uma vez que é mais fácil e rápida.

O script de automatização é fornecido abaixo. Substitua o texto com as suas informações e atualizar o script para corresponder ao seu cenário específico.

> [!NOTE]
> Utilizar o script existente não preserva a configuração da rede VM de origem. Terá re-config as definições de rede no seu VMs migrados.
>
>

```
    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a VM from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source VM to the new storage account.
    And then it will create a new VM from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement, but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location "Southeast Asia"

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [Bool] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid Azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an Azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you'd like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the VM manually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires deleting the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`t[Warning] You chose to copy data disks only. Moving VM requires removing the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. This is an irreversible action. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
            Start-Sleep -Seconds 10
            $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a VM from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location
```

#### <a name="optimization"></a>Otimização
A configuração atual do VM pode ser personalizada especificamente para funcionar bem com discos padrão. Por exemplo, para aumentar o desempenho ao utilizar discos muitas num volume repartido. Por exemplo, em vez de utilizar separadamente 4 discos no armazenamento Premium, poderá otimizar o custo por meio de um único disco. Otimizações de como este tem de ser processada de forma caso a caso e requer passos personalizados após a migração. Além disso, tenha em atenção que este processo pode não funcionar bem para bases de dados e aplicações que dependem do esquema de disco definido na configuração.

##### <a name="preparation"></a>Preparação
1. Conclua a migração simples, tal como descrito na secção anterior. Otimizações de serão efetuadas em nova VM após a migração.
2. Defina novos tamanhos de disco necessários para a configuração otimizada.
3. Determine o mapeamento dos discos/volumes atuais para as especificações de disco novo.

##### <a name="execution-steps"></a>Passos de execução
1. Crie novos discos com o tamanho adequado na VM do Premium Storage.
2. Início de sessão para a VM e copie os dados do volume atual para o novo disco, que mapeia para esse volume. Fazê-lo para todos os volumes atuais que necessitam de mapear para um novo disco.
3. Em seguida, altere as definições da aplicação para mudar para novos discos, exposição e os volumes antigos.

Para otimizar a aplicação para um melhor desempenho de disco, consulte [otimizar o desempenho da aplicação](../../virtual-machines/windows/premium-storage-performance.md#optimizing-application-performance).

### <a name="application-migrations"></a>Migrações de aplicação
Bases de dados e outras aplicações complexas podem necessitar de passos especiais conforme definido pelo fornecedor de aplicação para a migração. Consulte a documentação da respetiva aplicação. Por exemplo, Normalmente, as bases de dados podem ser migrados através da cópia de segurança e restaurar.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes recursos para cenários específicos para migrar máquinas virtuais:

* [Migrar máquinas virtuais do Azure entre contas de armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Criar e carregar um VHD do Windows Server para o Azure.](../../virtual-machines/windows/classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Criar e carregar um disco rígido Virtual que contém o sistema operativo Linux](../../virtual-machines/linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migração de máquinas virtuais do Amazon AWS ao Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Além disso, consulte os seguintes recursos para saber mais sobre o Storage do Azure e Virtual Machines do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Armazenamento Premium: Armazenamento de Elevado Desempenho para Cargas de Trabalho de Máquinas Virtuais do Azure](../../virtual-machines/windows/premium-storage.md)

[1]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]:./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png
[4]: http://technet.microsoft.com/library/hh831739.aspx
