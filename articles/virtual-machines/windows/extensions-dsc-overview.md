---
title: "Configuração para descrição geral do Azure do estado pretendido | Microsoft Docs"
description: "Descrição geral para utilizar o processador de extensão do Microsoft Azure para a configuração de estado pretendido do PowerShell. Incluindo a pré-requisitos, arquitetura, os cmdlets..."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
ms.openlocfilehash: c05c2d541a5f526f362f9cd72fe6d878374112b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução para o processador de extensão de configuração de estado pretendido do Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O agente da VM do Azure e as extensões associadas fazem parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são os componentes de software que expandem as funcionalidades VM e simplificam a várias operações de gestão de VM. Por exemplo, pode ser utilizada a extensão VMAccess para repor a palavra-passe de um administrador ou a extensão de Script personalizado pode ser utilizada para executar um script na VM.

Este artigo apresenta a extensão de PowerShell pretendido Estado Configuration (DSC) para as VMs do Azure como parte do Azure PowerShell SDK. Pode utilizar os novos cmdlets para carregar e aplicar uma configuração de DSC do PowerShell numa VM do Azure ativada com a extensão de DSC do PowerShell. As PowerShell DSC extensão chamadas para o PowerShell DSC para enact a configuração de DSC recebida na VM. Esta funcionalidade está também disponível através do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos
**Máquina local** para interagir com a extensão de VM do Azure, terá de utilizar o portal do Azure ou o Azure PowerShell SDK. 

**O agente convidado** a VM do Azure que está configurado na configuração de DSC tem de ser um sistema operativo que suporta o Windows Management Framework (WMF) 4.0 ou 5.0. A lista completa de versões de SO suportadas pode ser encontrada no [histórico de versões de extensão de DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Conceitos e termos de licenciamento
Este guia presumes familiaridade com os seguintes conceitos:

Configuração - um documento de configuração de DSC. 

Nó - um destino para uma configuração de DSC. Neste documento, "nó" sempre refere-se para uma VM do Azure.

Dados de configuração - um. psd1 ficheiros que contêm dados ambientais para uma configuração

## <a name="architectural-overview"></a>Descrição geral da arquitetura
A extensão de DSC do Azure utiliza o framework de agente da VM do Azure para fornecer, enact e relatórios sobre configurações de DSC em execução em VMs do Azure. A extensão de DSC espera um ficheiro. zip que contém, pelo menos, um documento de configuração e um conjunto de parâmetros fornecidos através do SDK do Azure PowerShell ou através do portal do Azure.

Quando a extensão é chamada pela primeira vez, é executado um processo de instalação. Este processo é instalado uma versão do Windows Management Framework (WMF) utilizando a seguinte lógica:

1. Se o SO de VM do Azure é o Windows Server 2016, não é necessária nenhuma ação. Windows Server 2016 já tem a versão mais recente do PowerShell instalada.
2. Se o `wmfVersion` é especificada a propriedade e que versão do WMF está instalada, a menos que é incompatível com SO a VM.
3. Se não `wmfVersion` propriedade for especificada, a versão mais recente aplicável do WMF está instalada.

Instalação do WMF requer um reinício. Após o reinício, a extensão transfere o ficheiro. zip especificado no `modulesUrl` propriedade. Se esta localização no armazenamento de Blobs do Azure, um token SAS pode ser especificado o `sasToken` propriedade para aceder ao ficheiro. Depois da. zip que é transferido e descompactado, a função de configuração definida no `configurationFunction` é executado para gerar o ficheiro MOF. A extensão, em seguida, executa `Start-DscConfiguration -Force` no ficheiro MOF gerado. A extensão de captura resultados e escreve-lo de contingência para o canal de estado do Azure. A partir deste ponto no, o MMC de DSC processa a monitorização e a correção como habitualmente. 

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Cmdlets do PowerShell pode ser utilizados com o Azure Resource Manager ou o modelo de implementação clássica para o pacote, publicar e monitorizar as implementações de extensão de DSC. Os seguintes cmdlets listados são os módulos de implementação clássica, mas "Azure" pode ser substituído por "AzureRm" para utilizar o modelo Azure Resource Manager. Por exemplo, `Publish-AzureVMDscConfiguration` utiliza o modelo de implementação clássica, onde `Publish-AzureRmVMDscConfiguration` utiliza o Azure Resource Manager. 

`Publish-AzureVMDscConfiguration`aceita um ficheiro de configuração, analisa-lo para recursos de DSC dependentes e cria um ficheiro. zip que contém a configuração e os recursos de DSC necessários para enact a configuração. Também pode criar o pacote localmente, utilizando o `-ConfigurationArchivePath` parâmetro. Caso contrário, publica o ficheiro. zip no blob storage do Azure e protege-lo com um token SAS.

O ficheiro. zip criado por este cmdlet tem o script de configuração. ps1, na raiz da pasta de arquivo. Recursos tem a pasta de módulo colocada na pasta de arquivo. 

`Set-AzureVMDscExtension`injects as definições necessárias para a extensão de DSC do PowerShell para um objeto de configuração de VM. No modelo de implementação clássica, as alterações VM tem de ser aplicadas a uma VM do Azure com `Update-AzureVM`. 

`Get-AzureVMDscExtension`obtém o estado da extensão DSC de uma VM específica. 

`Get-AzureVMDscExtensionStatus`obtém o estado da configuração DSC enacted pelo processador de extensão de DSC. Esta ação pode ser efetuada num única VM, ou grupo de VMs.

`Remove-AzureVMDscExtension`Remove o processador de extensão de uma máquina virtual especificada. Este cmdlet **não** remover a configuração, desinstale o WMF ou alterar as definições aplicadas na máquina virtual. Remove apenas o processador de extensão. 

**Principais diferenças nos cmdlets ASM e o Azure Resource Manager**

* Cmdlets do Gestor de recursos do Azure são síncronos. Cmdlets ASM são assíncronos.
* ResourceGroupName, VMName, ArchiveStorageAccountName, versão e a localização estão todos os parâmetros necessários no Gestor de recursos do Azure.
* ArchiveResourceGroupName é um novo parâmetro opcional para o Azure Resource Manager. Pode especificar este parâmetro, quando a sua conta de armazenamento pertence a um grupo de recursos diferente daquela onde a máquina virtual é criada.
* ConfigurationArchive denomina ArchiveBlobName no Gestor de recursos do Azure
* ContainerName denomina ArchiveContainerName no Gestor de recursos do Azure
* StorageEndpointSuffix denomina ArchiveStorageEndpointSuffix no Gestor de recursos do Azure
* O comutador AutoUpdate foi adicionado para o Azure Resource Manager para ativar a atualização automática do processador de extensão para a versão mais recente como e quando estiver disponível. Nota que este parâmetro tem o potencial de causar reinicia a VM quando for lançada uma nova versão do WMF. 

## <a name="azure-portal-functionality"></a>Funcionalidade de portal do Azure
Navegue para uma VM. Em Definições -> geral clique em "As extensões." É criado um novo painel. Clique em "Adicionar" e selecione o PowerShell DSC.

O portal tem de entrada.
**Módulos de configuração ou Script**: Este campo é obrigatório. Necessita de um ficheiro. ps1, que contém um script de configuração ou um ficheiro. zip com um script de configuração. ps1 no raiz e todos os recursos dependentes, nas pastas de módulo dentro de. zip. Podem ser criado com o `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet incluído no SDK do PowerShell do Azure. O ficheiro. zip é carregado para o armazenamento de BLOBs de utilizador protegido por um token SAS. 

**Ficheiro de configuração de dados PSD1**: Este campo é opcional. Se a configuração necessita de um ficheiro de dados de configuração. psd1, utilize este campo para selecioná-lo e carregá-la para o armazenamento de BLOBs de utilizador, onde é protegida por um token SAS. 

**Nome qualificado do módulo de configuração**:. ps1 ficheiros podem ter várias funções de configuração. Introduza o nome do script. ps1 configuração seguido por um '\' e o nome da função de configuração. Por exemplo, se o script. ps1 tem o nome "configuration.ps1" e a configuração é "IisInstall", deve introduzir:`configuration.ps1\IisInstall`

**Configuração argumentos**: se a função de configuração necessita de argumentos, introduzi-las a sessão aqui no formato `argumentName1=value1,argumentName2=value2`. Tenha em atenção de que este formato é um formato diferente que como argumentos de configuração são aceites através de cmdlets do PowerShell ou modelos do Resource Manager. 

## <a name="getting-started"></a>Introdução
A extensão de DSC do Azure aceita documentos de configuração de DSC e enacts-los em VMs do Azure. Segue-se um exemplo simple de uma configuração. Guarde-o localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Os seguintes passos colocar o script de IisInstall.ps1 na VM especificada, executar a configuração e devolver relatórios sobre o estado.
###<a name="classic-model"></a>Modelo clássico
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Modelo do Azure Resource Manager

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Registo
Os registos são colocados em:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[número de versão]

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o PowerShell DSC, [visitar o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine o [modelo Azure Resource Manager para a extensão de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para encontrar funcionalidades adicionais que pode ser geridas com o PowerShell DSC, [procurar na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) mais recursos de DSC.

Para obter detalhes sobre a transmitir parâmetros confidenciais para as configurações, consulte [gerir credenciais de forma segura com o processador de extensão de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

