---
title: "Tamanho da pasta predefinida TEMP é demasiado pequeno para uma função | Microsoft Docs"
description: "Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar em execução sem espaço."
services: cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 7/26/2017
ms.author: v-six
ms.openlocfilehash: 02a185fbe3603aa7f033ea3d50dc6ad36dd7f8f6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Tamanho da pasta predefinida TEMP é demasiado pequeno numa função de web/trabalho do serviço de nuvem
O diretório temporário predefinido de uma função de trabalho ou da web de serviço do cloud tem um tamanho máximo de 100 MB, que podem tornar-se completa, a determinada altura. Este artigo descreve como evitar a ficar sem espaço para o diretório temporário.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que motivo ficar sem espaço em?
As variáveis de ambiente Windows padrão TEMP e TMP estão disponíveis para o código que está a ser executado na sua aplicação. TEMP e TMP apontam para um único diretório que tem um tamanho máximo de 100 MB. Todos os dados são armazenados neste diretório não são continuados entre o ciclo de vida do serviço em nuvem; Se as instâncias de função no serviço em nuvem sejam recicladas, o diretório é limpa.

## <a name="suggestion-to-fix-the-problem"></a>Sugestão para corrigir o problema
Implemente uma das alternativas seguintes:

* Configurar um recurso de armazenamento local e estão acessíveis diretamente em vez de utilizar TEMP ou TMP. Para aceder a um recurso de armazenamento local a partir do código está em execução na sua aplicação, chame o [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) método.
* Configurar um recurso de armazenamento local e os diretórios TEMP e TMP para apontar para o caminho do recurso local de armazenamento do ponto. Esta modificação deve ser efetuada dentro de [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) método.

Exemplo de código seguinte mostra como modificar os diretórios de destino para TEMP e TMP de dentro do método OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Ler um blogue que descreve [como aumentar o tamanho do Azure Web função ASP.NET pasta temporária](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Ver mais [artigos de resolução de problemas](/?tag=top-support-issue&product=cloud-services) para serviços em nuvem.

Para saber como resolver problemas de função de serviço de nuvem, utilizando os dados de diagnóstico do Azure PaaS computador, ver [série de blogues de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
