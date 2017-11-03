---
title: "Analisar o fluxo de uma aplicação de serviços na nuvem com o diagnóstico do Azure | Microsoft Docs"
description: "Adicione mensagens de rastreio para uma aplicação do Azure para o ajudar a depuração, medir o desempenho, monitorização, análise de tráfego e muito mais."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 09934772-cc07-4fd2-ba88-b224ca192f8e
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/20/2016
ms.author: robb
ms.openlocfilehash: 35b4a4270846c54a1ca760e803ef7adba60cf03b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Analisar o fluxo de uma aplicação de serviços em nuvem com o diagnóstico do Azure
O rastreio é uma forma para que possa monitorizar a execução da aplicação enquanto estiver em execução. Pode utilizar o [Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), e [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) classes para registar informações sobre os erros e execução das aplicações nos registos, ficheiros de texto ou outros dispositivos para análise posterior. Para mais informações sobre o rastreio, consulte [rastreio e Instrumentar aplicações](https://msdn.microsoft.com/library/zs6s4h68.aspx).

## <a name="use-trace-statements-and-trace-switches"></a>Utilize as instruções de rastreio e comutadores de rastreio
Rastreio de implementar na sua aplicação de serviços em nuvem, adicionando o [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) para a configuração da aplicação e efetuar chamadas TRACE ou debug na sua código da aplicação. Utilize o ficheiro de configuração *App. config* para as funções de trabalho e o *Web. config* para funções da web. Quando cria um novo serviço alojado através de um modelo do Visual Studio, diagnósticos do Azure é automaticamente adicionado ao projeto e a DiagnosticMonitorTraceListener é adicionada ao ficheiro de configuração adequada para as funções que adicionar.

Para obter informações sobre a colocação de declarações de rastreio, consulte [como: adicionar declarações de rastreio ao código da aplicação](https://msdn.microsoft.com/library/zd83saa2.aspx).

Colocando [comutadores de rastreio](https://msdn.microsoft.com/library/3at424ac.aspx) no seu código, pode controlar se ocorre rastreio e extensivo como está. Isto permite-lhe monitorizar o estado da aplicação num ambiente de produção. Isto é especialmente importante numa aplicação empresarial que utiliza vários componentes executadas em vários computadores. Para obter mais informações, consulte [como: configurar comutadores de rastreio](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o serviço de escuta de rastreio de uma aplicação do Azure
Rastreio, a depuração e TraceSource, tem de configurar "os serviços de escuta" para recolher e registar as mensagens que são enviadas. Serviços de escuta de recolhem, armazenam e encaminhar mensagens de rastreio. Estes direcionam a saída de rastreio para um destino adequado, como um registo, a janela ou o ficheiro de texto. Diagnóstico do Azure utiliza o [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) classe.

Antes de concluir o procedimento seguinte, o monitor de diagnóstico do Azure tem de ser inicializado. Para tal, consulte [ativar diagnósticos no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Tenha em atenção que se utilizar os modelos que são fornecidos pelo Visual Studio, a configuração do serviço de escuta é adicionada automaticamente por si.

### <a name="add-a-trace-listener"></a>Adicionar um serviço de escuta de rastreio
1. Abra o ficheiro Web. config ou o App. config para a sua função.
2. Adicione o seguinte código ao ficheiro. Altere o atributo de versão para utilizar o número de versão da assemblagem que estão a referenciar. A versão de assemblagem não necessariamente altera com cada versão do SDK do Azure, exceto se existem atualizações ao mesmo.
   
    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                    <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
   > [!IMPORTANT]
   > Certifique-se de que tem uma referência de projecto à assemblagem Microsoft.WindowsAzure.Diagnostics. Atualize o número de versão no xml acima para corresponderem à versão da assemblagem Microsoft.WindowsAzure.Diagnostics referenciada.
   > 
   > 
3. Guarde o ficheiro de configuração.

Para obter mais informações sobre os serviços de escuta, consulte [serviços de escuta de rastreio](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Depois de concluir os passos para adicionar o serviço de escuta, pode adicionar declarações de rastreio para o seu código.

### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a declaração de rastreio para o seu código
1. Abra um ficheiro de origem para a sua aplicação. Por exemplo, o <RoleName>CS ficheiro para a função de trabalho ou a função da web.
2. Adicione o seguinte utilizando a instrução se já não estiver adicionado:
    ```
        using System.Diagnostics;
    ```
3. Adicione declarações de rastreio onde pretende capturar informações sobre o estado da aplicação. Pode utilizar diferentes métodos para formatar a saída da declaração de rastreio. Para obter mais informações, consulte [como: adicionar declarações de rastreio ao código da aplicação](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Guarde o ficheiro de origem.

