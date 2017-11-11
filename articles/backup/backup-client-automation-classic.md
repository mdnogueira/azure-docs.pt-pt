---
title: "Utilizar o PowerShell para gerir cópias de segurança do Windows Server no Azure | Microsoft Docs"
description: "Implementar e gerir cópias de segurança do Windows Server com o PowerShell."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: e7e269e2-1f11-41a9-957b-a2155de6a1e0
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: saurse;markgal;nkolli;trinadhk
ms.openlocfilehash: 21e84d6dd7d5064908555a2f7c64f39816c2956f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Implementar e gerir cópias de segurança para o Azure para o Windows Server/cliente Windows com o PowerShell
> [!div class="op_single_selector"]
> * [ARM](backup-client-automation.md)
> * [Clássico](backup-client-automation-classic.md)
>
>

Este artigo explica como utilizar o PowerShell para criar cópias de segurança do Windows Server ou Windows, dados de estação de trabalho para um cofre de cópia de segurança. A Microsoft recomenda utilizar cofres dos serviços de recuperação para todas as novas implementações. Se for um novo utilizador de cópia de segurança do Azure e não criou um cofre de cópia de segurança na sua subscrição, utilize o artigo [implementar e gerir dados do Data Protection Manager para o Azure com o PowerShell](backup-client-automation.md) para armazenar os dados num cofre dos serviços de recuperação. 

> [!IMPORTANT]
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> Após 30 de Novembro de 2017, já não poderá utilizar o PowerShell para criar cofres de cópia de segurança. **Até 30 de Novembro de 2017**:
>- Todos os cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

## <a name="install-azure-powershell"></a>Instalar o Azure PowerShell
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

Outubro de 2015, foi lançado o Azure PowerShell 1.0. Esta versão foi concluída com êxito a 0.9.8 de versão e colocados sobre algumas alterações significativas, especialmente no padrão de nomenclatura dos cmdlets. Os cmdlets da versão 1.0 seguem o padrão de nomenclatura {verbo}-AzureRm{nome}; ao passo que os nomes da versão 0.9.8 não incluem **Rm** (por exemplo, New-AzureRmResourceGroup em vez de New-AzureResourceGroup). Ao utilizar o Azure PowerShell 0.9.8, tem de começar por ativar o modo Resource Manager executando o comando **Switch-AzureMode AzureResourceManager**. Este comando não é necessário no 1.0 ou posterior.

Se pretender utilizar os scripts escritos para o 0.9.8 ambiente, no ambiente 1.0 ou posterior, deverá testar cuidadosamente os scripts num ambiente de pré-produção antes de os utilizar na produção para evitar o impacto inesperado.

[Transferir a versão mais recente do PowerShell](https://github.com/Azure/azure-powershell/releases) (versão mínima necessária é: 1.0.0)

[!INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## <a name="create-a-backup-vault"></a>Criar um cofre de cópia de segurança
> [!WARNING]
> Para os clientes utilizando a cópia de segurança do Azure pela primeira vez, tem de registar o fornecedor de cópia de segurança do Azure a ser utilizado com a sua subscrição. Isto pode ser efetuado executando o seguinte comando: Register AzureProvider - ProviderNamespace "Microsoft.Backup"
>
>

Pode criar um novo cofre de cópia de segurança utilizando o **New-AzureRMBackupVault** cmdlet. O Cofre de cópia de segurança é um recurso ARM, por isso terá de colocá-lo dentro de um grupo de recursos. Numa consola do Azure PowerShell elevada, execute os seguintes comandos:

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Utilize o **Get-AzureRMBackupVault** cmdlet para listar os cofres de cópia de segurança numa subscrição.

## <a name="installing-the-azure-backup-agent"></a>Instalar o agente de cópia de segurança do Azure
Antes de instalar o agente do Backup do Azure, tem de ter o instalador transferido e presente no servidor do Windows. Pode obter a versão mais recente do instalador do [Microsoft Download Center](http://aka.ms/azurebackup_agent) ou a partir da página do Dashboard do Cofre de cópia de segurança. Guardar o instalador para uma localização facilmente acessível, como * C:\Downloads\*.

Para instalar o agente, execute o seguinte comando numa consola do PowerShell elevada:

```
PS C:\> MARSAgentInstaller.exe /q
```

Esta ação instala o agente com todas as opções predefinidas. A instalação demora alguns minutos em segundo plano. Se não especificar o */nu* opção, em seguida, o **Windows Update** será aberta a janela no final da instalação para verificar a existência de quaisquer atualizações. Depois de instalado, o agente irá mostrar na lista de programas instalados.

Para ver a lista de programas instalados, aceda a **painel de controlo** > **programas** > **programas e funcionalidades**.

![Agente instalado](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Opções de instalação
Para ver todas as opções disponíveis através da linha de comandos, utilize o seguinte comando:

```
PS C:\> MARSAgentInstaller.exe /?
```

As opções disponíveis incluem:

| Opção | Detalhes | Predefinição |
| --- | --- | --- |
| /q |Instalação silenciosos |- |
| /p: "localização" |Caminho para a pasta de instalação para o agente de cópia de segurança do Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent |
| /s: "localização" |Caminho para a pasta de cache para o agente de cópia de segurança do Azure. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Optar ativamente por participar no Microsoft Update |- |
| /NU |Não procurar atualizações depois de concluída a instalação |- |
| /d |Desinstala o agente do Microsoft Azure Recovery Services |- |
| / pH |Endereço do anfitrião proxy |- |
| /po |Número de porta de anfitrião do proxy |- |
| /Pu |O nome de utilizador do proxy anfitrião |- |
| /PW |Palavra-passe do proxy |- |

## <a name="registering-with-the-azure-backup-service"></a>A registar com o serviço de cópia de segurança do Azure
Para poder registar com o serviço de cópia de segurança do Azure, terá de se certificar de que o [pré-requisitos](backup-configure-vault.md) são cumpridos. Tem de:

* Tiver uma subscrição do Azure válida
* Tiver um cofre de cópia de segurança

Para transferir as credenciais do cofre, execute o **Get-AzureRMBackupVaultCredentials** cmdlet numa consola do PowerShell do Azure e armazená-lo numa localização conveniente como * C:\Downloads\*.

```
PS C:\> $credspath = "C:\"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

Registar a máquina com o Cofre é feito utilizando o [início OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet:

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration -VaultCredentials $cred -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : West US

Machine registration succeeded.
```

> [!IMPORTANT]
> Não utilize caminhos relativos para especificar o ficheiro de credenciais do cofre. Tem de fornecer um caminho absoluto como uma entrada para o cmdlet.
>
>

## <a name="networking-settings"></a>Definições de rede
Quando a conectividade da máquina Windows à internet através de um servidor proxy, as definições de proxy também podem ser fornecidas para o agente. Neste exemplo, não há nenhum servidor de proxy, pelo que iremos explicitamente são limpar quaisquer informações relacionadas com o proxy.

Também é possível controlar a utilização de largura de banda com as opções de ```work hour bandwidth``` e ```non-work hour bandwidth``` para um conjunto especificado de dias da semana.

Definir os detalhes do proxy e de largura de banda é feito utilizando o [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet:

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Definições de encriptação
Os dados de cópia de segurança enviados para a cópia de segurança do Azure são encriptados para proteger a confidencialidade dos dados. A frase de acesso de encriptação é a "palavra-passe" para desencriptar os dados no momento do restauro.

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [!IMPORTANT]
> Manter as informações da frase de acesso seguro e segura quando está definido. Não será possível restaurar dados a partir do Azure sem esta frase de acesso.
>
>

## <a name="back-up-files-and-folders"></a>Faça uma cópia de segurança de ficheiros e pastas
Todas as suas cópias de segurança de clientes e servidores Windows a cópia de segurança do Azure são regidas por uma política. A política é composto por três partes:

1. A **agenda de cópia de segurança** que especifica quando as cópias de segurança tem de ser tomadas e sincronizados com o serviço.
2. A **agenda de retenção** que especifica o período de tempo para manter os pontos de recuperação no Azure.
3. A **especificação de inclusão/exclusão do ficheiro** que determinam o que deve ser feito.

Neste documento, uma vez que estamos a automatizar a cópia de segurança, iremos assumir que nada foi configurado. Vamos começar por criar uma nova política de cópia de segurança utilizando o [New-OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) cmdlet e utilizá-la.

```
PS C:\> $newpolicy = New-OBPolicy
```

Neste momento, a política está vazia e outros cmdlets são necessitam para definir quais itens que serão incluído ou excluído, quando as cópias de segurança será executada e em que as cópias de segurança serão armazenadas.

### <a name="configuring-the-backup-schedule"></a>Configurar a agenda de cópia de segurança
O primeiro das 3 partes de uma política é a agenda de cópia de segurança, que é criada utilizando o [New-OBSchedule](https://technet.microsoft.com/library/hh770401) cmdlet. A agenda de cópia de segurança define quando as cópias de segurança tem de ser executadas. Ao criar uma agenda tem de especificar os parâmetros de entrada 2:

* **Dias da semana** que deve executar a cópia de segurança. Pode executar a tarefa de cópia de segurança em apenas um dia ou todos os dias da semana ou qualquer combinação entre.
* **Horas do dia** quando a cópia de segurança deve ser executada. Pode definir até 3 vezes diferentes do dia quando será acionada a cópia de segurança.

Por exemplo, pode configurar uma política de cópia de segurança que é executada em 4 PM todos os sábados e Domingo.

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

A agenda de cópia de segurança tem de estar associado uma política e isto pode ser conseguido utilizando o [conjunto OBSchedule](https://technet.microsoft.com/library/hh770407) cmdlet.

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Configurar uma política de retenção
A política de retenção define quanto os pontos de recuperação criados das tarefas de cópia de segurança são retidos. Ao criar um novo através de política de retenção de [New-OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) cmdlet, pode especificar o número de dias que os pontos de recuperação de cópia de segurança têm de ser retidos com cópia de segurança do Azure. O exemplo abaixo define uma política de retenção de 7 dias.

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

A política de retenção tem de estar associada com a política de principal utilizando o cmdlet [conjunto OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Incluindo e excluindo ficheiros a cópia de segurança
Um ```OBFileSpec``` objeto define os ficheiros a ser incluído e excluído uma cópia de segurança. Este é um conjunto de regras que definir o âmbito do limite de ficheiros e pastas protegidos num computador. Pode ter como muitos ficheiros regras de inclusão ou exclusão conforme necessário e associá-las com a política. Ao criar um novo objeto de OBFileSpec, pode:

* Especificar os ficheiros e pastas a incluir
* Especificar os ficheiros e pastas a serem excluídos
* Especificar recursiva cópia de segurança de dados de uma pasta (ou) se apenas os ficheiros de nível superior na pasta especificada devem ter uma cópia de segurança.

A última opção é conseguido utilizando o sinalizador - NonRecursive no comando OBFileSpec de novo.

No exemplo abaixo, vamos criar cópias de segurança volume c e d: e excluir os binários do SO na pasta Windows e quaisquer pastas temporárias. Para fazê, vamos criar dois ficheiros especificações utilizando o [New-OBFileSpec](https://technet.microsoft.com/library/hh770408) cmdlet - uma para inclusão e outro para exclusão. Depois das especificações de ficheiro tiverem sido criadas, estão associados a política a utilizar o [adicionar OBFileSpec](https://technet.microsoft.com/library/hh770424) cmdlet.

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Aplicar a política
Agora, o objeto de política está concluído e tem uma agenda de cópia de segurança associada, política de retenção e uma lista de inclusão/exclusão dos ficheiros. Agora pode ser consolidada para cópia de segurança do Azure utilizar esta política. Antes de aplicar a política recém-criado Certifique-se de que existem não existem políticas de cópia de segurança existentes associadas ao servidor utilizando o [remover OBPolicy](https://technet.microsoft.com/library/hh770415) cmdlet. Remover a política irá pedir confirmação. Para ignorar a utilização de confirmação de ```-Confirm:$false``` sinalizador com o cmdlet.

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Consolidar o objeto de política é feito utilizando o [conjunto OBPolicy](https://technet.microsoft.com/library/hh770421) cmdlet. Isto também irá pedir confirmação. Para ignorar a utilização de confirmação de ```-Confirm:$false``` sinalizador com o cmdlet.

```
PS C:> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Pode ver os detalhes de utilização de política de cópia de segurança existente a [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) cmdlet. Pode desagregar ainda mais a utilizar o [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) cmdlet para a agenda de cópia de segurança e a [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) cmdlet para as políticas de retenção

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Efetuar uma cópia de segurança ad-hoc
Assim que tiver sido definida uma política de cópia de segurança as cópias de segurança irão ocorrer pelo agendamento. Acionar uma cópia de segurança ad-hoc é também possível utilizar o [início OBBackup](https://technet.microsoft.com/library/hh770426) cmdlet:

```
PS C:> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes...
Preparing storage...
Estimating size of backup items...
Estimating size of backup items...
Transferring data...
Verifying backup...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Restaurar dados a partir de cópia de segurança do Azure
Esta secção descreve os passos para automatizar a recuperação de dados da cópia de segurança do Azure. Se o fizer, envolve os seguintes passos:

1. Escolha o volume de origem
2. Escolha um ponto de cópia de segurança a restaurar
3. Escolha um item a restaurar
4. Acionar o processo de restauro

### <a name="picking-the-source-volume"></a>Escolha o volume de origem
Para restaurar um item da cópia de segurança do Azure, primeiro tem de identificar a origem do item. Uma vez que iremos estiver a executar os comandos no contexto de um servidor do Windows ou um cliente Windows, a máquina já é identificada. O passo seguinte identificar a origem é identificar o volume que o contém. Uma lista de volumes ou origens a cópia de segurança desta máquina pode ser obtida ao executar o [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) cmdlet. Este comando devolve uma matriz de todas as origens de cópia de segurança deste cliente/servidor.

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-to-restore"></a>Escolher um ponto de cópia de segurança a restaurar
A lista de pontos de cópia de segurança pode ser obtida ao executar o [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet com os parâmetros adequados. No nosso exemplo, vamos escolher o ponto de cópia de segurança mais recente do volume de origem *D:* e utilizá-lo para recuperar um ficheiro específico.

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
O objeto ```$rps``` é uma matriz de pontos de cópia de segurança. O primeiro elemento é o ponto mais recente e o elemento enésimo é o ponto mais antigo. Para escolher o ponto mais recente, utilizaremos ```$rps[0]```.

### <a name="choosing-an-item-to-restore"></a>Escolher um item a restaurar
Para identificar o exato ficheiro ou pasta para restaurar, utilize recursivamente o [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet. Dessa forma, a hierarquia de pastas pode ser pesquisada utilizando apenas a ```Get-OBRecoverableItem```.

Neste exemplo, se quisermos restaurar o ficheiro *finances.xls* pode referenciar que utilizar o objeto ```$filesFolders[1]```.

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Também pode procurar itens a restaurar utilizando o ```Get-OBRecoverableItem``` cmdlet. No nosso exemplo, para procurar *finances.xls* foi obtemos um identificador no ficheiro ao executar o comando:

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Acionar o processo de restauro
Para acionar o processo de restauro, vamos primeiro é necessário especificar as opções de recuperação. Isto pode ser feito utilizando o [New-OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) cmdlet. Neste exemplo, vamos supor que queremos restaurar os ficheiros para *C:\temp*. Vamos também partem do princípio de pretende ignorar ficheiros que já existem na pasta de destino *C:\temp*. Para criar uma opção de recuperação, utilize o seguinte comando:

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Agora acionar o restauro utilizando o [início OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) comando selecionado no ```$item``` da saída do ```Get-OBRecoverableItem``` cmdlet:

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## <a name="uninstalling-the-azure-backup-agent"></a>Desinstalar o agente de cópia de segurança do Azure
Desinstalar o agente de cópia de segurança do Azure pode ser feito usando o seguinte comando:

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

A desinstalar os binários do agente da máquina tem algumas consequências a ter em consideração:

* Remove o filtro de ficheiro da máquina e controlo de alterações está parado.
* Todas as informações de política são removidas da máquina, mas as informações de política continua a ser armazenada no serviço.
* Todas as agendas de cópia de segurança são removidas e são efetuadas sem cópias de segurança adicionais.

No entanto, os dados armazenados no Azure permanecem e são mantidos de acordo com a configuração de política de retenção por si. Pontos de antigos são automaticamente desatualizados.

## <a name="remote-management"></a>Gestão remota
Todas as a gestão à volta o agente do Backup do Azure, políticas e origens de dados pode ser feita remotamente através do PowerShell. O computador que irá ser gerido remotamente precisa de ser preparado corretamente.

Por predefinição, o serviço WinRM está configurado para arranque manual. O tipo de arranque tem de ser definido *automática* e o serviço deve ser iniciado. Para verificar se o serviço WinRM está em execução, o valor da propriedade de estado deve ser *executar*.

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell deve ser configurado para a gestão remota.

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

A máquina pode agora ser gerida remotamente - a partir da instalação do agente. Por exemplo, o script seguinte copia o agente para o computador remoto e instala-o.

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre Consulte Azure cópia de segurança para o servidor/cliente Windows

* [Introdução ao Azure Backup](backup-introduction-to-azure-backup.md)
* [Cópia de segurança de servidores do Windows](backup-configure-vault.md)
