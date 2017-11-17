---
title: "Encriptação de disco do Azure para o Windows e as VMs de Linux IaaS | Microsoft Docs"
description: "Este artigo fornece uma descrição geral do Microsoft Azure disco encriptação para o Windows e as VMs de IaaS Linux."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: kakhan
ms.openlocfilehash: 15ed35ab3a082db3376890992be3a29b6e042a2f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Encriptação de disco do Azure para o Windows e as VMs de Linux IaaS
Microsoft Azure é vivamente consolidada para assegurar a privacidade dos dados, soberania de dados e permite avançadas, para controlar o Azure alojadas dados através de uma variedade de tecnologias para encriptar, controlar e gerir chaves de encriptação, controlo & auditar o acesso aos dados. Isto proporciona aos clientes do Azure a flexibilidade para escolher a solução que melhor se adeque às suas necessidades de negócio. Neste documento, vamos apresenta-lhe uma nova solução de tecnologia "Do Azure Disk Encryption para o Windows e de Linux IaaS VM" para ajudar a proteger e salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacional. O documento fornece orientações detalhadas sobre como utilizar as funcionalidades de encriptação de disco do Azure, incluindo os cenários suportados e o utilizador experiências.

> [!NOTE]
> Algumas recomendações podem aumentar dados, a rede ou a utilização de recursos de computação, resultando em custos de licenciamento ou de subscrição adicionais.

## <a name="overview"></a>Descrição geral
Encriptação de disco do Azure é uma nova capacidade que ajuda-o a encriptar os discos da máquina virtual do Windows e Linux IaaS. O padrão da indústria tira partido do Azure Disk Encryption [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funcionalidade do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer a encriptação de volume para o SO e os discos de dados. A solução está integrada [Cofre de chaves do Azure](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. A solução também garante que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure.

Encriptação de disco do Azure para o Windows e as VMs de IaaS Linux está agora em **disponibilidade geral** em todas as regiões públicas do Azure e regiões de AzureGov para VMs padrão e VMs com o armazenamento premium.

### <a name="encryption-scenarios"></a>Cenários de encriptação
A solução do Azure Disk Encryption suporta os seguintes cenários de cliente:

* Ativar a encriptação em novas VMs de IaaS criados a partir de VHD previamente encriptado e chaves de encriptação
* Ativar a encriptação em novas VMs de IaaS criados a partir de imagens de galeria do Azure suportadas
* Ativar a encriptação em VMs de IaaS existentes em execução no Azure
* Desativar a encriptação em VMs de IaaS Windows
* Desativar a encriptação em unidades de dados para as VMs de IaaS Linux
* Ativar a encriptação de disco gerido VMs
* Atualizar as definições de encriptação de um existente premium encriptado e o armazenamento premium não VM
* Cópia de segurança e restauro de VMs encriptados

A solução suporta os seguintes cenários para VMs de IaaS quando são ativados no Microsoft Azure:

* Integração com o Cofre de chaves do Azure
* O escalão Standard VMs: [A, D, DS, G, GS, F e série Sim forth VMs do IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Ativar a encriptação em Windows e Linux VMs de IaaS e VMs de disco gerido de imagens de galeria do Azure suportadas
* Desativar a encriptação em unidades de dados e SO Windows VMs de IaaS e as VMs de disco gerido
* Desativar a encriptação em unidades de dados para VMs de disco gerido e as VMs de IaaS Linux
* Ativar a encriptação em VMs do IaaS SO de cliente do Windows em execução
* Ativar a encriptação em volumes com caminhos de montagem
* Ativar a encriptação em VMs do Linux configurado com o disco striping (RAID) utilizando mdadm
* Ativar a encriptação em VMs do Linux utilizando LVM para discos de dados
* Ativar a encriptação em Linux LVM 7.3 para o SO e discos de dados 
* Ativar a encriptação em VMs do Windows configurado com espaços de armazenamento
* Atualizar as definições de encriptação de um existente premium encriptado e o armazenamento premium não VM
* Cópia de segurança e restauro de VMs encriptados, para KEK não e cenários KEK (de chaves KEK - chave de encriptação de chave)
* Todos os público do Azure e AzureGov regiões são suportadas

A solução não suporta os seguintes cenários, funcionalidades e tecnologias:

* Escalão básico VMs do IaaS
* A desativação da encriptação numa unidade do SO para as VMs de IaaS Linux
* A desativação da encriptação numa unidade de dados se o disco do SO é encriptado para as VMs de Iaas Linux
* VMs de IaaS que são criados utilizando o método de criação de VM clássico
* Ative a encriptação em Windows e as VMs de Linux IaaS imagens personalizadas do cliente não é suportada.
* Integração com o serviço de gestão de chaves no local
* Ficheiros do Azure (sistema de ficheiros partilhados), o sistema de ficheiros de rede (NFS), volumes dinâmicos e VMs do Windows que estão configurados com sistemas RAID baseados em software

### <a name="encryption-features"></a>Funcionalidades de encriptação
Quando ativa e implementa a Azure Disk Encryption para VMs IaaS do Azure, as seguintes funcionalidades estão ativadas, dependendo da configuração fornecida:

* Encriptação do volume para proteger o volume de arranque Inativos no armazenamento do SO
* Encriptação de volumes de dados para proteger volumes de dados Inativos no armazenamento do
* A desativação da encriptação em unidades de dados e SO para as VMs de IaaS Windows
* A desativação da encriptação nos dados de unidades para as VMs de IaaS Linux (apenas se o SO unidade é não encriptadas)
* Proteger as as chaves de encriptação e segredos na sua subscrição do Cofre de chaves
* Relatórios de estado de encriptação de encriptados VM do IaaS
* Remoção das definições de configuração de encriptação de disco da máquina virtual IaaS
* Cópia de segurança e restauro de VMs encriptadas ao utilizar o serviço de cópia de segurança do Azure

Encriptação de disco do Azure para as VMS de IaaS para Windows e Linux solução inclui:

* A extensão de encriptação de disco para o Windows.
* A extensão de encriptação de disco para Linux.
* Os cmdlets do PowerShell de encriptação de disco.
* A disco encriptação cmdlets de interface de linha de comandos (CLI) do Azure.
* Os modelos Azure Resource Manager de encriptação de disco.

A solução do Azure Disk Encryption é suportada em VMs de IaaS que estão a executar SO Windows ou Linux. Para obter mais informações sobre os sistemas operativos suportados, consulte a secção "Pré-requisitos".

> [!NOTE]
> Não há sem encargos adicionais para encriptar os discos da VM com o Azure Disk Encryption.

### <a name="value-proposition"></a>Proposta de valor
Quando aplica a solução de gestão de encriptação de disco do Azure, pode satisfazer as necessidades de negócio seguintes:

* VMs de IaaS são protegidas inativos, porque é possível utilizar tecnologia de encriptação de norma da indústria para abordar os requisitos de segurança e conformidade organizacionais.
* Arranque de VMs de IaaS em chaves controlado de cliente e as políticas e pode auditar a respetiva utilização no seu Cofre de chaves.

### <a name="encryption-workflow"></a>Fluxo de trabalho de encriptação
Para ativar a encriptação de disco para o Windows e VMs com Linux, efetue o seguinte:

1. Escolha um cenário de encriptação entre os cenários de encriptação anteriores.
2. Optar por ativar a encriptação através do modelo de encriptação de disco do Azure Resource Manager, os cmdlets do PowerShell ou comando da CLI de disco e especificar a configuração de encriptação.

   * Para o cenário VHD encriptados de cliente, carrega o VHD encriptado para a conta do storage e o material de chave de encriptação para o seu Cofre de chaves. Em seguida, forneça a configuração de encriptação para ativar a encriptação numa nova VM do IaaS.
   * Para novas VMs que são criadas no Marketplace e VMs existentes que já estão em execução no Azure, forneça a configuração de encriptação para ativar a encriptação na VM do IaaS.

3. Conceder acesso para a plataforma para ler o material de chave de encriptação (chaves de encriptação BitLocker para sistemas Windows) e o frase de acesso para Linux a partir do seu Cofre de chaves para ativar a encriptação na VM do IaaS do Azure.

4. Fornece a identidade da aplicação do Azure Active Directory (Azure AD) para escrever a chave de encriptação materiais ao seu Cofre de chaves. Se o fizer, ativa a encriptação na VM do IaaS para os cenários mencionados no passo 2.

5. Azure atualiza o modelo de VM de serviço com a encriptação e a configuração do Cofre de chaves e configura o VM encriptado.

 ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Fluxo de trabalho de desencriptação
Para desativar a encriptação de disco para as VMs de IaaS, conclua os seguintes passos de alto nível:

1. Optar por desativar a encriptação (desencriptação) numa VM IaaS em execução no Azure através do modelo de encriptação de disco do Azure Resource Manager ou os cmdlets do PowerShell e especificar a configuração de desencriptação.

 Este passo desativa a encriptação de sistema operativo ou o volume de dados ou ambos na VM do IaaS em execução do Windows. No entanto, tal como mencionado na secção anterior, a desativação da encriptação de disco de SO para Linux não é suportada. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux, desde que o disco de SO não está encriptado.
2. O modelo de VM de serviço de atualizações do Azure e a VM do IaaS está marcado como desencriptada. O conteúdo da VM já não é encriptado em pausa.

> [!NOTE]
> A operação de encriptação de desativar não eliminar o Cofre de chaves e o material de chave de encriptação (chaves de encriptação BitLocker para sistemas Windows) ou o frase de acesso para Linux.
 > Não é suportada a desativação da encriptação de disco de SO do Linux. O passo de desencriptação só é permitido para unidades de dados em VMs do Linux.
A desativação da encriptação de disco de dados de Linux não é suportada se a unidade de SO estiver encriptada.

## <a name="prerequisites"></a>Pré-requisitos
Antes de ativar o Azure Disk Encryption em VMs do IaaS do Azure para os cenários suportados que foram abordados na secção "Descrição geral", consulte os seguintes pré-requisitos:

* Tem de ter uma subscrição do Azure Active Directory válida para criar recursos no Azure em regiões suportadas.
* Encriptação de disco do Azure é suportada nas seguintes versões do Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.
* Encriptação de disco do Azure é suportada nas seguintes versões de cliente do Windows: Windows 8 e o cliente Windows 10.

> [!NOTE]
> Para o Windows Server 2008 R2, tem de ter o .NET Framework 4.5 instalados antes de ativar a encriptação no Azure. Pode instalá-lo do Windows Update ao instalar a atualização opcional o Microsoft .NET Framework 4.5.2 para sistemas baseados em x64 do Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Encriptação de disco do Azure é suportada nos seguintes galeria do Azure com base em Linux server Distribuições e versões:

| Distribuição de Linux | Versão | Tipo de volume suportado para a encriptação|
| --- | --- |--- |
| Ubuntu | 16.04-DIARIAMENTE-LTS | Disco do SO e dados |
| Ubuntu | 14.04.5-DAILY-LTS | Disco do SO e dados |
| Ubuntu | 12.10 | Disco de dados |
| Ubuntu | 12.04 | Disco de dados |
| RHEL | 7.4 | Disco do SO e dados |
| RHEL | 7.3 | Disco do SO e dados |
| RHEL | LVM 7.3 | Disco do SO e dados |
| RHEL | 7.2 | Disco do SO e dados |
| RHEL | 6.8 | Disco do SO e dados |
| RHEL | 6.7 | Disco de dados |
| CentOS | 7.3 | Disco do SO e dados |
| CentOS | 7.2N | Disco do SO e dados |
| CentOS | 6.8 | Disco do SO e dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 13.2 | Disco de dados |
| SLES | 12 SP1 | Disco de dados |
| SLES | 12-SP1 (Premium) | Disco de dados |
| SLES | HPC 12 | Disco de dados |
| SLES | 11-SP4 (Premium) | Disco de dados |
| SLES | 11 SP4 | Disco de dados |

* Encriptação de disco do Azure requer que o Cofre de chaves e as VMs de residir na mesma região do Azure e subscrição.

> [!NOTE]
> Configurar os recursos em regiões separadas causa uma falha na ativação da funcionalidade do Azure Disk Encryption.

* Para definir e configurar o seu Cofre de chaves de encriptação de disco do Azure, consulte a secção **definir configurar e configurar o seu Cofre de chaves do Azure Disk Encryption** no *pré-requisitos* secção deste artigo.
* Para preparar e configurar a aplicação do Azure AD no Azure Active directory para o Azure Disk Encryption, consulte a secção **configurar a aplicação do Azure AD no Azure Active Directory** no *pré-requisitos* secção Este artigo.
* Para definir e configurar a política de acesso do Cofre de chaves para a aplicação do Azure AD, consulte a secção **configurar a política de acesso do Cofre de chaves para a aplicação do Azure AD** no *pré-requisitos* secção deste artigo.
* Para preparar um previamente encriptado VHD do Windows, consulte a secção **preparar um VHD do Windows previamente encriptado** no *apêndice*.
* Para preparar um VHD previamente encriptado do Linux, consulte a secção **preparar um VHD de Linux previamente encriptado** no *apêndice*.
* A plataforma do Azure necessita de acesso a chaves de encriptação ou segredos no seu Cofre de chaves para que fiquem disponíveis para a máquina virtual quando se efetua o arranque e desencripta o volume de SO da máquina virtual. Para conceder permissões para a plataforma do Azure, defina o **EnabledForDiskEncryption** propriedade no Cofre de chaves. Para obter mais informações, consulte **definir configurar e configurar o seu Cofre de chaves do Azure Disk Encryption** no anexo.
* O segredo do Cofre de chaves e os KEK URLs tem de ser com a versão. Azure impõe esta restrição do controlo de versões. Para segredo válido e KEK URLs, consulte os exemplos seguintes:

  * Exemplo de um URL válido secreto: *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de um URL válido da KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption não suporta a especificação de números de porta como parte dos segredos do Cofre de chaves e KEK URLs. Para obter exemplos de URLs do Cofre de chaves suportadas e não suportados, consulte o seguinte:

  * URL do Cofre de chaves inaceitável *https://contosovault.vault.azure.net:443/segredos/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do Cofre de chaves aceitável: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Para ativar o Azure Disk Encryption funcionalidade, as VMs de IaaS tem de cumprir os seguintes requisitos de configuração de ponto final de rede:
  * Para obter um token para ligar ao seu Cofre de chaves, a VM do IaaS tem de ser capaz de ligar a um ponto final do Azure Active Directory, \[login.microsoftonline.com\].
  * Para escrever as chaves de encriptação para o seu Cofre de chaves, a VM do IaaS tem de ser capaz de ligar ao ponto final do Cofre de chaves.
  * A VM do IaaS tem de ser capaz de ligar a um ponto final de armazenamento do Azure que aloja o repositório de extensão do Azure e uma conta de armazenamento do Azure que aloja os ficheiros VHD.

  > [!NOTE]
  > Se a política de segurança limita o acesso a partir de VMs do Azure para a Internet, pode resolver o URI anterior e configurar uma regra para permitir a conectividade de saída para os IPs específica.
  >
  >Para configurar e aceder ao Cofre de chaves do Azure atrás de uma firewall (https://docs.microsoft.com/en-us/azure/key-vault/key-vault-access-behind-firewall)

* Utilize a versão mais recente da versão do SDK do Azure PowerShell para configurar a Azure Disk Encryption. Transfira a versão mais recente do [Azure PowerShell versão](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Encriptação de disco do Azure não é suportada em [SDK do Azure PowerShell versão 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se está a receber um erro relacionado com a utilizar o Azure PowerShell 1.1.0, consulte [Azure disco encriptação erro relacionado com o Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Para executar qualquer comando da CLI do Azure e associá-lo à sua subscrição do Azure, primeiro tem de instalar o CLI do Azure:
  * Para instalar a CLI do Azure e associá-lo à sua subscrição do Azure, consulte [como instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
  * Para utilizar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager, consulte [comandos da CLI do Azure no modo Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* Encriptar um disco gerido, seja obrigatório pré-requisito para tirar um instantâneo do disco gerido ou uma cópia de segurança do disco fora do Azure Disk Encryption antes de ativar a encriptação.  Sem uma cópia de segurança no local, qualquer falha inesperada durante a encriptação poderá fazer o disco e VM inacessível sem uma opção de recuperação.  Atualmente não, AzureRmVMDiskEncryptionExtension do conjunto de cópia de segurança discos geridos e será erro se utilizada relativamente um disco gerido, a menos que foi especificado o parâmetro - skipVmBackup.  Este parâmetro não é seguro para utilizar a menos que já foi efetuada uma cópia de segurança fora do Azure Disk Encryption.   Quando o parâmetro - skipVmBackup for especificado, o cmdlet não fará uma cópia de segurança do disco gerido antes de encriptação.  Por este motivo, é considerada um pré-requisito obrigatório para se certificar de que uma cópia de segurança do disco gerido a VM está em vigor antes de ativar o Azure Disk Encryption no caso de recuperação é necessária mais tarde.  
> [!NOTE]
 > O parâmetro - skipVmBackup nunca deve ser utilizado, a menos que um instantâneo ou uma cópia de segurança já sido efetuada fora do Azure Disk Encryption. 

* A solução do Azure Disk Encryption utiliza o protetor de chave externo do BitLocker para VMs IaaS do Windows. Para o domínio associado ao VMs, não EFETUE emitir quaisquer políticas de grupo que impõem protetores TPM. Para informações sobre a política de grupo para "Permitir que o BitLocker sem um TPM compatível", consulte [referência de política de grupo do BitLocker](https://technet.microsoft.com/library/ee706521).
* Política do BitLocker em máquinas de virtuais associados a um domínio com a política de grupo personalizado tem de incluir a seguinte definição: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` Azure Disk Encryption irá falhar quando as definições de política de grupo personalizada para o Bitlocker são incompatíveis. Nas máquinas que não tinha a política correta definição, aplicar a nova política, forçando a nova política de atualização (gpupdate.exe /force) e, em seguida, reiniciar poderão ser necessárias.  
* Para criar uma aplicação do Azure AD, criar um cofre de chaves, ou configurar um cofre de chaves e ativar a encriptação, consulte o [script do PowerShell de pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Para configurar os pré-requisitos de encriptação de disco com a CLI do Azure, consulte [este script de Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Para utilizar o serviço de cópia de segurança do Azure para criar cópias de segurança e restaurar VMs encriptadas, quando é ativada a encriptação com o Azure Disk Encryption, encriptar as suas VMs utilizando a configuração de chave de encriptação de disco do Azure. O serviço de cópia de segurança suporta VMs que estão encriptadas com a KEK não ou configurações de KEK. Consulte [como fazer cópias de segurança e restaurar encriptados máquinas virtuais com a encriptação da cópia de segurança do Azure](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption).

* Encriptar um volume com SO Linux, tenha em atenção de que está atualmente necessário no final do processo de um reinício VM. Isto pode ser feito através do portal, o powershell ou a CLI.   Para acompanhar o progresso de encriptação, consulte periodicamente a mensagem de estado devolvida pelo Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Assim que a encriptação estiver concluída, a mensagem de estado devolvida por este comando irá indicar isto.  Por exemplo, "ProgressMessage: disco do SO encriptados com êxito, reinicie a VM" neste momento a VM pode ser reiniciada e utilizada.  

* Azure Disk Encryption para Linux necessita de discos de dados para ter um sistema de ficheiros montado no Linux antes de encriptação

* Recursivamente montado dados discos não são suportados pelo Azure Disk Encryption para Linux. Por exemplo, se o sistema de destino tem montado um disco na barra/foo/e, em seguida, outra no /foo/bar/baz, a encriptação de /foo/bar/baz será concluída com êxito, mas a encriptação da barra/foo/irá falhar. 

* Azure Disk Encryption só é suportado nas imagens de galeria do Azure suportada que satisfaçam os pré-requisitos acima mencionados. Imagens personalizadas do cliente não são suportadas devido a esquemas de partição personalizado e comportamentos de processos que possam existir nestas imagens. Além disso, mesmo Galeria baseia imagem de VM que inicialmente cumpridos pré-requisitos, mas que foram modificada após a criação poderão não ser compatíveis.  Para que o motivo, o procedimento sugerido para encriptar uma VM com Linux é iniciar a partir de uma imagem de galeria limpa, encriptar a VM e, em seguida, adicionar personalizadas de software ou dados para a VM conforme necessário.  

* Encriptação de disco do Azure e o volume de dados local - Bek Volume para Windows e mnt/azure_bek_disk para VMs de IaaS Linux armazenar em segurança a chave de encriptação. Não elimine ou edite qualquer conteúdo este disco. Não é desmonte o disco, uma vez que a presença da chave de encriptação é necessária para as operações de encriptação na VM do IaaS. Ficheiro Leia-me incluído no volume contém detalhes adicionais.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Configurar a aplicação do Azure AD no Azure Active Directory
Quando precisar de encriptação esteja ativado numa VM em execução no Azure, o Azure Disk Encryption gera e escreve as chaves de encriptação para o seu Cofre de chaves. Gestão de chaves de encriptação no seu Cofre de chaves requer autenticação do Azure AD.

Para este fim, crie uma aplicação do Azure AD. Pode encontrar passos detalhados para registar uma aplicação na secção "Obter uma identidade para a aplicação" da mensagem de blogue [Cofre de chaves do Azure - passo](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Esta mensagem também contém um número de exemplos útil para definir e configurar o seu Cofre de chaves. Para efeitos de autenticação, pode utilizar a autenticação baseada em segredo do cliente ou autenticação de cliente baseada em certificados do Azure AD.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Autenticação baseada em segredo do cliente para o Azure AD
As secções que se seguem podem ajudar a configurar uma autenticação baseada em segredo de cliente para o Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Criar uma aplicação do Azure AD utilizando o Azure PowerShell
Utilize o seguinte cmdlet do PowerShell para criar uma aplicação do Azure AD:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId é o ID de cliente do Azure AD e $aadClientSecret é o segredo do cliente que deve utilizar posteriormente para ativar a encriptação de disco do Azure. Salvaguarde adequadamente o segredo do cliente do Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Configurar o ID de cliente do Azure AD e o segredo do portal do Azure
Pode também configurar o ID de cliente do Azure AD e o segredo utilizando o Portal do Azure. Para efetuar esta tarefa, efetue o seguinte:

1. Clique em de **do Active Directory** separador.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. Clique em **Adicionar aplicação**e, em seguida, escreva o nome da aplicação.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. Clique no botão de seta e, em seguida, configure as propriedades da aplicação.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Clique na marca de verificação no canto inferior esquerdo para concluir. É apresentada a página de configuração de aplicação e o ID de cliente do Azure AD é apresentado na parte inferior da página.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Guardar o segredo do cliente do Azure AD, clicando no **guardar** botão. Tenha em atenção o segredo do cliente do Azure AD na caixa de texto de chaves. Salvaguarde adequadamente.

 ![Azure Disk Encryption](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


##### <a name="use-an-existing-application"></a>Utilizar uma aplicação existente
Para executar os comandos seguintes, obtenha e utilize o [módulo Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Os seguintes comandos tem de ser executados a partir de uma nova janela do PowerShell. Não utilize o Azure PowerShell ou a janela do Azure Resource Manager para executar os comandos. Recomendamos esta abordagem porque estes cmdlets no módulo MSOnline ou do Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticação baseada em certificado para o Azure AD
> [!NOTE]
> Autenticação baseada em certificado. do Azure AD não é atualmente suportada em VMs do Linux.

As secções que se seguem mostram como configurar uma autenticação baseada em certificado para o Azure AD.

##### <a name="create-an-azure-ad-application"></a>Criar uma aplicação do Azure AD
Para criar uma aplicação do Azure AD, execute os seguintes cmdlets do PowerShell:

> [!NOTE]
> Substitua o seguinte `yourpassword` cadeia com a palavra-passe segura e a salvaguardar a palavra-passe.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Depois de concluir este passo, carregar um ficheiro PFX para o seu Cofre de chaves e ativar a política de acesso necessária para implementar esse certificado para uma VM.

##### <a name="use-an-existing-azure-ad-application"></a>Utilizar uma aplicação do Azure AD existente
Se estiver a configurar a autenticação baseada em certificado para uma aplicação existente, utilize os cmdlets do PowerShell mostrados aqui. Lembre-se de que executá-los a partir de uma nova janela do PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Depois de concluir este passo, carregar um ficheiro PFX para o seu Cofre de chaves e ativar a política de acesso necessários para implementar o certificado a uma VM.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Carregar um ficheiro PFX para o seu Cofre de chaves
Para obter uma explicação detalhada deste processo, consulte [o oficial do Azure chave de cofre blogue da equipa](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). No entanto, os seguintes cmdlets do PowerShell são tudo o que precisa para a tarefa. Lembre-se de que executá-los a partir da consola do Azure PowerShell.

> [!NOTE]
> Substitua o seguinte `yourpassword` cadeia com a palavra-passe segura e a salvaguardar a palavra-passe.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Implemente um certificado no seu Cofre de chaves para uma VM existente
Depois de concluir o carregamento PFX, implemente um certificado no Cofre de chaves para uma VM existente com o seguinte:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Configurar a política de acesso do Cofre de chaves para a aplicação do Azure AD
A aplicação do Azure AD tem direitos de acesso a chaves ou segredos no cofre. Utilize o [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) cmdlet para conceder permissões para a aplicação, utilizando o ID de cliente (o que foi gerado quando a aplicação foi registada) como o _– ServicePrincipalName_ valor do parâmetro. Para obter mais informações, consulte a mensagem de blogue [Cofre de chaves do Azure - passo](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Eis um exemplo de como efetuar esta tarefa através do PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Azure Disk Encryption exige que configure as seguintes políticas de acesso à aplicação de cliente do Azure AD: _WrapKey_ e _definir_ permissões.

## <a name="terminology"></a>Terminologia
Para compreender alguns dos termos comuns utilizados por esta tecnologia, utilize a seguinte tabela de terminologia:

| Terminologia | Definição |
| --- | --- |
| Azure AD | Azure AD [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Uma conta do Azure AD é um pré-requisito para autenticar, armazenar e obter segredos a partir de um cofre de chaves. |
| Azure Key Vault | O Cofre de chaves é um serviço de gestão de serviços de criptografia, chave baseada em módulos de segurança de hardware validados Federal Information Processing Standards FIPS, que ajudam a salvaguardar as chaves criptográficas e segredos confidenciais. Para obter mais informações, consulte [Cofre de chaves](https://azure.microsoft.com/services/key-vault/) documentação. |
| ARM | Azure Resource Manager |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma setor-reconhecido volume encriptação tecnologia Windows que é utilizada para ativar a encriptação de disco em VMs de IaaS do Windows. |
| BEK | São utilizadas chaves de encriptação do BitLocker para encriptar o volume de arranque do SO e volumes de dados. As chaves do BitLocker são salvaguardadas no Cofre de chaves como segredos. |
| CLI | Consulte [interface de linha de comandos do Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) é o subsistema de encriptação de disco baseado em Linux, transparente, que é utilizado para ativar a encriptação de disco em VMs do IaaS Linux. |
| KEK | Chave de encriptação de chaves é a chave assimétrica (RSA 2048) que pode utilizar para proteger ou moldar o segredo. Pode fornecer uma segurança de hardware (HSM) de módulos-chave ou de chave protegida por software protegidos. Para obter mais detalhes, consulte [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) documentação. |
| PS cmdlets | Consulte [cmdlets Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Definir e configurar o seu Cofre de chaves para o Azure Disk Encryption
Encriptação de disco do Azure ajuda a salvaguardar a encriptação de disco chaves e segredos no seu Cofre de chaves. Para configurar o seu Cofre de chaves para o Azure Disk Encryption, conclua os passos em cada uma das seguintes secções.

#### <a name="create-a-key-vault"></a>Criar um cofre de chaves
Para criar um cofre de chaves, utilize uma das seguintes opções:

* [Modelo de Gestor de recursos "101-chave-cofre-criar"](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Cmdlets do Cofre de chaves do Azure PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Como [proteger o seu Cofre de chaves](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Se já tiver configurado um cofre de chaves para a sua subscrição, avance para a secção seguinte.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de encriptação de chaves (opcional)
Se pretender utilizar um KEK para uma camada adicional de segurança para as chaves de encriptação do BitLocker, adicione um KEK ao seu Cofre de chaves. Utilize o [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet para criar uma chave de encriptação de chaves no Cofre de chaves. Também pode importar um KEK da sua gestão de chaves HSM no local. Para obter mais detalhes, consulte [documentação do Cofre de chave](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Pode adicionar a KEK acedendo para o Azure Resource Manager ou utilizando a interface do Cofre de chaves.

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Definir permissões do Cofre de chaves
A plataforma do Azure necessita de acesso a chaves de encriptação ou segredos no seu Cofre de chaves para que fiquem disponíveis para a VM para arrancar e desencriptação de volumes. Para conceder permissões para a plataforma do Azure, defina o **EnabledForDiskEncryption** propriedade na chave do cofre utilizando o cmdlet PowerShell do Cofre de chaves:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Também pode definir o **EnabledForDiskEncryption** propriedade, visitando o [Explorador de recursos do Azure](https://resources.azure.com).

Conforme mencionado anteriormente, tem de definir o **EnabledForDiskEncryption** propriedade no seu Cofre de chaves. Caso contrário, a implementação irá falhar.

Pode configurar políticas de acesso para a sua aplicação do Azure AD da interface do Cofre de chaves, conforme mostrado aqui:

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

No **políticas de acesso avançadas** separador, certifique-se de que o seu Cofre de chaves está ativada para a Azure Disk Encryption:

![Cofre de chaves do Azure](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Cenários de implementação de encriptação de disco e as experiências de utilizador
Pode ativar vários cenários de encriptação de disco e os passos poderão variar de acordo com o cenário. As secções seguintes abrangem os cenários mais detalhadamente.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Ativar a encriptação em novas VMs de IaaS criados a partir do Marketplace
Pode ativar a encriptação de disco em nova VM do IaaS Windows no Marketplace no Azure utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**, introduza a configuração de encriptação **parâmetros** painel e, em seguida, clique em **OK**.

2. Selecione a subscrição, o grupo de recursos, localização do grupo de recursos, termos legais e contrato e, em seguida, clique em **criar** para ativar a encriptação numa nova VM do IaaS.

> [!NOTE]
> Este modelo cria uma nova VM do Windows encriptados que utiliza a imagem de galeria do Windows Server 2012.

Pode ativar a encriptação de disco num novo IaaS VM de RedHat Linux 7.2 com uma matriz de RAID 0 200 GB ao utilizar este [modelo do Resource Manager](https://aka.ms/fde-rhel). Depois de implementar o modelo, verificar o estado de encriptação da VM utilizando o `Get-AzureRmVmDiskEncryptionStatus` cmdlet, conforme descrito em [SO de encriptação de unidade numa VM com Linux em execução](#encrypting-os-drive-on-a-running-linux-vm). Quando o computador devolve um Estado de _VMRestartPending_, reiniciar a VM.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para novas VMs o cenário de Marketplace utilizando o ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| adminUserName | Nome de utilizador de administrador para a máquina virtual. |
| adminPassword | Administração utilizador palavra-passe para a máquina virtual. |
| newStorageAccountName | Nome da conta do storage para armazenar dados VHDs e SO. |
| vmSize | Tamanho da VM. Atualmente, é suportada apenas padrão A, D e G série. |
| virtualNetworkName | Nome da VNet que devem pertencer a NIC de VM para. |
| subnetName | Nome da sub-rede na VNet que devem pertencer a NIC de VM para. |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| AADClientSecret | Segredo do cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| keyVaultURL | URL do Cofre de chaves que a chave do BitLocker deve ser carregada. Pode obtê-lo utilizando o cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker (opcional). |
| keyVaultResourceGroup | Grupo de recursos do Cofre de chaves. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Pode colocar o seus próprios KEK para salvaguardar adicional a chave de encriptação de dados (segredo frase de acesso) no seu Cofre de chaves.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Ativar a encriptação em novas VMs de IaaS criados a partir de VHD encriptados de cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando o modelo do Resource Manager, os cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam mais detalhadamente o modelo do Resource Manager e os comandos da CLI.

Siga as instruções de um nestas secções para preparar imagens previamente encriptadas, que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptados.

* [Preparar um VHD do Windows previamente encriptado](#preparing-a-pre-encrypted-windows-vhd)
* [Preparar um VHD de Linux previamente encriptado](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Com o modelo do Resource Manager
Pode ativar a encriptação de disco no seu VHD encriptado utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**, introduza a configuração de encriptação **parâmetros** painel e, em seguida, clique em **OK**.

2. Selecione a subscrição, o grupo de recursos, localização do grupo de recursos, termos legais e contrato e, em seguida, clique em **criar** para ativar a encriptação da nova VM do IaaS.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para o VHD encriptado:

| Parâmetro | Descrição |
| --- | --- |
| newStorageAccountName | Nome da conta do storage para armazenar o VHD de SO encriptados. Esta conta de armazenamento deve já ter sido criada no mesmo grupo de recursos e a mesma localização que a VM. |
| osVhdUri | URI do VHD SO da conta do storage. |
| osType | Tipo de produto do SO (Windows/Linux). |
| virtualNetworkName | Nome da VNet que devem pertencer a NIC de VM para. O nome deve já ter sido criado no mesmo grupo de recursos e a mesma localização que a VM. |
| subnetName | Nome da sub-rede da vnet a NIC de VM deverão pertencer ao. |
| vmSize | Tamanho da VM. Atualmente, é suportada apenas padrão A, D e G série. |
| keyVaultResourceID | O ResourceID do que identifica o recurso do Cofre de chaves no Gestor de recursos do Azure. Pode obtê-lo utilizando o cmdlet do PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | URL da chave de encriptação de disco que é definida no Cofre de chaves. |
| keyVaultKekUrl | URL da chave de chave de encriptação para encriptar a chave de encriptação de disco gerado. |
| vmName | Nome da VM do IaaS. |

#### <a name="using-powershell-cmdlets"></a>Utilizando os cmdlets do PowerShell
Pode ativar a encriptação de disco no seu VHD encriptado utilizando o cmdlet do PowerShell [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Utilizar comandos da CLI
Para ativar a encriptação de disco para este cenário através da utilização de comandos da CLI, efetue o seguinte:

1. Definir políticas de acesso no seu Cofre de chaves:

   * Definir o **EnabledForDiskEncryption** sinalizador:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Definir as permissões para a aplicação do Azure AD para escrever segredos para o seu Cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para ativar a encriptação numa VM existente ou em execução, escreva:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obter o estado de encriptação:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para ativar a encriptação numa nova VM do seu VHD encriptado, utilize os seguintes parâmetros com o `azure vm create` comando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Ativar a encriptação em existente ou VM do IaaS Windows a executar no Azure
Neste cenário, pode ativar a encriptação utilizando o modelo do Resource Manager, os cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam como ativá-la utilizando o modelo do Resource Manager e os comandos da CLI mais detalhadamente.

#### <a name="using-the-resource-manager-template"></a>Com o modelo do Resource Manager
Pode ativar a encriptação de disco existente ou executar VMs de IaaS do Windows Azure utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**, introduza a configuração de encriptação **parâmetros** painel e, em seguida, clique em **OK**.

2. Selecione a subscrição, o grupo de recursos, localização do grupo de recursos, termos legais e contrato e, em seguida, clique em **criar** para ativar a encriptação na VM do IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo de Gestor de recursos existente ou executar as VMs que utilizam um ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| AADClientSecret | Segredo do cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser carregada. Pode obtê-lo utilizando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker. Este parâmetro é opcional se selecionar **nokek** na lista pendente UseExistingKek. Se selecionar **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| volumeType | Tipo de volume que é efetuar a operação de encriptação no. Os valores válidos são _SO_, _dados_, e _todos os_. |
| sequenceVersion | Versão de sequência da operação BitLocker. Aumentar este número de versão sempre que uma operação de encriptação de disco é executada na mesma VM. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Pode colocar o seus próprios KEK para salvaguardar adicional a chave de encriptação de dados (segredo de encriptação do BitLocker) no Cofre de chaves.

#### <a name="using-powershell-cmdlets"></a>Utilizando os cmdlets do PowerShell
Para informações sobre como ativar a encriptação com o Azure Disk Encryption utilizando cmdlets do PowerShell, consulte as mensagens de blogue [explorar o Azure Disk Encryption com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [explorar o Azure Disk Encryption com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Utilizar comandos da CLI
Para ativar a encriptação em existente ou VM do IaaS Windows a executar no Azure através de comandos da CLI, efetue o seguinte:

1. Para definir políticas de acesso no Cofre de chaves:
   * Definir o **EnabledForDiskEncryption** sinalizador:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Definir as permissões para a aplicação do Azure AD para escrever segredos para o seu Cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Para ativar a encriptação numa VM existente ou em execução:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Para obter o estado de encriptação:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Para ativar a encriptação numa nova VM do seu VHD encriptado, utilize os seguintes parâmetros com o `azure vm create` comando:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Ativar a encriptação de uma VM do Linux IaaS existente ou em execução no Azure
Pode ativar a encriptação de disco num VM do Linux IaaS existente ou em execução no Azure utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Clique em **implementar no Azure** no modelo de início rápido do Azure, introduza a configuração de encriptação **parâmetros** painel e, em seguida, clique em **OK**.

2. Selecione a subscrição, o grupo de recursos, localização do grupo de recursos, termos legais e contrato e, em seguida, clique em **criar** para ativar a encriptação na VM do IaaS existente ou em execução.

A tabela seguinte lista os parâmetros do modelo do Resource Manager para existente ou executar as VMs que utilizam um ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| AADClientSecret | Segredo do cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| keyVaultName | Nome do Cofre de chaves que a chave do BitLocker deve ser carregada. Pode obtê-lo utilizando o cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada do BitLocker. Este parâmetro é opcional se selecionar **nokek** na lista pendente UseExistingKek. Se selecionar **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| volumeType | Tipo de volume que é efetuar a operação de encriptação no. Os valores suportados válidos são _SO_ ou _todos os_ (consulte suportado Linux distros e das respetivas versões de SO e discos de dados na secção de prerequisiteis anteriormente). |
| sequenceVersion | Versão de sequência da operação BitLocker. Aumentar este número de versão sempre que uma operação de encriptação de disco é executada na mesma VM. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |
| frase de acesso | Escreva uma frase de acesso forte como a chave de encriptação de dados. |

> [!NOTE]
> _KeyEncryptionKeyURL_ é um parâmetro opcional. Pode colocar o seus próprios KEK para salvaguardar adicional a chave de encriptação de dados (segredo frase de acesso) no seu Cofre de chaves.

#### <a name="cli-commands"></a>Comandos da CLI
Pode ativar a encriptação de disco no seu VHD encriptado através da instalação e a utilizar o [comando da CLI](../cli-install-nodejs.md). Para ativar a encriptação em existente ou VMs do IaaS Linux em execução no Azure através da utilização de comandos da CLI, efetue o seguinte:

1. Definir políticas de acesso no Cofre de chaves:

 * Definir o **EnabledForDiskEncryption** sinalizador:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Definir as permissões para a aplicação do Azure AD para escrever segredos para o seu Cofre de chaves:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Para ativar a encriptação numa VM existente ou em execução:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Obter o estado de encriptação:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Para ativar a encriptação numa nova VM do seu VHD encriptado, utilize os seguintes parâmetros com o `azure vm create` comando:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Obter o estado de encriptação de uma VM do IaaS encriptados
Pode obter o estado de encriptação utilizando o Gestor de recursos do Azure, [cmdlets do PowerShell](/powershell/azure/overview), ou comandos da CLI. As secções seguintes explicam como utilizar o Portal do Azure e os comandos da CLI para obter o estado de encriptação.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Obter o estado de encriptação de uma VM do Windows encriptada utilizando o Gestor de recursos do Azure
Pode obter o estado de encriptação da VM do IaaS do Azure Resource Manager efetuando o seguinte procedimento:

1. Iniciar sessão para o [Portal do Azure](https://portal.azure.com/)e, em seguida, clique em **máquinas virtuais** no painel da esquerda para ver uma vista de resumo das máquinas virtuais na sua subscrição. Pode filtrar a vista de máquinas virtuais ao selecionar o nome da subscrição no **subscrição** na lista pendente.

2. Na parte superior do **máquinas virtuais** página, clique em **colunas**.

3. No **escolha coluna** painel, selecione **encriptação de disco**e, em seguida, clique em **atualização**. Deverá ver a coluna de encriptação de disco que mostra o estado de encriptação _ativado_ ou _não ativada_ para cada VM, conforme mostrado na figura seguinte:

 ![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Obter o estado de encriptação de uma VM do IaaS (Windows/Linux) encriptada utilizando o cmdlet do PowerShell de encriptação de disco
Pode obter o estado de encriptação da VM do IaaS a partir do cmdlet do PowerShell de encriptação de disco `Get-AzureRmVMDiskEncryptionStatus`. Para obter as definições de encriptação para a VM, introduza o seguinte:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Pode inspecionar a saída do _Get-AzureRmVMDiskEncryptionStatus_ URLs da chave de encriptação.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Os valores de definições OSVolumeEncrypted e DataVolumesEncrypted estão definidos como _encriptado_, que mostra que ambos os volumes são encriptados utilizando a Azure Disk Encryption. Para informações sobre como ativar a encriptação com o Azure Disk Encryption utilizando cmdlets do PowerShell, consulte as mensagens de blogue [explorar o Azure Disk Encryption com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [explorar o Azure Disk Encryption com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Em VMs do Linux, demora três a quatro minutos o `Get-AzureRmVMDiskEncryptionStatus` cmdlet para comunicar o estado de encriptação.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Obter o estado de encriptação da VM do IaaS do comando da CLI de encriptação de disco
Pode obter o estado de encriptação da VM do IaaS ao utilizar o comando da CLI de encriptação de disco `azure vm show-disk-encryption-status`. Para obter as definições de encriptação para a VM, introduza a sua sessão do CLI do Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Desativar a encriptação sobre a execução de VM do IaaS Windows
Pode desativar a encriptação num Windows em execução ou VM do IaaS Linux através do modelo de encriptação de disco do Azure Resource Manager ou os cmdlets do PowerShell e especificar a configuração de desencriptação.

##### <a name="windows-vm"></a>VM do Windows
O passo de encriptação de desativar desativa a encriptação do sistema operativo, o volume de dados ou ambos na VM do IaaS em execução do Windows. Não é possível desativar o volume do SO e deixe o volume de dados encriptado. Quando o passo de desativar a encriptação é efetuado, o modelo de VM de serviço de atualizações do modelo de implementação clássica do Azure e a VM do IaaS Windows está marcado como desencriptada. O conteúdo da VM já não é encriptado em pausa. A desencriptação não eliminar o Cofre de chaves e o material de chave de encriptação (chaves de encriptação BitLocker para Windows e o frase de acesso para Linux).

##### <a name="linux-vm"></a>VM com Linux
O passo de encriptação de desativar desativa a encriptação de volume de dados na VM de IaaS Linux em execução. Este passo só funciona se o disco de SO não estiver encriptado.

> [!NOTE]
> A desativação da encriptação de disco de SO não é permitida em VMs do Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Desativar a encriptação de uma VM do IaaS existente ou em execução
Pode desativar a encriptação de disco em execução VMs de IaaS do Windows utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**, introduza a configuração de desencriptação **parâmetros** painel e, em seguida, clique em **OK**.

2. Selecione a subscrição, o grupo de recursos, localização do grupo de recursos, termos legais e contrato e, em seguida, clique em **criar** para ativar a encriptação numa nova VM do IaaS.

Para VMs com Linux, pode desativar a encriptação utilizando o [desativar a encriptação de uma VM com Linux em execução](https://aka.ms/decrypt-linuxvm) modelo.

A tabela seguinte lista os parâmetros do modelo do Resource Manager para desativar a encriptação de uma VM do IaaS em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa.
| volumeType | Tipo de volume que é efetuar uma operação de desencriptação no. Os valores válidos são _SO_, _dados_, e _todos os_. Não é possível desativar a encriptação em com o volume de VM do IaaS Windows SO/arranque sem a desativação da encriptação no _dados_ volume. Note também que a desativação da encriptação de disco de SO não é permitida em VMs do Linux. |
| sequenceVersion | Versão de sequência da operação BitLocker. Aumentar este número de versão sempre que uma operação de desencriptação de disco é executada na mesma VM. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Desativar a encriptação de uma VM do IaaS existente ou em execução
Para desativar a encriptação numa VM do IaaS em execução ou existente utilizando o cmdlet do PowerShell, consulte o artigo [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Este cmdlet suporta o Windows e VMs com Linux. Para desativar a encriptação, instala uma extensão na máquina virtual. Se o _nome_ parâmetro não for especificado, uma extensão com o nome predefinido _AzureDiskEncryption para VMs do Windows_ é criado.

Em VMs do Linux, a extensão de AzureDiskEncryptionForLinux é utilizada.

> [!NOTE]
> Este cmdlet reinicia a máquina virtual.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Ativar a encriptação em VM do IaaS previamente encriptado com o disco gerida do Azure
Utilizar o modelo ARM do Azure gerida disco para criar uma VM encriptada a partir de um VHD previamente encriptado utilizando o modelo ARM, localizado em   
[Criar um novo disco gerido encriptado a partir de um blob VHD/armazenamento previamente encriptado] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Ativar a encriptação numa nova VM do IaaS Linux com o disco gerida do Azure
Utilize o modelo ARM do Azure gerida disco para criar uma nova VM do IaaS Linux encriptada utilizando o modelo ARM, localizado em   
[Implementação do RHEL 7.2 com a encriptação de disco completo] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Ativar a encriptação numa nova VM do IaaS do Windows com o disco gerida do Azure
 Utilize o modelo ARM do Azure gerida disco para criar uma nova VM do IaaS Linux encriptada utilizando o modelo ARM, localizado em   
 [Criar um novo encriptados IaaS geridos disco VM do Windows da imagem de galeria] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >É obrigatório para instantâneo e/ou a cópia de segurança um geridos com base em disco instância de VM fora do e antes de ativar o Azure Disk Encryption.  Pode ser obtido um instantâneo do disco gerido no portal ou pode ser utilizada a cópia de segurança do Azure.  As cópias de segurança garantir que uma opção de recuperação no caso de qualquer falha inesperada durante a encriptação.  Assim que for efetuada uma cópia de segurança, o cmdlet Set-AzureRmVMDiskEncryptionExtension pode ser utilizado para encriptar discos geridos, especificando o parâmetro - skipVmBackup.  Este comando irá falhar contra uma VM baseada em disco de gerido até que foi efetuada uma cópia de segurança e se este parâmetro foi especificado.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Atualizar as definições de encriptação de uma VM do premium não encriptada existente
  Utilize as interfaces de encriptação suportada existentes do disco do Azure para executar a VM [cmdlets de PS, CLI ou ARM modelos] para atualizar as definições de encriptação como AAD cliente ID/segredo, chave de encriptação da chave [KEK], chave de encriptação do BitLocker para a VM do Windows ou o frase de acesso para a VM com Linux etc. A definição de encriptação de atualização é suportada para VMs do premium não storage e premium.

## <a name="appendix"></a>Apêndice
### <a name="connect-to-your-subscription"></a>Estabelecer a ligação à subscrição
Antes de continuar, reveja o *pré-requisitos* secção deste artigo. Depois de assegurar que todos os pré-requisitos tiverem sido cumpridos, ligar à sua subscrição da seguinte forma:

1. Iniciar uma sessão do Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:

    `Login-AzureRmAccount`

2. Se tiver várias subscrições e pretender especificar um para utilizar, escreva o seguinte para ver as subscrições da sua conta:

    `Get-AzureRmSubscription`

3. Para especificar a subscrição que pretende utilizar, introduza:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Para verificar se a subscrição configurada está correta, escreva:

    `Get-AzureRmSubscription`

5. Para confirmar que os cmdlets do Azure Disk Encryption são instalados, escreva:

    `Get-command *diskencryption*`

6. O seguinte resultado confirma a instalação do PowerShell de encriptação de disco do Azure:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparar um VHD do Windows previamente encriptado
As secções que se seguem são necessárias para preparar um VHD do Windows previamente encriptado para a implementação de um VHD encriptado no IaaS do Azure. Utilize as informações para preparar e efetuar o arranque de uma nova VM (VHD do Windows) no Azure Site Recovery ou do Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir que o TPM não para a proteção de SO
Configurar a definição de política de grupo do BitLocker **encriptação de unidade BitLocker**, que encontrará em **política de computador Local** > **configuração do computador**  >  **Modelos administrativos** > **componentes do Windows**. Alterar esta definição para **unidades do sistema operativo** > **requer autenticação adicional durante o arranque** > **permitir BitLocker sem um TPM compatível**, conforme mostrado na figura seguinte:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidades do BitLocker
Para o Windows Server 2012 ou posterior, utilize o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, utilize o seguinte comando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume de SO para o BitLocker utilizando`bdehdcfg`
Para comprimir a partição de SO e preparar a máquina para o BitLocker, execute o seguinte comando:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume de SO ao utilizar o BitLocker
Utilize o [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) comando para ativar a encriptação em volume de arranque com um protetor de chave externo. Também colocar a chave externa (ficheiro .bek) na unidade externa ou volume. A encriptação está ativada no volume de arranque do sistema após o reinício seguinte.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um separado/recurso de dados VHD para obter a chave externa utilizando o BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Encriptar uma unidade de SO numa VM com Linux em execução
Encriptação de uma unidade de SO numa VM com Linux em execução é suportada nas distribuições seguintes:

* RHEL 7.2
* CentOS 7.2
* Ubuntu 16.04

##### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para encriptação de disco do SO

* A VM tem de ser criada a imagem do Marketplace no Gestor de recursos do Azure.
* VM do Azure com, pelo menos, 4 GB de RAM (recomendado é de tamanho 7 GB).
* (Para RHEL e CentOS) Desative SELinux. Para desativar SELinux, consulte "4.4.2 criação. Desativar o SELinux"o [Guia do administrador e utilizador SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na VM.
* Depois de desativar SELinux, pelo menos uma vez de reiniciar a VM.

##### <a name="steps"></a>Passos
1. Crie uma VM ao utilizar uma das distribuições especificadas anteriormente.

 A CentOS 7.2, a encriptação de disco do SO é suportada através de uma imagem especial. Para utilizar esta imagem, especifique "7.2n" como o SKU quando criar a VM:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configure a VM consoante as suas necessidades. Se pretender encriptar todos os a (SO + dados), unidades, as unidades de dados tem de ser especificado e mountable de /etc/fstab.

 > [!NOTE]
 > Utilize o UUID =... para especificar unidades de dados no etc/fstab em vez de especificar o nome de dispositivo de blocos (por exemplo, dev/sdb1). Durante a encriptação, a ordem das unidades de alterações na VM. Se a VM depende de uma ordem específica de impedir que os dispositivos, ocorrerá uma falha de monte-os depois da encriptação.

3. Terminar as sessões SSH.

4. Para encriptar o SO, especifique volumeType como **todos os** ou **SO** quando que [ativar a encriptação](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Todos os processos de espaço do utilizador que não estão em execução como `systemd` serviços devem ser desativados com um `SIGKILL`. Reinicie a VM. Quando ativar a encriptação de disco de SO numa VM em execução, planeie no período de indisponibilidade VM.

5. Periodicamente, monitorizar o progresso de encriptação utilizando as instruções de [secção seguinte](#monitoring-os-encryption-progress).

6. Depois de Get-AzureRmVmDiskEncryptionStatus mostra "VMRestartPending", reinicie a VM ou ao iniciar sessão, ao mesmo utilizando o portal, o PowerShell ou a CLI.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Antes de reiniciar, recomendamos que guarde [diagnóstico de arranque](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) da VM.

#### <a name="monitoring-os-encryption-progress"></a>Monitorizar o progresso de encriptação do SO
Pode monitorizar o progresso de encriptação de SO de três formas diferentes:

* Utilize o `Get-AzureRmVmDiskEncryptionStatus` cmdlet e inspecionar o campo de ProgressMessage:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Depois da VM atinge "Foi iniciada de encriptação de disco de SO", que demora cerca de 40 a 50 minutos num armazenamento Premium efetuada VM.

 Porque [emitir #388](https://github.com/Azure/WALinuxAgent/issues/388) no WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` apresentados como `Unknown` em algumas distribuições. Com WALinuxAgent versão 2.1.5 e posterior, este problema é resolvido automaticamente. Se vir `Unknown` no resultado, pode verificar o estado de encriptação de disco, utilizando o Explorador de recursos do Azure.

 Aceda a [Explorador de recursos do Azure](https://resources.azure.com/)e, em seguida, expanda esta hierarquia no painel de seleção no lado esquerdo:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 No InstanceView, desloque para baixo para ver o estado de encriptação das suas unidades.

 ![Vista de instância VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Observe [diagnóstico de arranque](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). As mensagens a partir da extensão ADE devem ter o prefixo `[AzureDiskEncryption]`.

* Inicie sessão para a VM através de SSH e obter o registo de extensão de:

    /var/log/Azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Recomendamos que não iniciar sessão para a VM com encriptação de SO em curso. Copie os registos apenas quando os outros dois métodos falharem.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparar um VHD de Linux previamente encriptado
##### <a name="ubuntu-16"></a>Ubuntu 16
Configure a encriptação durante a instalação de distribuição da seguinte forma:

1. Selecione **configurar volumes encriptados** quando a partição dos discos.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de arranque separado, que não pode ser encriptada. Encriptar a unidade de raiz.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase de acesso. Este é o frase de acesso que carregar para o Cofre de chaves.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua a criação de partições.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando a VM de arranque e são pedidas a uma frase de acesso, utilize a frase de acesso fornecida no passo 3.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Preparar a VM para carregar no Azure com [estas instruções](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute o último passo (desaprovisionamento VM) ainda.

Configure a encriptação funcione com o Azure da seguinte forma:

1. Crie um ficheiro em /usr/local/sbin/azure_crypt_key.sh, com o conteúdo no seguinte script. Preste atenção a KeyFileName, porque é o nome de ficheiro frase de acesso utilizado pelo Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Alterar a configuração de crypt no *etc/crypttab*. Deve ter o seguinte aspeto:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se estiver a editar *azure_crypt_key.sh* no Windows e copiou-lo para Linux, execute `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adicione permissões executável para o script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Editar */etc/initramfs-tools/modules* acrescentando linhas:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Executar `update-initramfs -u -k all` para atualizar o initramfs para tornar o `keyscript` entrem em vigor.

7. Agora pode desaprovisionar a VM.

 ![Ubuntu 16.04 programa de configuração](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue para o passo seguinte e [carregar o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Para configurar a encriptação durante a instalação de distribuição, efetue o seguinte:
1. Quando a partição dos discos, selecione **encriptar o grupo de Volume**e, em seguida, introduza uma palavra-passe. Esta é a palavra-passe, irá carregar para o seu Cofre de chaves.

 ![o programa de configuração openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. A VM com a palavra-passe de arranque.

 ![o programa de configuração openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Preparar a VM do carregamento para o Azure ao seguir as instruções no [preparar uma máquina virtual SLES ou openSUSE para o Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute o último passo (desaprovisionamento VM) ainda.

Para configurar a encriptação funcione com o Azure, efetue o seguinte:
1. Editar o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente estas linhas no final de /usr/lib/dracut/modules.d/90crypt/module-setup.sh o ficheiro:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Acrescente a linha seguinte no início do /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ficheiro:
 ```
    DRACUT_SYSTEMD=0
 ```
E altere todas as ocorrências de:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
para:
```
    if [ 1 ]; then
```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e o acrescentem a "device de abrir LUKS #":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Executar `/usr/sbin/dracut -f -v` para atualizar o initrd.

6. Agora pode retirar o aprovisionamento da VM e [carregar o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

##### <a name="centos-7"></a>CentOS 7
Para configurar a encriptação durante a instalação de distribuição, efetue o seguinte:
1. Selecione **encriptar os meus dados** quando de partição de discos.

 ![Configuração do centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se **encriptar** está selecionado para a partição de raiz.

 ![Configuração do centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase de acesso. Este é o frase de acesso que irá carregar para o seu Cofre de chaves.

 ![Configuração do centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando a VM de arranque e são pedidas a uma frase de acesso, utilize a frase de acesso fornecida no passo 3.

 ![Configuração do centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Preparar a VM para carregar no Azure utilizando as instruções de "CentOS 7.0 +" em [preparar uma máquina de virtual com base em CentOS para o Azure](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute o último passo (desaprovisionamento VM) ainda.

6. Agora pode retirar o aprovisionamento da VM e [carregar o VHD](#upload-encrypted-vhd-to-an-azure-storage-account) no Azure.

Para configurar a encriptação funcione com o Azure, efetue o seguinte:

1. Editar o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente estas linhas no final de /usr/lib/dracut/modules.d/90crypt/module-setup.sh o ficheiro:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Acrescente a linha seguinte no início do /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ficheiro:
```
    DRACUT_SYSTEMD=0
```
E altere todas as ocorrências de:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
para
```
    if [ 1 ]; then
```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e de acréscimo isto depois de dispositivo"LUKS aberta de #":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Executar o "/ sbin/usr/dracut - f - v" para atualizar o initrd.

![Configuração do centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregar o VHD encriptado para uma conta de armazenamento do Azure
Depois da encriptação BitLocker ou a encriptação de DM-Crypt estiver ativada, o VHD encriptado local tem de ser também carregadas para a sua conta do storage.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Carregar o segredo de encriptação de disco para a VM previamente encriptada para o seu Cofre de chaves
O segredo de encriptação de disco que obteve anteriormente deve ser também carregado como um segredo no Cofre de chaves. O Cofre de chaves tem de ter a encriptação de disco e permissões ativadas para o cliente do Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de encriptação de disco não encriptado com uma KEK
Para configurar o segredo no Cofre de chaves, utilize [conjunto AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Se tiver uma máquina virtual do Windows, o ficheiro de bek é codificado como uma cadeia base64 e, em seguida, é carregado para o seu Cofre de chaves utilizando o `Set-AzureKeyVaultSecret` cmdlet. Para Linux, o frase de acesso é codificado como uma cadeia base64 e, em seguida, é carregado para o Cofre de chaves. Além disso, certifique-se de que as etiquetas seguintes estão definidas quando cria o segredo no Cofre de chaves.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Utilize o `$secretUrl` no passo seguinte para [anexar o disco de SO sem utilizar KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com uma KEK
Antes de carregar o segredo ao Cofre de chaves, opcionalmente, pode encriptá-la utilizando uma chave de encriptação de chaves. Utilize a moldagem [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) primeiro encriptar o segredo utilizando a chave de encriptação de chaves. O resultado desta operação de moldagem é uma cadeia de URL codificado em base64, que, em seguida, pode carregar como um segredo utilizando o [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) cmdlet.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Utilize `$KeyEncryptionKey` e `$secretUrl` no passo seguinte para [anexar o disco de SO utilizando KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco de SO
#### <a name="without-using-a-kek"></a>Sem utilizar um KEK
Enquanto estiver a ligá o disco do SO, tem de passar `$secretUrl`. O URL foi gerado na secção "segredo de encriptação de disco não encriptada com uma KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Utilizar um KEK
Ao anexar o disco do SO, passe `$KeyEncryptionKey` e `$secretUrl`. O URL foi gerado na secção "segredo de encriptação de disco não encriptada com uma KEK".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Transferir este guia
Pode transferir este guia do [TechNet Gallery](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).

## <a name="for-more-information"></a>Para obter mais informações
[Explorar a encriptação de disco do Azure com o Azure PowerShell - parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Explorar a encriptação de disco do Azure com o Azure PowerShell - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
