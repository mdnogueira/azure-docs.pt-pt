---
title: "Versões do esquema de configuração de extensão do Azure Diagnostics e o histórico | Microsoft Docs"
description: "Relevantes para a recolha de contadores de desempenho em máquinas virtuais do Azure, conjuntos de dimensionamento de VM, Service Fabric e serviços em nuvem."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2017
ms.author: robb
ms.openlocfilehash: 119e8a237f24cdc80a1ab8e376f2b308c9eada05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-extention-configuration-schema-versions-and-history"></a>Versões do esquema de configuração de extensão do Azure Diagnostics e o histórico
Esta página de índices versões de esquema de extensão de diagnóstico do Azure vem incluído como parte do Microsoft Azure SDK.  

> [!NOTE]
> A extensão de diagnóstico do Azure é o componente utilizado para recolher os contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure 
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric 
> - Serviços Cloud 
> - Grupos de Segurança de Rede
> 
> Esta página só é relevante se estiver a utilizar um destes serviços.

A extensão de diagnóstico do Azure é utilizada com outros produtos de diagnóstico do Microsoft Azure Monitor, Application Insights e análise de registos. Para obter mais informações consulte [descrição geral de ferramentas de monitorização Microsoft](monitoring-overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Versões do Azure SDK e ao diagnóstico do gráfico de envio  

|Versão do SDK do Azure | Versão da extensão de diagnóstico | Modelo|  
|------------------|-------------------------------|------|  
|1. x               |1.0                            |Plug-in|  
|2.0 - 2.4         |1.0                            |Plug-in|  
|2.5               |1.2                            |Extensão|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|



 Versão do Azure Diagnostics 1.0 primeiro fornecido de um modelo de plug-in –, o que significa que, quando instalou o SDK do Azure, obteve a versão do diagnóstico do Azure incluída com o mesmo.  

 Começando com o SDK 2.5 (diagnostics versão 1.2), diagnóstico do Azure Ocorreu um erro para um modelo de extensão. As ferramentas de utilizar novas funcionalidades só estavam disponíveis no mais recente do Azure SDKs, mas qualquer serviço de diagnóstico do Azure a utilizar seria processará a versão mais recente do envio diretamente a partir do Azure. Por exemplo, qualquer pessoa que ainda utilize SDK 2.5 seria possível carregar a versão mais recente mostrada na tabela anterior, independentemente se estão a utilizar as funcionalidades mais recentes.  

## <a name="schemas-index"></a>Índice de esquemas  
Versões diferentes do diagnóstico do Azure utilizam esquemas de configuração diferente. 

[Esquema de configuração de diagnósticos 1.0](azure-diagnostics-schema-1dot0.md)  

[Esquema de configuração de diagnósticos 1.2](azure-diagnostics-schema-1dot2.md)  

[Diagnóstico de 1.3 e esquema de configuração posterior](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Histórico de versões


### <a name="diagnostics-extension-19"></a>Extensão de diagnóstico 1.9 
Adicionado suporte de Docker.


### <a name="diagnostics-extension-181"></a>Extensão de diagnóstico 1.8.1 
Pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração privada. Se não for fornecido um token SAS, a chave de conta do storage é ignorada.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Extensão de diagnóstico 1.8 
Tipo de armazenamento adicionada a PublicConfig. Pode ser StorageType *tabela*, *Blob*, *TableAndBlob*. *Tabela* é a predefinição.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Extensão de diagnóstico 1.7. 
Foi adicionada a capacidade para encaminhar para EventHub.

### <a name="diagnostics-extension-15"></a>Extensão de diagnóstico 1.5
Adicionar o elemento sinks e a capacidade de enviar dados de diagnóstico para [Application Insights](../application-insights/app-insights-cloudservices.md) tornando mais fácil diagnosticar problemas na sua aplicação, bem como o nível de infraestrutura e sistema.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Extensão do Azure SDK 2.6 e diagnóstico 1.3 
Para projetos do serviço em nuvem no Visual Studio, as seguintes alterações foram efetuadas. (Estas alterações também se aplicam a versões posteriores do SDK do Azure.)

* O emulador local agora suporta diagnóstico. Isto significa que pode recolher dados de diagnóstico e certifique-se de que a aplicação está a criar os rastreios direita enquanto estiver a desenvolver e testar no Visual Studio. A cadeia de ligação `UseDevelopmentStorage=true` permite a recolha de dados de diagnóstico enquanto estiver a executar o projeto de serviço em nuvem no Visual Studio, utilizando o emulador do storage do Azure. Todos os dados de diagnóstico são recolhidos na conta do storage (armazenamento de desenvolvimento).
* A cadeia de ligação de conta de armazenamento de diagnóstico (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) é novamente armazenada no ficheiro de configuração (. cscfg) do serviço. No Azure SDK 2.5 a conta de armazenamento do diagnostics foi especificada no ficheiro diagnostics.wadcfgx.

Existem algumas diferenças importantes entre como a cadeia de ligação trabalhado no Azure SDK 2.4 e anterior e como funciona no Azure SDK 2.6 e posterior.

* No Azure SDK 2.4 e versões anteriores, a cadeia de ligação era utilizada como um tempo de execução pelo plug-in de diagnóstico para obter as informações de conta de armazenamento para transferir os registos de diagnóstico.
* No Azure SDK 2.6 ou posterior, a cadeia de ligação de diagnóstico é utilizada pelo Visual Studio para configurar a extensão de diagnóstico com as informações de conta de armazenamento adequado durante a publicação. A cadeia de ligação permite-lhe definir as contas de armazenamento diferentes para configurações de serviço diferente que o Visual Studio irá utilizar quando publicar. No entanto, porque o plug-in de diagnóstico já não está disponível (após o Azure SDK 2.5), o ficheiro. cscfg por si só não é possível ativar a extensão de diagnóstico. Tem de ativar a extensão em separado através de ferramentas, como o Visual Studio ou o PowerShell.
* Para simplificar o processo de configuração a extensão de diagnóstico com o PowerShell, o resultado de pacote do Visual Studio também contém a configuração pública XML para a extensão de diagnóstico para cada função. Visual Studio utiliza a cadeia de ligação de diagnóstico para preencher as informações de conta de armazenamento presentes na configuração pública. Os ficheiros de configuração pública são criados na pasta de extensões e seguem o padrão PaaSDiagnostics. <RoleName>. PubConfig.xml. Todas as implementações do PowerShell com base podem utilizar este padrão para mapear cada configuração para uma função.
* A cadeia de ligação no ficheiro. cscfg também é utilizada pelo portal do Azure para aceder os dados de diagnóstico para pode aparecer no **monitorização** separador. A cadeia de ligação é necessária para configurar o serviço para mostrar dados de monitorização verbosos no portal.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrar projetos ao Azure SDK 2.6 ou posterior
Quando migrar do Azure SDK 2.5 para o Azure SDK 2.6 ou posterior, se tiver uma conta de armazenamento do diagnostics especificada no ficheiro .wadcfgx, em seguida,-permanecerão não existe. Para tirar partido da flexibilidade de utilização de contas de armazenamento diferentes para configurações de armazenamento diferente, terá de adicionar manualmente a cadeia de ligação ao seu projeto. Se estiver a migrar um projeto do Azure SDK 2.4 ou anteriormente para o Azure SDK 2.6, as cadeias de ligação de diagnóstico são preservadas. No entanto,. tenha em atenção que as alterações na forma como cadeias de ligação são tratadas no Azure SDK 2.6 conforme especificado na secção anterior.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Como o Visual Studio determina a conta de armazenamento de diagnóstico
* Se for especificada uma cadeia de ligação de diagnóstico num ficheiro. cscfg, o Visual Studio utiliza-o para configurar a extensão de diagnóstico ao publicar e ao gerar os ficheiros xml de configuração pública durante empacotamento.
* Não se for especificada nenhuma cadeia de ligação de diagnóstico num ficheiro. cscfg, em seguida, Visual Studio retrocede para utilizar a conta de armazenamento especificada no ficheiro .wadcfgx para configurar a extensão de diagnóstico quando publicar e gerar os ficheiros xml de configuração pública Quando o empacotamento.
* A cadeia de ligação de diagnóstico num ficheiro. cscfg tem precedência sobre a conta do storage no ficheiro .wadcfgx. Se for especificada uma cadeia de ligação de diagnóstico num ficheiro. cscfg, o Visual Studio utiliza esse e ignora a conta de armazenamento .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>O que faz as "atualização desenvolvimento armazenamento cadeias de ligação..." fazer a caixa de verificação?
A caixa de verificação **atualizar as cadeias de ligação de armazenamento de desenvolvimento de diagnóstico e de colocação em cache com credenciais de conta de armazenamento do Microsoft Azure durante a publicação para o Microsoft Azure** dá-lhe uma maneira conveniente para atualizar qualquer desenvolvimento cadeias de ligação de conta de armazenamento com a conta de armazenamento do Azure especificada durante a publicação.

Por exemplo, suponha que selecionar esta caixa de verificação e especifica a cadeia de ligação de diagnóstico `UseDevelopmentStorage=true`. Quando publica o projeto no Azure, Visual Studio será automaticamente atualizado a cadeia de ligação de diagnóstico com a conta de armazenamento que especificou no Assistente de publicação. No entanto, se uma conta de armazenamento real foi especificada como a cadeia de ligação de diagnóstico, em seguida, essa conta é utilizada em vez disso.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diferenças de funcionalidade de diagnóstico entre o Azure SDK 2.4 e anterior e do Azure SDK 2,5 e posterior
Se estiver a atualizar o projeto do Azure SDK 2.4 para o Azure SDK 2.5 ou posterior, deve tenha em consideração as seguintes diferenças de funcionalidade de diagnóstico.

* **APIs de configuração foram preteridas** – configuração programática do diagnostics está disponível no Azure SDK 2.4 ou versões anteriores, mas foi preterida no Azure SDK 2.5 e posterior. Se a configuração de diagnósticos atualmente está definida no código, terá de reconfigurar essas definições a partir do zero no projeto migrado pela ordem de diagnóstico para manter a funcionar. O ficheiro de configuração de diagnóstico para o Azure SDK 2.4 é diagnostics.wadcfg e diagnostics.wadcfgx para o Azure SDK 2.5 e posterior.
* **Diagnósticos para aplicações de serviço em nuvem só podem ser configurados ao nível da função, não ao nível da instância.**
* **Sempre que implementar a sua aplicação, a configuração de diagnóstico foi atualizada** – Isto pode causar problemas de paridade, se alterar a configuração de diagnósticos no Explorador de servidores e, em seguida, volte a implementar a sua aplicação.
* **No Azure SDK 2.5 e posterior, informações de estado de falha são configuradas no ficheiro de configuração de diagnósticos, não no código** – se tiver configuradas no código de capturas de falhas, terá da transferir manualmente a configuração a partir do código ao ficheiro de configuração, porque o capturas de falhas não são transferidas durante a migração para o Azure SDK 2.6.

