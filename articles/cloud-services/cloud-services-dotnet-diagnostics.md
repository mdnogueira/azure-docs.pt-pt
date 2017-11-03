---
title: "Como utilizar o diagnóstico do Azure (.NET) com serviços em nuvem | Microsoft Docs"
description: "Diagnóstico do Azure a utilizar para recolher dados dos cloud Services do Azure para depuração, medir o desempenho, monitorização, análise de tráfego e muito mais."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: 
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: robb
ms.openlocfilehash: 333d2f26ce043a167fb84858c8327cb39e868ffa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Ativar o diagnóstico do Azure nos serviços em nuvem do Azure
Consulte [descrição geral do Azure Diagnostics](../azure-diagnostics.md) para um fundo no diagnóstico do Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Como ativar o diagnóstico numa função de trabalho
Esta explicação passo a passo descreve como implementar uma função de trabalho do Azure que emite os dados de telemetria utilizando a classe de .NET EventSource. Diagnóstico do Azure é utilizado para recolher os dados de telemetria e armazene-o de uma conta de armazenamento do Azure. Ao criar uma função de trabalho, o Visual Studio permite automaticamente 1.0 de diagnóstico como parte da solução de SDKs do Azure para .NET 2.4 e versões anteriores. As seguintes instruções descrevem o processo para criar a função de trabalho, a desativação 1.0 de diagnóstico da solução e implementação diagnóstico 1.2 ou 1.3 à sua função de trabalho.

### <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma subscrição do Azure e estiver a utilizar o Visual Studio com o Azure SDK. Se não tiver uma subscrição do Azure, pode inscrever-se para obter o [avaliação gratuita][Free Trial]. Certifique-se de que [instalar e configurar o Azure PowerShell versão 0.8.7 ou posterior][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Passo 1: Criar uma função de trabalho
1. Inicie o **Visual Studio**.
2. Criar um **o serviço em nuvem do Azure** projeto a **nuvem** modelo que tenha como alvo o .NET Framework 4.5.  Nomeie o projeto "WadExample" e clique em Ok.
3. Selecione **função de trabalho** e clique em Ok. O projeto será criado.
4. No **Explorador de soluções**, faça duplo clique o **WorkerRole1** ficheiro de propriedades.
5. No **configuração** separador xecutar uma verificação **ativar diagnósticos** para desativar 1.0 de diagnóstico do Azure SDK (2.4 e anterior).
6. Crie a sua solução para verificar que tem sem erros.

### <a name="step-2-instrument-your-code"></a>Passo 2: Instrumentar código
Substitua os conteúdos de WorkerRole.cs com o seguinte código. A classe SampleEventSourceWriter, herdadas o [EventSource classe][EventSource Class], implementa quatro métodos de registo: **SendEnums**, **MessageMethod**, **SetOther** e **HighFreq**. O primeiro parâmetro para o **WriteEvent** método define o ID de evento correspondentes. O método de execução implementa um ciclo infinito que chama a cada um dos métodos implementados no registo de **SampleEventSourceWriter** classe cada 10 segundos.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Passo 3: Implementar a sua função de trabalho

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implementar a função de trabalho no Azure do Visual Studio, selecionando o **WadExample** projeto no Explorador de soluções, em seguida, **publicar** do **criar** menu.
2. Escolha a sua subscrição.
3. No **definições de publicação do Microsoft Azure** caixa de diálogo, selecione **criar novo...** .
4. No **criar serviço de nuvem e de conta de armazenamento** caixa de diálogo, introduza um **nome** (por exemplo, "WadExample") e selecione uma região ou grupo de afinidade.
5. Definir o **ambiente** para **transição**.
6. Modificar quaisquer outros **definições** conforme adequado e clique em **publicar**.
7. Depois de concluída a implementação, certifique-se no portal do Azure que o serviço em nuvem está a ser um **executar** estado.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Passo 4: Criar o ficheiro de configuração de diagnósticos e instalar a extensão
1. Transfira a definição de esquema do ficheiro de configuração pública executando o seguinte comando do PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adicionar um ficheiro XML a sua **WorkerRole1** projeto clicando no **WorkerRole1** projeto e selecione **adicionar** -> **Novo Item...** -> **Visual c# itens** -> **dados** -> **ficheiro XML**. Nome de ficheiro "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associe o WadConfig.xsd com o ficheiro de configuração. Certifique-se a janela do editor WadExample.xml a janela ativa. Prima **F4** para abrir o **propriedades** janela. Clique em de **esquemas** propriedade no **propriedades** janela. Clique em de **...** no **esquemas** propriedade. Clique no botão **Adicionar…** botão e navegue para a localização onde guardou o ficheiro XSD e selecione o ficheiro WadConfig.xsd. Clique em **OK**.

4. Substitua o conteúdo do ficheiro de configuração WadExample.xml com o seguinte XML e guarde o ficheiro. Este ficheiro de configuração define alguns contadores de desempenho para recolher: um para utilização da CPU e outro para a utilização da memória. Em seguida, a configuração define os eventos de quatro correspondente para os métodos na classe SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Passo 5: Instalar o diagnóstico na sua função de trabalho
Os cmdlets do PowerShell para gerir o diagnóstico numa função web ou de trabalho são: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension e Remove-AzureServiceDiagnosticsExtension.

1. Abra o PowerShell do Azure.
2. Executar o script para instalar o diagnóstico na sua função de trabalho (substituir *StorageAccountKey* com a chave de conta de armazenamento para a sua conta de armazenamento wadexample e *config_path* com o caminho para o *WadExample.xml* ficheiros):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Passo 6: Observar os dados de telemetria
No Visual Studio **Explorador de servidores**, navegue para a conta de armazenamento wadexample. Depois do serviço em nuvem tem estado em execução cerca de cinco (5) minutos, deverá ver as tabelas **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** e **WADSetOtherTable**. Faça duplo clique nas tabelas para ver a telemetria que tenha sido recolhida.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Esquema do ficheiro de configuração
O ficheiro de configuração de diagnósticos define valores que são utilizados para inicializar as definições de diagnóstico de configuração quando o agente de diagnóstico é iniciado. Consulte o [referência mais recente do esquema](https://msdn.microsoft.com/library/azure/mt634524.aspx) para obter exemplos e os valores válidos.

## <a name="troubleshooting"></a>Resolução de problemas
Se tiver problemas, consulte [resolução de problemas do diagnóstico do Azure](../azure-diagnostics-troubleshooting.md) para obter ajuda com problemas comuns.

## <a name="next-steps"></a>Passos Seguintes
[Ver uma lista de máquina virtual relacionados do Azure diagnóstico artigos](../monitoring-and-diagnostics/azure-diagnostics.md#cloud-services-using-azure-diagnostics) para alterar os dados que está a recolher, resolver problemas ou saber mais sobre o diagnóstico em geral.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
