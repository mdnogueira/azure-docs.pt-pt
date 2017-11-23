---
title: "Resolução de problemas de encriptação de disco do Azure | Microsoft Docs"
description: "Este artigo fornece sugestões de resolução de problemas para o Microsoft Azure disco encriptação para Windows e as VMs de IaaS Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: devtiw
ms.openlocfilehash: c7734b8e02b6a2f08f5fc6ebe4b2ec43e34b35c3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco do Azure

Este guia destina-se a profissionais de TI, analistas de segurança de informações e administradores de nuvem cujas organizações utilizam o Azure Disk Encryption e necessitam de documentação de orientação para resolver problemas relacionados com encriptação de disco.

## <a name="troubleshooting-linux-os-disk-encryption"></a>Resolução de problemas de encriptação de disco de SO Linux

Encriptação de disco de sistema operativo (SO) do Linux tem desmonte a unidade de SO antes de o executar através do processo de encriptação de disco completo. Se este não é possível desmontar a unidade, uma mensagem de erro de "Falha ao desmontar após..." é provável que ocorrem.

Este erro é mais provável acontecer se a encriptação de disco de SO for tentada num ambiente de VM de destino que foi alterado ou mudou da imagem de galeria as cotações suportados. Exemplos de desvios de imagem suportado que podem interferir com a capacidade da extensão de desmonte a unidade de SO incluem o seguinte:
- Imagens personalizadas já não correspondem a um sistema de ficheiros suportado ou o esquema de criação de partições.
- Grande aplicações como SAP, MongoDB ou Apache Cassandra estão instalados e em execução no sistema operativo antes de encriptação. A extensão não é possível corretamente encerrado estas aplicações. Se as aplicações manter identificadores de ficheiros abertos a unidade do SO, a unidade não pode ser desmontada, causando a falha.
- Personalizado de scripts que são executados na proximidade do tempo de fecho para a encriptação ativada ou se existirem outras alterações estão a ser efetuadas na VM durante o processo de encriptação. Este conflito pode acontecer quando um modelo Azure Resource Manager define várias extensões para executar em simultâneo, ou quando uma extensão de script personalizado ou outra ação será executada em simultâneo para encriptação de disco. Isolar esses passos e não poderão resolver o problema.
- Não foi desativado segurança avançada Linux (SELinux) antes de ativar a encriptação, pelo que o passo de unmount falhar. Pode ser reenabled SELinux após a conclusão da encriptação.
- O disco de SO utiliza um esquema de Gestor de Volume lógica (LVM). Embora o suporte de disco de dados do limitado LVM esteja disponível, um disco de SO LVM não é.
- Requisitos mínimos de memória não são cumpridos (7 GB é sugerida para encriptação de disco do SO).
- Unidades de dados estão em modo recursivo montado no diretório /mnt/ ou entre si (por exemplo, /mnt/data1, /mnt/data2, /data3 + /data3/data4).
- Outros Azure Disk Encryption [pré-requisitos](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) para Linux não são cumpridos.

## <a name="unable-to-encrypt"></a>Não é possível encriptar

Em alguns casos, o encriptação de disco parece estar bloqueada em "Iniciada de encriptação de disco de SO" de Linux e SSH está desativada. O processo de encriptação pode demorar entre 3-16 horas para concluir uma imagem das cotações de galeria. Se forem adicionados discos de dados de várias terabyte tamanho, o processo pode demorar dias.

A sequência de encriptação de disco de SO Linux unmounts temporariamente a unidade de SO. Em seguida, efetua bloco a bloco de encriptação de disco de SO completo, antes que o remounts no seu estado encriptado. Ao contrário do Azure Disk Encryption no Windows, encriptação de disco do Linux não permite a utilização simultânea de VM enquanto a encriptação está em curso. As características de desempenho da VM podem efetuar uma diferença significativa no tempo necessário para a encriptação completa. Estas características incluem o tamanho do disco e se a conta de armazenamento é padrão ou armazenamento premium (SSD).

Para verificar o estado de encriptação, consultar o **ProgressMessage** campo devolvido o [Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) comando. Enquanto está a ser encriptada a unidade de SO, a VM entra num Estado de manutenção e desativa o SSH para evitar qualquer interrupção para o processo contínuo. O **EncryptionInProgress** relatórios para a maioria do tempo de mensagens, enquanto a encriptação está em curso. Várias horas mais tarde, um **VMRestartPending** mensagem pede-lhe para reiniciar a VM. Por exemplo:


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Depois de lhe for pedido para reiniciar a VM e, depois de reinicia a VM, tem de aguardar 2 a 3 minutos para o reinício e para os passos finais para ser executado no destino. As alterações de mensagem de estado quando a encriptação é finally concluir. Depois desta mensagem estiver disponível, o disco de SO encriptado deverá estar pronto a utilizar e a VM está pronta para ser utilizadas novamente.

Nos seguintes casos, recomendamos que restaure a VM para o instantâneo ou uma cópia de segurança criada imediatamente antes de encriptação:
   - Se a sequência de reinício descrito anteriormente não ocorrer.
   - Se as informações de arranque, mensagem de progresso ou outros indicadores de erro se a encriptação de SO de relatório não meio este processo. Um exemplo de uma mensagem é o erro "Falha ao desmontar" descrita neste guia.

Antes da tentativa seguinte, reevaluate as características da VM e certifique-se de que todos os pré-requisitos são cumpridos.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas do Azure Disk Encryption atrás de uma firewall
Quando a conectividade é restrita por uma firewall, requisitos de proxy ou definições de grupo (NSG) de segurança de rede, a capacidade da extensão para efetuar tarefas de necessárias poderá ser interrompida. Este interrupção pode resultar em mensagens de estado, tais como o "Estado da extensão não está disponível na VM." Em cenários esperados, a encriptação não for concluída. As secções que se seguem tem alguns problemas comuns de firewall que poderá investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
As definições de grupo de segurança de rede que são aplicadas ainda permite que o ponto final para cumpram a configuração de rede documentado [pré-requisitos](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites) para encriptação de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Cofre de chaves do Azure atrás de uma firewall
A VM tem de ser capaz de aceder um cofre de chaves. Consulte a documentação de orientação sobre o acesso ao Cofre de chaves por trás de uma firewall que o [Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) mantém de equipa.

### <a name="linux-package-management-behind-a-firewall"></a>Gestão de pacotes de Linux atrás de uma firewall

Em runtime, o Azure Disk Encryption para Linux baseia-se no sistema de gestão do pacote de distribuição de destino para instalar os componentes de pré-requisito necessários antes de ativar a encriptação. Se as definições da firewall impediram que a VM ser capaz de transferir e instalar estes componentes, em seguida, falhas subsequentes esperadas. Os passos para configurar este sistema de pacote de gestão podem variar por distribuição. No Red Hat, quando um proxy for necessário, tem de garantir que o Gestor de subscrição e yum estão configurados corretamente. Para obter mais informações, consulte [como resolver problemas do Gestor de subscrição e yum](https://access.redhat.com/solutions/189533).  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Resolução de problemas de núcleo de servidor do Windows Server 2016

No Windows Server 2016 Server Core, o componente de bdehdcfg não está disponível por predefinição. Este componente necessita de encriptação de disco do Azure. É utilizado para dividir o volume de sistema do volume do SO, o que é efetuado apenas uma vez para o tempo de vida da VM. Estes binários não são necessários durante as operações de encriptação posteriores.

Para resolver este problema, ficheiros de cópia dos seguintes 4 de uma VM de centro de dados do Windows Server 2016 para a mesma localização no Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

   2. Introduza o seguinte comando:

   ```
   bdehdcfg.exe -target default
   ```

   3. Este comando cria uma partição do sistema de 550 MB. Reinicie o sistema.

   4. Utilize o utilitário DiskPart para verificar os volumes e, em seguida, avançar.  

Por exemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu mais informações sobre alguns problemas comuns no Azure Disk Encryption e como resolver esses problemas. Para obter mais informações sobre este serviço e as respetivas capacidades, consulte os artigos seguintes:

- [Aplicar a encriptação de disco no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Encriptar uma máquina virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Encriptação de dados do Azure Inativos](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
