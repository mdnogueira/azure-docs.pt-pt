---
title: "Ativar depuração remota com entrega contínua | Microsoft Docs"
description: "Saiba como ativar a depuração remota quando utilizar o fornecimento contínuo para implementar no Azure"
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: c2bd67afc0c289de94019497e57b57f97a759f3a
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Ativar a depuração remota ao utilizar a entrega contínua para publicar no Azure
Pode ativar a depuração remota no Azure, para serviços em nuvem ou de máquinas virtuais, quando utiliza [entrega contínua](cloud-services-dotnet-continuous-delivery.md) para publicar no Azure, seguindo estes passos.

## <a name="enabling-remote-debugging-for-cloud-services"></a>Ativar depuração remota para serviços em nuvem
1. O agente de compilação, configurar o ambiente inicial para o Azure conforme descrito na [criar linha de comandos para o Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Porque o tempo de execução de depuração remota (msvsmon.exe) é necessário para o pacote, instale o **remoto Tools para Visual Studio**.

    * [As ferramentas para Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Ferramentas remotas para o Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [As ferramentas para Visual Studio 2013 atualização 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    Como alternativa, pode copiar os binários de depuração remota a partir de um sistema que tenha o Visual Studio instalado.

3. Criar um certificado, conforme descrito na [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md). Mantenha o PFX e o thumbprint do certificado RDP e carregar o certificado para o serviço de nuvem de destino.
4. Utilize as seguintes opções na linha de comandos de MSBuild para compilar e do pacote com a depuração remota ativada. (Substitui caminhos reais para os ficheiros de sistema e o projeto para os itens entre parênteses ângulo).
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath`é o caminho para a pasta que contém msvsmon.exe nas ferramentas do remoto, para o Visual Studio.
    `RemoteDebuggerConnectorVersion`é a versão do SDK do Azure no seu serviço em nuvem. Também deve corresponder à versão instalada com o Visual Studio.
5. Publicar o serviço de nuvem de destino utilizando o ficheiro de pacote e. cscfg gerado no passo anterior.
6. Importe o certificado (. pfx) para o computador que tem o Visual Studio com o Azure SDK para .NET instalado. Certifique-se importar para o `CurrentUser\My` arquivo de certificados, caso contrário, anexar ao depurador no Visual Studio irá falhar.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>Ativar depuração remota para máquinas virtuais
1. Crie uma máquina virtual do Azure. Consulte [criar uma Máquina Virtual com o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [criar e gerir máquinas virtuais do Azure no Visual Studio](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
2. No [página do portal clássica do Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), ver o dashboard da máquina virtual para ver a máquina virtual **THUMBPRINT do certificado RDP**. Este valor é utilizado para o `ServerThumbprint` valor na configuração da extensão.
3. Criar um certificado de cliente conforme descrito na [descrição geral de certificados para serviços de nuvem do Azure](cloud-services-certs-create.md) (mantenha o thumbprint do certificado RDP e. pfx).
4. Instalar o Azure Powershell (versão 0.7.4 ou posterior) conforme descrito na [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
5. Execute o script seguinte para ativar a extensão de RemoteDebug. Substitua os dados pessoais e os caminhos com os seus próprios, tais como o nome da subscrição, o nome do serviço e o thumbprint.
   
   > [!NOTE]
   > Este script está configurado para o Visual Studio 2015. Se estiver a utilizar o Visual Studio 2013 ou Visual Studio 2017, modifique o `$referenceName` e `$extensionName` atribuições abaixo para `RemoteDebugVS2013` ou `RemoteDebugVS2017`.

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Importe o certificado (. pfx) para o computador que tem o Visual Studio com o Azure SDK para .NET instalado.

