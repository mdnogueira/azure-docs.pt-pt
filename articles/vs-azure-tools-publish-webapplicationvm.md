---
title: WebApplicationVM publicar | Microsoft Docs
description: "Saiba como implementar uma aplicação web para uma máquina virtual. Este script cria os recursos necessários na sua subscrição do Azure, caso não existam."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: 2738fc1dff50a177a227ae2c7719bd9a192d82ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publicar-WebApplicationVM (script do Windows PowerShell)
Implementa uma aplicação web para uma máquina virtual. O script cria os recursos necessários na sua subscrição do Azure, caso não existam.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuração
O caminho para o ficheiro de configuração JSON que descreve os detalhes da implementação.

| Aliases | Nenhum |
| --- | --- |
| Necessário? |VERDADEIRO |
| Posição |com o nome |
| Valor predefinido |Nenhum |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="subscriptionname"></a>SubscriptionName
O nome da subscrição do Azure na qual pretende criar a máquina virtual.

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |Utiliza a subscrição primeiro no ficheiro de subscrição |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="webdeploypackage"></a>WebDeployPackage
O caminho para o pacote de implementação web para publicar para a máquina virtual. Pode criar este pacote utilizando o Assistente Publicar Web no Visual Studio. Consulte [como: criar um pacote de implementação Web no Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |Nenhum |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="allowuntrusted"></a>AllowUntrusted
Se for VERDADEIRO, permita a utilização de certificados que não são assinadas por uma autoridade de raiz fidedigna.

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |FALSO |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="vmpassword"></a>VMPassword
As credenciais da conta de máquina virtual. Exemplo: - VMPassword @{nome = "admin"; Palavra-passe = "password"}

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |Nenhum |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
As credenciais para a base de dados do SQL Server no Azure. Exemplo: - DatabaseServerPassword @{nome = "admin"; Palavra-passe = "password"}

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |Nenhum |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Se for VERDADEIRO, impressão as mensagens a partir do script no fluxo de saída.

| Aliases | Nenhum |
| --- | --- |
| Necessário? |FALSO |
| Posição |com o nome |
| Valor predefinido |FALSO |
| Aceitar entrada de pipeline? |FALSO |
| Aceitar carateres universais? |FALSO |

## <a name="remarks"></a>Observações
Para obter uma explicação completa de como utilizar o script para criar ambientes de desenvolvimento e teste, consulte [utilizando Scripts do Windows PowerShell para publicar a ambientes de teste e desenvolvimento](vs-azure-tools-publishing-using-powershell-scripts.md).

O ficheiro de configuração JSON Especifica os detalhes do que está a ser implementada. Inclui as informações que especificou quando criou o projeto, como o nome, o grupo de afinidade, a imagem VHD e o tamanho da máquina virtual. Também inclui os pontos finais na máquina virtual, as bases de dados para aprovisionar, se aplicável e web parâmetros de implementação. O código seguinte mostra um ficheiro de configuração do JSON de exemplo:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Pode editar o ficheiro de configuração JSON para alterar o que está aprovisionado. São necessários uma máquina virtual e um serviço em nuvem, mas a secção de base de dados é opcional.

