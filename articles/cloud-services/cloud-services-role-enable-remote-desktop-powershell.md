---
title: "Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços de nuvem do Azure com o PowerShell"
description: "Como configurar a aplicação do serviço em nuvem do azure através do PowerShell para permitir ligações de ambiente de trabalho remotas"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: ab99eaa10d232e244b17325188e83128c651caf6
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Ativar a ligação de ambiente de trabalho remoto para uma função nos serviços de nuvem do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)

Ambiente de trabalho remoto permite-lhe aceder ao ambiente de trabalho de uma função em execução no Azure. Pode utilizar uma ligação de ambiente de trabalho remoto para resolver problemas e diagnosticar problemas com a sua aplicação enquanto estiver em execução.

Este artigo descreve como ativar o ambiente de trabalho remoto nas funções do serviço de nuvem com o PowerShell. Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para os pré-requisitos necessários para este artigo. PowerShell utiliza a extensão de ambiente de trabalho remoto, pelo que pode ativar o ambiente de trabalho remoto após a aplicação é implementada.

## <a name="configure-remote-desktop-from-powershell"></a>Configurar o ambiente de trabalho remoto a partir do PowerShell
O [conjunto AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet permite-lhe ativar o ambiente de trabalho remoto em funções ou especificados todas as funções da sua implementação do serviço de nuvem. O cmdlet permite-lhe especificar o nome de utilizador e palavra-passe para o utilizador de ambiente de trabalho remoto através de *credencial* parâmetro aceita um objeto PSCredential.

Se estiver a utilizar interativamente PowerShell, pode facilmente definir o objeto PSCredential ao chamar o [Get-credenciais](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

```
$remoteusercredentials = Get-Credential
```

Este comando apresenta uma caixa de diálogo, permitindo-lhe introduzir o nome de utilizador e palavra-passe do utilizador remoto de forma segura.

Uma vez que o PowerShell ajuda-o em cenários de automatização, pode também configurar a **PSCredential** objeto de forma a que não requer interação do utilizador. Em primeiro lugar, terá de configurar uma palavra-passe segura. Começar com a especificação de uma palavra-passe de texto simples convertê-la para uma cadeia segura utilizando [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Em seguida terá de converter esta cadeia segura para uma cadeia padrão encriptada utilizando [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Agora pode guardar esta cadeia padrão encriptada para um ficheiro utilizando [conjunto conteúdo](https://technet.microsoft.com/library/ee176959.aspx).

Também pode criar um ficheiro de palavra-passe segura para que não tem de escrever a palavra-passe sempre. Além disso, um ficheiro de palavra-passe segura é melhor do que um ficheiro de texto simples. Utilize o PowerShell seguinte para criar um ficheiro de palavra-passe segura:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Ao definir a palavra-passe, certifique-se de que cumpre o [requisitos de complexidade](https://technet.microsoft.com/library/cc786468.aspx).
>
>

Para criar o objeto de credencial a partir do ficheiro de palavra-passe segura, deve ler o conteúdo do ficheiro e convertê-las novamente utilizando uma cadeia segura [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

O [conjunto AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet também aceita um *expiração* parâmetro, que especifica um **DateTime** em que a conta de utilizador expira. Por exemplo, pode configurar a conta para expirar alguns dias a partir da data e hora atuais.

Este exemplo de PowerShell mostra como definir a extensão de ambiente de trabalho remoto num serviço em nuvem:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Pode também pode especificar opcionalmente o bloco de implementação e as funções que pretende ativar o ambiente de trabalho remoto no. Estes parâmetros são se não for especificados, o cmdlet permite que o ambiente de trabalho remoto em todas as funções no **produção** ranhura de implementação.

A extensão de ambiente de trabalho remoto está associada uma implementação. Se criar uma nova implementação para o serviço, terá de ativar o ambiente de trabalho remoto dessa implementação. Se pretende sempre que o ambiente de trabalho remoto ativado, em seguida, deve considerar a integrar os scripts do PowerShell para o seu fluxo de trabalho de implementação.

## <a name="remote-desktop-into-a-role-instance"></a>Ambiente de trabalho remoto para uma instância de função
O [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet é utilizado para remoto ambiente de trabalho para uma instância de função específica do seu serviço em nuvem. Pode utilizar o *LocalPath* parâmetro para transferir o RDP ficheiro localmente. Ou pode utilizar o *iniciar* parâmetro diretamente iniciar a caixa de diálogo de ligação de ambiente de trabalho remoto para aceder a instância de função do serviço de nuvem.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Verifique se a extensão de ambiente de trabalho remoto está ativado num serviço
O [Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) cmdlet apresenta que ambiente de trabalho remoto está ativado ou desativado na implementação de serviço. O cmdlet devolve o nome de utilizador para o utilizador de ambiente de trabalho remoto e as funções que a extensão de ambiente de trabalho remota está ativada para. Por predefinição, isto ocorre numa ranhura de implementação e pode optar por utilizar em vez disso, o bloco de transição.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Remover extensão de ambiente de trabalho remoto a partir de um serviço
Se já tiver ativado a extensão de ambiente de trabalho remota numa implementação e tem de atualizar as definições de ambiente de trabalho remoto, primeiro remova a extensão. E volte a ativá-lo com as novas definições. Por exemplo, se pretender definir uma nova palavra-passe da conta de utilizador remoto ou a conta expirou. Efetuar este procedimento é necessário em implementações existentes que tenham a extensão de ambiente de trabalho remota ativada. Para novas implementações, pode simplesmente aplicar a extensão diretamente.

Para remover a extensão de ambiente de trabalho remota da implementação, pode utilizar o [remover AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) cmdlet. Pode também pode especificar opcionalmente o bloco de implementação e a função a partir do qual pretende remover a extensão de ambiente de trabalho remota.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Para remover completamente a configuração da extensão, deve chamar o *remover* cmdlet com o **UninstallConfiguration** parâmetro.
>
> O **UninstallConfiguration** parâmetro desinstala qualquer configuração de extensão que esteja aplicada ao serviço. Cada configuração da extensão está associada com a configuração do serviço. Chamar o *remover* cmdlet sem **UninstallConfiguration** disassociates o <mark>implementação</mark> da configuração da extensão, deste modo, removendo efetivamente o extensão. No entanto, a configuração da extensão continua a ser associada com o serviço.
>
>

## <a name="additional-resources"></a>Recursos adicionais

[Como configurar os serviços em nuvem](cloud-services-how-to-configure-portal.md)
[Cloud services FAQ – ambiente de trabalho remoto](cloud-services-faq.md)
