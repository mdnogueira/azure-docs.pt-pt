---
title: "Extensão de diagnóstico do Azure 1.3 e esquema de configuração posterior | Microsoft Docs"
description: "Versão do esquema de 1.3 e posterior diagnóstico do Azure vem incluído como parte do 2.4 de SDK do Microsoft Azure e mais tarde."
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
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 2ee66e0f41868d7d5411605596a22c00b5712896
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>1.3 de diagnóstico do Azure e o esquema de configuração posterior
> [!NOTE]
> A extensão de diagnóstico do Azure é o componente utilizado para recolher os contadores de desempenho e outras estatísticas de:
> - Máquinas Virtuais do Azure 
> - Conjuntos de Dimensionamento de Máquinas Virtuais
> - Service Fabric 
> - Serviços Cloud 
> - Grupos de Segurança de Rede
> 
> Esta página só é relevante se estiver a utilizar um destes serviços.

Esta página é válida para as versões de 1.3 e mais recente do Azure SDK (2.4 e mais recente). As secções de configuração mais recentes são comentadas para mostrar na versão que foram adicionados.  

O ficheiro de configuração descrito aqui é utilizado para definir as definições de diagnóstico de configuração quando o monitor de diagnóstico é iniciado.  

A extensão é utilizada em conjunto com outros produtos de diagnóstico do Microsoft Azure Monitor, Application Insights e análise de registos.



Transfira a definição de esquema do ficheiro de configuração pública executando o seguinte comando do PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [extensão de diagnóstico do Azure](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo de ficheiro de configuração de diagnóstico  
 O exemplo seguinte mostra um ficheiro de configuração típica de diagnóstico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 --> 
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
   
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
   
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Equivalente JSON do ficheiro de configuração XML anterior. 

O PublicConfig e PrivateConfig estão separadas porque, na maioria dos casos de utilização de json, são transmitidos como diferentes variáveis. Nestes casos incluem modelos do Resource Manager, o conjunto de dimensionamento de Máquina Virtual, PowerShell e o Visual Studio. 

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Ler nesta página  
 As etiquetas seguintes são aproximadamente pela ordem mostrada no exemplo anterior.  Se não vir uma descrição completa em que o esperado, procure a página para o elemento ou atributo.  

## <a name="common-attribute-types"></a>Tipos de atributo comuns  
 **scheduledTransferPeriod** atributo aparece no vários elementos. É o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Elemento DiagnosticsConfiguration  
 *Árvore: Raiz-DiagnosticsConfiguration*

Adicionados na versão 1.3.  

O elemento de nível superior do ficheiro de configuração de diagnóstico.  

**Atributo** xmlns - espaço de nomes XML para o ficheiro de configuração de diagnóstico é:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**PublicConfig**|Necessário. Ver descrição noutro local nesta página.|  
|**PrivateConfig**|Opcional. Ver descrição noutro local nesta página.|  
|**IsEnabled**|valor booleano. Ver descrição noutro local nesta página.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig*

 Descreve a configuração de diagnósticos público.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**WadCfg**|Necessário. Ver descrição noutro local nesta página.|  
|**StorageAccount**|O nome da conta do Storage do Azure para armazenar os dados. Também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Pode ser *tabela*, *Blob*, ou *TableAndBlob*. A tabela é predefinido. Quando é escolhido TableAndBlob, dados de diagnóstico são escritos duas vezes – uma vez para cada tipo.|  
|**LocalResourceDirectory**|O diretório na máquina virtual em que o agente de monitorização armazena dados de eventos. Se não, definido, é utilizado o diretório predefinido:<br /><br /> Para uma função de trabalho/web:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> - **caminho** -o diretório no sistema para ser utilizado por diagnósticos do Azure.<br /><br /> - **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG de raiz*
 
 Identifica e configura os dados de telemetria a ser recolhido.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration 
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Necessário 

|Atributos|Descrição|  
|----------------|-----------------|  
| **overallQuotaInMB** | A quantidade máxima de espaço no disco local que pode ser utilizada por vários tipos de dados de diagnóstico recolhidos pelo diagnósticos do Azure. O valor predefinido é 5120 MB.<br />
|**useProxyServer** | Configure o diagnóstico do Azure para utilizar as definições do servidor proxy conforme definido nas definições de i/e.|  

<br /> <br />

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumps**|Ver descrição noutro local nesta página.|  
|**DiagnosticInfrastructureLogs**|Ative a recolha dos registos gerados por diagnósticos do Azure. Os registos de diagnóstico de infraestrutura são úteis para resolução de problemas do sistema de diagnóstico. Atributos opcionais quantos são:<br /><br /> - **scheduledTransferLogLevelFilter** -configura o nível de gravidade mínima dos registos recolhidos.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Diretórios**|Ver descrição noutro local nesta página.|  
|**EtwProviders**|Ver descrição noutro local nesta página.|  
|**Métricas**|Ver descrição noutro local nesta página.|  
|**PerformanceCounters**|Ver descrição noutro local nesta página.|  
|**WindowsEventLog**|Ver descrição noutro local nesta página.| 
|**DockerSources**|Ver descrição noutro local nesta página. | 



## <a name="crashdumps-element"></a>Elemento CrashDumps  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps de raiz*
 
 Ative a recolha de informações de falhas.  

|Atributos|Descrição|  
|----------------|-----------------|  
|**containerName**|Opcional. O nome do contentor do blob na sua conta do Storage do Azure a ser utilizado para armazenar informações de falhas.|  
|**crashDumpType**|Opcional.  Configura o diagnóstico do Azure para recolher informações de mini ou falhas completa.|  
|**directoryQuotaPercentage**|Opcional.  Configura a percentagem de **overallQuotaInMB** seja reservado para informações de falhas na VM.|  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Define os valores de configuração para cada processo.<br /><br /> Também é necessário o atributo seguinte:<br /><br /> **processName** -o nome do processo de que pretende que o diagnóstico do Azure para recolher as informações de falhas para.|  

## <a name="directories-element"></a>Elemento de diretórios 
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - diretórios de raiz*

 Permite a recolha do conteúdo de um diretório, registos de IIS não conseguiu de pedidos de acesso e/ou os registos IIS.  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**IISLogs**|Incluindo este elemento na configuração permite a recolha de registos do IIS:<br /><br /> **containerName** -o nome do contentor do blob na sua conta do Storage do Azure a ser utilizado para armazenar os registos IIS.|   
|**FailedRequestLogs**|Incluindo este elemento na configuração permite a recolha de registos sobre pedidos com falhas para uma aplicação ou site do IIS. Tem também de ativar as opções de rastreio em **sistema. Servidor Web** no **Web. config**.|  
|**Origens de dados**|Uma lista de diretórios a monitorizar.| 




## <a name="datasources-element"></a>Elemento de origens de dados  
 *Árvore: Origens de dados raiz - DiagnosticsConfiguration - PublicConfig - diretórios WadCFG - DiagnosticMonitorConfiguration-*

 Uma lista de diretórios a monitorizar.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo necessário:<br /><br /> **containerName** -o nome do contentor do blob no armazenamento do Azure à conta que a ser utilizado para armazenar os ficheiros de registo.|  





## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - diretórios - as origens de dados - DirectoryConfiguration de raiz*

 Pode incluir um o **absoluto** ou **LocalResource** elemento mas não ambos.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório a monitorizar. São necessários os seguintes atributos:<br /><br /> - **Caminho** -o caminho absoluto para o diretório a monitorizar.<br /><br /> - **expandEnvironment** -configura se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo para um recurso local para monitorizar. Os atributos necessários são:<br /><br /> - **Nome** -o recurso local que contém o diretório para monitorizar<br /><br /> - **relativePath** -o caminho relativo ao nome que contém o diretório para monitorizar|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders de raiz*

 Configura a recolha de eventos do ETW de EventSource de e/ou o manifesto do ETW baseado em fornecedores.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a recolha de eventos gerados a partir de [EventSource classe](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necessário:<br /><br /> **fornecedor** -o nome da classe do evento de EventSource.<br /><br /> Atributos opcionais quantos são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta do storage.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Atributo necessário:<br /><br /> **fornecedor** -o GUID do fornecedor de evento<br /><br /> Atributos opcionais quantos são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta do storage.<br /><br /> - **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration de raiz*

 Configura a recolha de eventos gerados a partir de [EventSource classe](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination** -o nome da tabela para armazenar eventos|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  



## <a name="metrics-element"></a>Elemento de métricas  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - as métricas de raiz*

 Permite-lhe gerar uma tabela de contador de desempenho que está otimizada para consultas rápidas. Cada contador de desempenho que está definida a **PerformanceCounters** elemento é armazenado na tabela de métricas para além da tabela de contador de desempenho.  

 O **resourceId** atributo é necessário.  O ID de recurso de Máquina Virtual ou o conjunto de dimensionamento de Máquina Virtual que está a implementar o Azure Diagnostics. Obter o **resourceID** do [portal do Azure](https://portal.azure.com). Selecione **procurar** -> **grupos de recursos** -> **< nome\>**. Clique em de **propriedades** mosaico e copie o valor da **ID** campo.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**MetricAggregation**|Atributo necessário:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters de raiz*

 Permite a recolha de contadores de desempenho.  

 Atributo opcional:  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.

|Elemento subordinado|Descrição|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|São necessários os seguintes atributos:<br /><br /> - **counterSpecifier** -o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de contadores de desempenho no seu anfitrião, execute o comando `typeperf`.<br /><br /> - **sampleRate** -com que frequência o contador deve ser objeto de amostragem.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|  




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog de raiz*
 
 Permite a recolha de registos de eventos do Windows.  

 Opcional **scheduledTransferPeriod** atributo. Ver a explicação anteriormente.  

|Elemento subordinado|Descrição|  
|-------------------|-----------------|  
|**Origem de dados**|Os registos de eventos do Windows para recolher. Atributo necessário:<br /><br /> **nome** - a consulta XPath que descrevem os eventos do windows a serem recolhidos. Por exemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para recolher todos os eventos, especifique "*"|  




## <a name="logs-element"></a>Elemento de registos  
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - registos de raiz*

 Está presente na versão 1.0 e 1.1. Em falta no 1.2. 1.3 adicionado novamente.  

 Define a configuração de memória intermédia para os registos do Azure básicas.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilterr**|**cadeia**|Opcional. Especifica o nível de gravidade mínimo para as entradas de registo que são transferidos. O valor predefinido é **Undefined**, que transfere todos os registos. Outros valores possíveis (por ordem de mais informações, pelo menos,) são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|**duração**|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  
|**sinks** adicionado no 1.5|**cadeia**|Opcional. Aponta para uma localização de receptores também enviar dados de diagnóstico. Por exemplo, Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources de raiz*

 Adicionar no 1.9.

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Estatísticas**|Indica o sistema para recolher estatísticas para contentores de Docker|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 *Árvore: Raiz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Uma lista de localizações para enviar dados de diagnóstico e de configuração associados a essas localizações.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Sink**|Ver descrição noutro local nesta página.|  

## <a name="sink-element"></a>Sink do elemento
 *Árvore: - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink de raiz*

 Adicionados na versão 1.5.  

 Define as localizações para enviar dados de diagnóstico para. Por exemplo, o serviço de Application Insights.  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**nome**|Cadeia|Uma cadeia que identifica o sinkname.|  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Application Insights**|Cadeia|Utilizado apenas quando enviar dados para o Application Insights. Contém a chave de instrumentação para uma conta ativa do Application Insights que tenha acesso.|  
|**Canais**|Cadeia|Um para cada adicionais de filtragem de fluxo que|  

## <a name="channels-element"></a>Elemento de canais  
 *Árvore: Canais de SinksConfig - Sink - raiz - DiagnosticsConfiguration - PublicConfig - WadCFG -*

 Adicionados na versão 1.5.  

 Define os filtros para fluxos de passar por um receptor de dados de registo.  

|Elemento|Tipo|Descrição|  
|-------------|----------|-----------------|  
|**Canal**|Cadeia|Ver descrição noutro local nesta página.|  

## <a name="channel-element"></a>Canal de elemento
 *Árvore: Canais de SinksConfig - Sink - raiz - DiagnosticsConfiguration - PublicConfig - WadCFG --canal*

 Adicionados na versão 1.5.  

 Define as localizações para enviar dados de diagnóstico para. Por exemplo, o serviço de Application Insights.  

|Atributos|Tipo|Descrição|  
|----------------|----------|-----------------|  
|**logLevel**|**cadeia**|Especifica o nível de gravidade mínimo para as entradas de registo que são transferidos. O valor predefinido é **Undefined**, que transfere todos os registos. Outros valores possíveis (por ordem de mais informações, pelo menos,) são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**nome**|**cadeia**|Um nome exclusivo do canal para fazer referência aos|  


## <a name="privateconfig-element"></a>Elemento PrivateConfig 
 *Árvore: Raiz - DiagnosticsConfiguration - PrivateConfig*

 Adicionados na versão 1.3.  

 Opcional  

 Armazena os detalhes da conta de armazenamento (nome, a chave e ponto final) privados. Estas informações são enviadas para a máquina virtual, mas não podem ser obtidas a partir do mesmo.  

|Elementos subordinados|Descrição|  
|--------------------|-----------------|  
|**StorageAccount**|A conta de armazenamento a utilizar. Os seguintes atributos são necessários<br /><br /> - **nome** -o nome da conta de armazenamento.<br /><br /> - **chave** -chave para a conta de armazenamento.<br /><br /> - **ponto final** -o ponto final para aceder à conta de armazenamento. <br /><br /> -**sasToken** (adicionado 1.8.1)-pode especificar um token SAS em vez de uma chave de conta de armazenamento na configuração privada. Se for indicado, a chave de conta do storage é ignorada. <br />Requisitos para o Token SAS: <br />-Suporta apenas o token SAS conta <br />- *b*, *t* tipos de serviço são necessários. <br /> - *um*, *c*, *u*, *w* são necessárias permissões. <br /> - *c*, *Nã* tipos de recursos são necessários. <br /> -Suporta o protocolo HTTPS apenas <br /> -Início e a hora de expiração tem de ser válida.|  


## <a name="isenabled-element"></a>Elemento IsEnabled  
 *Árvore: Raiz - DiagnosticsConfiguration - IsEnabled*

 valor booleano. Utilize `true` para ativar o diagnóstico ou `false` para desativar o diagnóstico.
