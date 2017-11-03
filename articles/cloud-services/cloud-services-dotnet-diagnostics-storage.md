---
title: "Arquivo e a vista de dados de diagnóstico no armazenamento do Azure | Microsoft Docs"
description: "Obter dados de diagnóstico do Azure para o armazenamento do Azure e vê-la"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Arquivo e a vista de dados de diagnóstico no armazenamento do Azure
Dados de diagnóstico não são permanentemente armazenados, a menos que a transferência para o emulador de armazenamento do Microsoft Azure ou para armazenamento do Azure. Uma vez no armazenamento, podem ser visualizado com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especifique uma conta de armazenamento
Especifique a conta de armazenamento que pretende utilizar no ficheiro serviceconfiguration. Cscfg. As informações de conta são definidas como uma cadeia de ligação na definição de configuração. O exemplo seguinte mostra a cadeia de ligação predefinido criada para um novo projeto de serviço em nuvem no Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Pode alterar esta cadeia de ligação para fornecer informações de conta para uma conta de armazenamento do Azure.

Dependendo do tipo de dados de diagnóstico que estão a ser recolhidos, diagnósticos do Azure utiliza o serviço Blob ou o serviço de tabela. A tabela seguinte mostra as origens de dados que são mantidas e o respetivo formato.

| Origem de dados | Formato de armazenamento |
| --- | --- |
| Registos do Azure |Tabela |
| Registos do IIS 7.0 |Blobs |
| Registos de infraestrutura de diagnóstico do Azure |Tabela |
| Falha de registos de rastreio de pedido |Blobs |
| Registos de eventos do Windows |Tabela |
| Contadores de desempenho |Tabela |
| Informações de falhas |Blobs |
| Registos de erros personalizados |Blobs |

## <a name="transfer-diagnostic-data"></a>Transferência de dados de diagnóstico
Para o SDK 2.5 e posterior, o pedido de transferência de dados de diagnóstico pode ocorrer através do ficheiro de configuração. Pode transferir dados de diagnóstico em intervalos agendados conforme especificado na configuração.

SDK 2.4 e anterior pode pedir para transferir os dados de diagnóstico através do ficheiro de configuração, bem como através de programação. A abordagem programática também lhe permite efetuar transferências da pedido.

> [!IMPORTANT]
> Quando a transferência de dados de diagnóstico para uma conta de armazenamento do Azure, pode implicar custos para os recursos de armazenamento que utiliza os dados de diagnóstico.
> 
> 

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico
Dados de registo são armazenados no armazenamento de BLOBs ou tabela com os seguintes nomes:

**Tabelas**

* **WadLogsTable** - registos escritos no código utilizando o serviço de escuta de rastreio.
* **WADDiagnosticInfrastructureLogsTable** -alterações de configuração e o monitor de diagnóstico.
* **WADDirectoriesTable** – diretórios que está a monitorizar o monitor de diagnóstico.  Isto inclui os registos IIS, IIS não conseguiu registos de pedido e diretórios personalizados.  A localização do ficheiro de registo de blob está especificada no campo do contentor e o nome do blob é no campo RelativePath.  O campo de AbsolutePath indica a localização e o nome do ficheiro, que existia na máquina virtual do Azure.
* **WADPerformanceCountersTable** – contadores de desempenho.
* **WADWindowsEventLogsTable** – registos de eventos do Windows.

**Blobs**

* **contentor de controlo de WAD** – (apenas para o SDK 2.4 e anterior) contém os ficheiros de configuração XML que controla o diagnóstico do Azure.
* **WAD-iis-failedreqlogfiles** – contém informações de registos de falha de pedidos do IIS.
* **WAD-iis-logfiles** – contém informações sobre os registos do IIS.
* **"personalizada"** – um contentor personalizado com base na configuração de diretórios que são monitorizados pelo monitor de diagnóstico.  O nome deste contentor de blob será especificado no WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para ver os dados de diagnóstico
Várias ferramentas estão disponíveis para ver os dados depois de serem transferidos para o armazenamento. Por exemplo:

* Explorador de servidores no Visual Studio – se tiver instalado as ferramentas do Azure para o Microsoft Visual Studio, pode utilizar o nó de armazenamento do Azure no Explorador de servidores para ver como só de leitura de BLOBs e os dados da tabela das suas contas do storage do Azure. Pode apresentar os dados da sua conta do emulador de armazenamento local e também das contas do storage que criou para o Azure. Para obter mais informações, consulte [a navegação nos e gerir recursos de armazenamento com o Explorador de servidores](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Gestor de diagnóstico do Azure que permite-lhe ver, transfira e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes
[Analisar o fluxo de uma aplicação de serviços em nuvem com o diagnóstico do Azure](cloud-services-dotnet-diagnostics-trace-flow.md)

