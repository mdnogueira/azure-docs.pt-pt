---
title: "Esquema de configuração de diagnóstico do Azure 1.2 | Microsoft Docs"
description: "APENAS relevante se estiver a utilizar o Azure SDK 2.5 com Virtual Machines do Azure, conjuntos de dimensionamento de Máquina Virtual, Service Fabric ou serviços em nuvem."
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
ms.openlocfilehash: 1e9cc6d0950945df8c4fba74d8e1f6196be224f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Esquema de configuração 1.2 de diagnóstico do Azure
> [!NOTE]
> Diagnóstico do Azure é o componente utilizado para recolher os contadores de desempenho e outras estatísticas de Virtual Machines do Azure, conjuntos de dimensionamento de Máquina Virtual, Service Fabric e serviços em nuvem.  Esta página só é relevante se estiver a utilizar um destes serviços.
>

Diagnóstico do Azure é utilizado com outros produtos de diagnóstico do Microsoft Azure Monitor, Application Insights e análise de registos.

Este esquema define os valores possíveis que pode utilizar para inicializar as definições de diagnóstico de configuração quando o monitor de diagnóstico é iniciado.  


 Transfira a definição de esquema do ficheiro de configuração pública executando o seguinte comando do PowerShell:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [ativar o diagnóstico na Cloud Services do Azure](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo de ficheiro de configuração de diagnóstico  
 O exemplo seguinte mostra um ficheiro de configuração típica de diagnóstico:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Espaço de nomes de configuração de diagnóstico  
 O espaço de nomes XML para o ficheiro de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 Elemento de nível superior do ficheiro de configuração de diagnóstico. A tabela seguinte descreve os elementos do ficheiro de configuração.  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**WadCfg**|Necessário. Definições de configuração para os dados de telemetria a ser recolhido.|  
|**StorageAccount**|O nome da conta do Storage do Azure para armazenar os dados. Isto também pode ser especificado como um parâmetro ao executar o cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|O diretório na máquina virtual a ser utilizado pelo agente de monitorização para armazenar dados de eventos. Se não for definido, o diretório predefinido é utilizado:<br /><br /> Para uma função de trabalho/web:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para uma Máquina Virtual:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Os atributos necessários são:<br /><br /> -                      **caminho** -o diretório no sistema para ser utilizado por diagnósticos do Azure.<br /><br /> -                      **expandEnvironment** -controla se as variáveis de ambiente são expandidas no nome do caminho.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
Define as definições de configuração para os dados de telemetria a ser recolhido. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Necessário. Atributos opcionais quantos são:<br /><br /> -                     **overallQuotaInMB** -a quantidade máxima de espaço no disco local que pode ser utilizada por vários tipos de dados de diagnóstico recolhidos pelo diagnósticos do Azure. O valor predefinido é 5120MB.<br /><br /> -                     **useProxyServer** -configurar diagnósticos do Azure para utilizar as definições do servidor proxy conforme definido nas definições de i/e.|  
|**CrashDumps**|Ative a recolha de informações de falhas. Atributos opcionais quantos são:<br /><br /> -                     **containerName** -o nome do contentor do blob na sua conta do Storage do Azure a ser utilizado para armazenar informações de falhas.<br /><br /> -                     **crashDumpType** -configura diagnósticos do Azure para falhas de Mini ou completo de recolher informações de estado.<br /><br /> -                     **directoryQuotaPercentage**-configura a percentagem de **overallQuotaInMB** seja reservado para informações de falhas na VM.|  
|**DiagnosticInfrastructureLogs**|Ative a recolha dos registos gerados por diagnósticos do Azure. Os registos de diagnóstico de infraestrutura são úteis para resolução de problemas do sistema de diagnóstico. Atributos opcionais quantos são:<br /><br /> -                     **scheduledTransferLogLevelFilter** -configura o nível de gravidade mínima dos registos recolhidos.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**Diretórios**|Permite a recolha do conteúdo de um diretório, registos de IIS não conseguiu de pedidos de acesso e/ou os registos IIS. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração."](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**EtwProviders**|Configura a recolha de eventos do ETW de EventSource de e/ou o manifesto do ETW baseado em fornecedores.|  
|**Métricas**|Este elemento permite-lhe gerar uma tabela de contador de desempenho que está otimizada para consultas rápidas. Cada contador de desempenho que está definida a **PerformanceCounters** elemento é armazenado na tabela de métricas para além da tabela de contador de desempenho. Atributo necessário:<br /><br /> **resourceId** -este é o ID de recurso da Máquina Virtual está a implementar o Azure Diagnostics. Obter o **resourceID** do [portal do Azure](https://portal.azure.com). Selecione **procurar** -> **grupos de recursos** -> **< nome\>**. Clique em de **propriedades** mosaico e copie o valor da **ID** campo.|  
|**PerformanceCounters**|Permite a recolha de contadores de desempenho. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Permite a recolha de registos de eventos do Windows. Atributo opcional:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [XML "Tipo de dados de duração".](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Permite a recolha de informações de falhas. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Necessário. Atributo necessário:<br /><br /> **processName** -o nome do processo de que pretende que o diagnóstico do Azure para recolher as informações de falhas para.|  
|**crashDumpType**|Configura o diagnóstico do Azure para recolher informações de mini ou falhas completa.|  
|**directoryQuotaPercentage**|Configura a percentagem de **overallQuotaInMB** seja reservado para informações de falhas na VM.|  

## <a name="directories-element"></a>Elemento de diretórios  
 Permite a recolha do conteúdo de um diretório, registos de IIS não conseguiu de pedidos de acesso e/ou os registos IIS. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Origens de dados**|Uma lista de diretórios a monitorizar.|  
|**FailedRequestLogs**|Incluindo este elemento na configuração permite a recolha de registos sobre pedidos com falhas para uma aplicação ou site do IIS. Tem também de ativar as opções de rastreio em **sistema. Servidor Web** no **Web. config**.|  
|**IISLogs**|Incluindo este elemento na configuração permite a recolha de registos do IIS:<br /><br /> **containerName** -o nome do contentor do blob na sua conta do Storage do Azure a ser utilizado para armazenar os registos IIS.|  

## <a name="datasources-element"></a>Elemento de origens de dados  
 Uma lista de diretórios a monitorizar. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Necessário. Atributo necessário:<br /><br /> **containerName** -o nome do contentor do blob na sua conta do Storage do Azure a ser utilizado para armazenar os ficheiros de registo.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 **DirectoryConfiguration** pode incluir um o **absoluto** ou **LocalResource** elemento mas não ambos. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Absoluto**|O caminho absoluto para o diretório a monitorizar. São necessários os seguintes atributos:<br /><br /> -                     **Caminho** -o caminho absoluto para o diretório a monitorizar.<br /><br /> -                      **expandEnvironment** -configura se as variáveis de ambiente no caminho são expandidas.|  
|**LocalResource**|O caminho relativo para um recurso local para monitorizar. Os atributos necessários são:<br /><br /> -                     **Nome** -o recurso local que contém o diretório para monitorizar<br /><br /> -                     **relativePath** -o caminho relativo ao nome que contém o diretório para monitorizar|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura a recolha de eventos do ETW de EventSource de e/ou o manifesto do ETW baseado em fornecedores. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura a recolha de eventos gerados a partir de [EventSource classe](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necessário:<br /><br /> **fornecedor** -o nome da classe do evento de EventSource.<br /><br /> Atributos opcionais quantos são:<br /><br /> -                     **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta do storage.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [tipo de dados XML duração](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Atributo necessário:<br /><br /> **fornecedor** -o GUID do fornecedor de evento<br /><br /> Atributos opcionais quantos são:<br /><br /> - **scheduledTransferLogLevelFilter** -o nível de gravidade mínimo para transferir a sua conta do storage.<br /><br /> -                     **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [tipo de dados XML duração](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura a recolha de eventos gerados a partir de [EventSource classe](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  
|**Evento**|Atributo necessário:<br /><br /> **ID** -o id do evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination** -o nome da tabela para armazenar eventos|  

## <a name="metrics-element"></a>Elemento de métricas  
 Permite-lhe gerar uma tabela de contador de desempenho que está otimizada para consultas rápidas. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**MetricAggregation**|Atributo necessário:<br /><br /> **scheduledTransferPeriod** -o intervalo entre as transferências agendadas para armazenamento arredondado para o minuto mais próximo. O valor é um [tipo de dados XML duração](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 Permite a recolha de contadores de desempenho. A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|São necessários os seguintes atributos:<br /><br /> -                     **counterSpecifier** -o nome do contador de desempenho. Por exemplo, `\Processor(_Total)\% Processor Time`. Para obter uma lista de desempenho contadores no seu anfitrião execute o comando `typeperf`.<br /><br /> -                     **sampleRate** -com que frequência o contador deve ser objeto de amostragem.<br /><br /> Atributo opcional:<br /><br /> **unidade** -a unidade de medida do contador.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**anotação**|Atributo necessário:<br /><br /> **displayName** -o nome a apresentar para o contador<br /><br /> Atributo opcional:<br /><br /> **região** -a região a utilizar quando se apresenta o nome do contador|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 A tabela seguinte descreve os elementos subordinados:  

|Nome do elemento|Descrição|  
|------------------|-----------------|  
|**Origem de dados**|Os registos de eventos do Windows para recolher. Atributo necessário:<br /><br /> **nome** - a consulta XPath que descrevem os eventos do windows a serem recolhidos. Por exemplo:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Para recolher todos os eventos, especifique "*".|
