---
title: "Processar eventos de ciclo de vida do serviço em nuvem | Microsoft Docs"
description: "Saiba como os métodos de ciclo de vida de uma função de serviço em nuvem podem ser utilizados no .NET"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: eb78c05df3b3cdf3887334c11bdabd5cebb74747
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o Ciclo de Vida de uma função Web ou de Trabalho em .NET
Quando cria uma função de trabalho, pode alargar a [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) classe, que fornece métodos para a substituição que permitem-lhe responder a eventos de ciclo de vida. Para funções da web esta classe é opcional, pelo que deve utilizá-lo a responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Expandir a classe de RoleEntryPoint
O [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) classe inclui métodos que são chamados pelo Azure quando- **inicia**, **executa**, ou **interrompe** uma função web ou de trabalho. Opcionalmente, pode substituir estes métodos para gerir a inicialização de função, as sequências de encerramento de função ou o thread de execução da função. 

Quando a expandir **RoleEntryPoint**, deve ter conhecimento dos seguintes comportamentos dos métodos:

* O [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) e [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) métodos devolvem um valor booleano, pelo que é possível devolver **falso** destes métodos.
  
   Se o seu código devolve **falso**, o processo de função é terminado abruptamente, sem executar qualquer sequência encerramento pode ter no local. Em geral, deve evitar devolver **falso** do **OnStart** método.
* Qualquer não identificadas exceção dentro de uma sobrecarga de uma **RoleEntryPoint** método é tratado como uma exceção não processada.
  
   Se ocorrer uma excepção dentro de um dos métodos de ciclo de vida, o Azure irá elevar o [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) eventos e, em seguida, o processo é terminado. Depois da função tiver sido colocada offline, este será reiniciado pelo Azure. Quando ocorre uma exceção não processada, o [parar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) o evento não é gerado e o **OnStop** método não é chamado.

Se a função não inicia ou é Reciclagem entre a inicializar, ocupado e Estados de parar, o código pode ser a gerar uma exceção não processada dentro de um dos eventos de ciclo de vida reinicia a função de cada vez. Neste caso, utilize o [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) eventos para determinar a causa da exceção e processá-la corretamente. Também pode ser regressar a sua função do [executar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método, o que faz com que a função de reinício. Para obter mais informações sobre os Estados de implementação, consulte [problemas que causa funções comuns para reciclagem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se estiver a utilizar o **ferramentas do Azure para o Microsoft Visual Studio** para desenvolver a sua aplicação, os modelos de projeto de função automaticamente expandem o **RoleEntryPoint** classe para si, no *WebRole.cs* e *WorkerRole.cs* ficheiros.
> 
> 

## <a name="onstart-method"></a>Método OnStart
O **OnStart** método é chamado quando a instância de função é colocada online pelo Azure. Enquanto está a executar o código de OnStart, a instância de função está marcada como **ocupado** e nenhum tráfego externo será direcionado para a mesma pelo balanceador de carga. Pode substituir este método para efetuar o trabalho de inicialização, tais como processadores de eventos de implementar e iniciar [diagnósticos do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** devolve **verdadeiro**, a instância está a ser inicializada com êxito e Azure chama o **RoleEntryPoint.Run** método. Se **OnStart** devolve **falso**, a função termina imediatamente, sem executar quaisquer sequências de encerramento planeado.

O exemplo de código seguinte mostra como substituir o **OnStart** método. Este método, configura e inicia um monitor de diagnóstico quando a instância de função é iniciado e configura uma transferência de dados de registo para uma conta de armazenamento:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método de OnStop
O **OnStop** método for chamado depois de uma instância de função tiver sido colocada offline pelo Azure e antes do processo é terminado. Pode substituir este método para chamar o código necessário para a instância de função para encerrada corretamente.

> [!IMPORTANT]
> Código em execução no **OnStop** método tem um período limitado de tempo a concluir quando é denominado por motivos que não seja um encerramento iniciada pelo utilizador. Depois de decorrido este tempo, o processo é terminado, pelo que deve certificar-se de que esse código no **OnStop** método pode executar rapidamente ou tolerates não está em execução para conclusão. O **OnStop** método for chamado depois do **parar** o evento é gerado.
> 
> 

## <a name="run-method"></a>Executar o método
Pode substituir o **executar** método para implementar um thread de execução longa para a instância de função.

A substituir o **executar** método não é necessário; a implementação predefinida é iniciado um thread que permaneça suspenso indefinidamente. Se substituir o **executar** método, o código deverá bloquear indefinidamente. Se o **executar** método devolve, a função é automaticamente transições reciclada; por outras palavras, o Azure gera o **parar** eventos e as chamadas a **OnStop** método para que as sequências de encerramento podem ser executadas antes da função é colocada offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementar os métodos de ciclo de vida do ASP.NET para uma função da web
Pode utilizar os métodos de ciclo de vida do ASP.NET, para além dos fornecidos pelo **RoleEntryPoint** classe, para gerir sequências de inicialização e encerramento de uma função da web. Isto poderá ser útil para fins de compatibilidade se são transferências uma aplicação ASP.NET existente para o Azure. Os métodos de ciclo de vida do ASP.NET são chamados a partir do **RoleEntryPoint** métodos. O **aplicação\_iniciar** método for chamado depois do **RoleEntryPoint.OnStart** método concluída. O **aplicação\_final** método é chamado antes do **RoleEntryPoint.OnStop** método é chamado.

## <a name="next-steps"></a>Passos seguintes
Saiba como [criar um pacote de serviço em nuvem](cloud-services-model-and-package.md).

