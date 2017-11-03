---
title: "Ativar o ambiente de trabalho remoto num serviço em nuvem do Azure | Microsoft Docs"
description: "Como configurar a aplicação do serviço em nuvem do azure para permitir ligações de ambiente de trabalho remotas"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: d3110ee8-6526-4585-aba5-d0bc9a713e9b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 413e72e9a39fcde84f56bfc61a6bc72dbadf1c97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços em nuvem do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Portal Clássico do Azure](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Pode ativar uma ligação de ambiente de trabalho remoto na sua função durante o desenvolvimento, incluindo os módulos de ambiente de trabalho remoto na sua definição de serviço ou pode optar por ativar o ambiente de trabalho remoto através da extensão de ambiente de trabalho remoto. A abordagem preferida consiste em utilizar a extensão de ambiente de trabalho remoto, pode ativar o ambiente de trabalho remoto, mesmo depois da aplicação for implementada sem ter de voltar a implementar a aplicação.

## <a name="configure-remote-desktop-from-the-azure-classic-portal"></a>Configurar o ambiente de trabalho remoto a partir do portal clássico do Azure
Portal clássico do Azure utiliza a abordagem de extensão de ambiente de trabalho remoto, pelo que pode ativar o ambiente de trabalho remoto, mesmo depois da aplicação é implementada. O **configurar** página do serviço em nuvem permite-lhe ativar o ambiente de trabalho remoto, alterar a conta de administrador local utilizada para ligar às máquinas virtuais, o certificado utilizado na autenticação e definir a data de expiração.

1. Clique em **serviços em nuvem**, clique no nome do serviço de nuvem e, em seguida, clique em **configurar**.
2. Clique em de **remoto** na parte inferior.

    ![Serviços cloud remoto](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)

   > [!WARNING]
   > Todas as instâncias de função serão reiniciadas quando ativar o ambiente de trabalho remoto e clique em OK (marca de verificação) pela primeira vez. Para impedir que um reinício, o certificado utilizado para encriptar a palavra-passe tem de estar instalado na função. Para impedir que um reinício, [carregar um certificado para o serviço em nuvem](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) e, em seguida, regresse a esta caixa de diálogo.

3. No **funções**, selecione a função que pretende atualizar ou selecione **todos os** para todas as funções.
4. Efetue qualquer uma das seguintes alterações:

   * Para ativar o ambiente de trabalho remoto, selecione o **ativar o ambiente de trabalho remoto** caixa de verificação. Para desativar o ambiente de trabalho remoto, desmarque a caixa de verificação.
   * Crie uma conta a utilizar nas ligações de ambiente de trabalho remoto para as instâncias da função.
   * Atualize a palavra-passe para a conta existente.
   * Selecione um certificado carregado a utilizar para autenticação (carregar o certificado utilizando **carregar** no **certificados** página) ou criar um novo certificado.
   * Altere a data de expiração para a configuração do ambiente de trabalho remoto.

5. Quando concluir as atualizações de configuração, clique em **OK** (marca de verificação).

## <a name="remote-into-role-instances"></a>Remoto em instâncias de função
Depois de ambiente de trabalho remoto está ativado nas funções de pode remoto numa instância de função através de várias ferramentas.

Para ligar a uma instância de função a partir do portal clássico do Azure:

1. Clique em **instâncias** para abrir o **instâncias** página.
2. Selecione uma instância de função que tenha o ambiente de trabalho remoto configuradas.
3. Clique em **Connect**e siga as instruções para abrir o ambiente de trabalho.
4. Clique em **abra** e, em seguida, **Connect** para iniciar a ligação de ambiente de trabalho remoto.

### <a name="use-visual-studio-to-remote-into-a-role-instance"></a>Utilizar o Visual Studio para remoto numa instância de função
No Visual Studio, Explorador de servidores:

1. Expanda o **Azure** > **serviços em nuvem** > **[nome do serviço de nuvem]** nós.
2. Expanda a **transição** ou **produção**.
3. Expanda a função individuais.
4. Clique com botão direito uma das instâncias de função, clique em **estabeleçam ligação através do ambiente de trabalho remoto...** e, em seguida, introduza o nome de utilizador e palavra-passe.

![Ambiente de trabalho remoto do Explorador de servidor](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)

### <a name="use-powershell-to-get-the-rdp-file"></a>Utilize o PowerShell para obter o ficheiro RDP
Pode utilizar o [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet para obter o ficheiro RDP. Em seguida, pode utilizar o ficheiro RDP com ligação ao ambiente de trabalho remoto para aceder ao serviço de nuvem.

### <a name="programmatically-download-the-rdp-file-through-the-service-management-rest-api"></a>Através de programação transferir o ficheiro RDP através da API de REST de gestão de serviço
Pode utilizar o [transferir o ficheiro de RDP](https://msdn.microsoft.com/library/jj157183.aspx) operação REST para transferir o ficheiro RDP.

## <a name="to-configure-remote-desktop-in-the-service-definition-file"></a>Para configurar o ambiente de trabalho remoto no ficheiro de definição de serviço
Este método permite-lhe ativar o ambiente de trabalho remoto para a aplicação durante o desenvolvimento. Esta abordagem requer palavras-passe encriptadas armazenados na configuração do serviço de ficheiros e as atualizações para a configuração do ambiente de trabalho remota precisaria que um reimplementação da aplicação. Se quiser evitar estes downsides deve utilizar a abordagem de extensão de ambiente de trabalho remoto com base descrita acima.  

Pode utilizar o Visual Studio para [ativar uma ligação de ambiente de trabalho remoto](../vs-azure-tools-remote-desktop-roles.md) utilizar a abordagem de ficheiro de definição de serviço.  
Os passos abaixo descrevem as alterações necessárias para os ficheiros de modelo de serviço para ativar o ambiente de trabalho remoto. Visual Studio irá automaticamente faz com que estas alterações durante a publicação.

### <a name="set-up-the-connection-in-the-service-model"></a>Configurar a ligação no modelo de serviço
Utilize o **importações** elemento para importar o **RemoteAccess** módulo e a **RemoteForwarder** módulo para a [servicedefinition. Csdef](cloud-services-model-and-package.md#csdef) ficheiro.

O ficheiro de definição de serviço deve ser semelhante ao seguinte exemplo com o `<Imports>` elemento adicionado.

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
O [serviceconfiguration. Cscfg](cloud-services-model-and-package.md#cscfg) ficheiro deve ser semelhante ao seguinte exemplo, tenha em atenção o `<ConfigurationSettings>` e `<Certificates>` elementos. O certificado especificado tem de ser [carregado para o serviço em nuvem](cloud-services-how-to-create-deploy.md#how-to-upload-a-certificate-for-a-cloud-service).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## <a name="additional-resources"></a>Recursos Adicionais
[Como configurar os serviços em nuvem](cloud-services-how-to-configure.md)
[Cloud services FAQ – ambiente de trabalho remoto](cloud-services-faq.md)
