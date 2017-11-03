---
title: "Esquema de configuração de diagnóstico do Azure 1.0 | Microsoft Docs"
description: "APENAS relevante se estiver a utilizar o Azure SDK 2.4 e abaixo com Virtual Machines do Azure, conjuntos de dimensionamento de Máquina Virtual, o Service Fabric ou serviços em nuvem."
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
ms.openlocfilehash: a8fdfb52d5091d3fc9779657737c7430fcfada51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Esquema de configuração de diagnóstico do Azure 1.0
> [!NOTE]
> Diagnóstico do Azure é o componente utilizado para recolher os contadores de desempenho e outras estatísticas de Virtual Machines do Azure, conjuntos de dimensionamento de Máquina Virtual, Service Fabric e serviços em nuvem.  Esta página só é relevante se estiver a utilizar um destes serviços.
>

Diagnóstico do Azure é utilizado com outros produtos de diagnóstico do Microsoft Azure Monitor, Application Insights e análise de registos.

O ficheiro de configuração de diagnósticos do Azure define valores que são utilizados para inicializar o Monitor de diagnóstico. Este ficheiro é utilizado para inicializar as definições de diagnóstico de configuração quando o monitor de diagnóstico é iniciado.  

 Por predefinição, o ficheiro de esquema de configuração de diagnósticos do Azure está instalado para a `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` diretório. Substitua `<version>` com a versão instalada do [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  O ficheiro de configuração de diagnóstico é normalmente utilizado com as tarefas de arranque que necessitam de dados de diagnóstico a serem recolhidos anteriormente no processo de arranque. Para obter mais informações sobre como utilizar o diagnóstico do Azure, consulte [recolher dados de registo por utilizando o Azure Diagnostics](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Exemplo de ficheiro de configuração de diagnóstico  
 O exemplo seguinte mostra um ficheiro de configuração típica de diagnóstico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Espaço de nomes de DiagnosticsConfiguration  
 O espaço de nomes XML para o ficheiro de configuração de diagnóstico é:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementos de esquema  
 O ficheiro de configuração de diagnósticos inclui os seguintes elementos.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration  
O elemento de nível superior do ficheiro de configuração de diagnóstico.  

Atributos:

|Atributo  |Tipo   |Necessário| Predefinição | Descrição|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|Duração|Opcional | PT1M| Especifica o intervalo no qual o monitor de diagnóstico consulta alterações de configuração de diagnóstico.|  
|**overallQuotaInMB**|unsignedInt|Opcional| 4000 MB. Se fornecer um valor, não pode exceder este valor |A quantidade total de armazenamento do sistema de ficheiros atribuída a todas as memórias intermédias de registo.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento DiagnosticInfrastructureLogs  
Define a configuração de memória intermédia para os registos que são gerados pela infraestrutura subjacente do diagnóstico.

Elemento principal: [DiagnosticMonitorConfiguration elemento](#DiagnosticMonitorConfiguration).  

Atributos:

|Atributo|Tipo|Descrição|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|Cadeia|Opcional. Especifica o nível de gravidade mínimo para as entradas de registo que são transferidos. O valor predefinido é **Undefined**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="logs-element"></a>Elemento de registos  
 Define a configuração de memória intermédia para os registos do Azure básicas.

 Elemento principal: [DiagnosticMonitorConfiguration elemento](#DiagnosticMonitorConfiguration).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|Cadeia|Opcional. Especifica o nível de gravidade mínimo para as entradas de registo que são transferidos. O valor predefinido é **Undefined**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="directories-element"></a>Elemento de diretórios  
Define a configuração de memória intermédia para os registos baseados em ficheiros, que pode definir.

Elemento principal: [DiagnosticMonitorConfiguration elemento](#DiagnosticMonitorConfiguration).  


Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Define o diretório de informações de estado de falha.

 Elemento principal: [diretórios elemento](#Directories).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contentor**|Cadeia|O nome do contentor onde o conteúdo do diretório está a ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="failedrequestlogs-element"></a>Elemento FailedRequestLogs  
 Define o diretório de registo de pedidos falhados.

 Principal elemento [diretórios elemento](#Directories).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contentor**|Cadeia|O nome do contentor onde o conteúdo do diretório está a ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

##  <a name="iislogs-element"></a>Elemento IISLogs  
 Define o diretório de registo do IIS.

 Principal elemento [diretórios elemento](#Directories).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contentor**|Cadeia|O nome do contentor onde o conteúdo do diretório está a ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="datasources-element"></a>Elemento de origens de dados  
 Define zero ou mais diretórios de registo adicionais.

 Elemento principal: [diretórios elemento](#Directories).

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Define o diretório de ficheiros de registo para monitorizar.

 Elemento principal: [elemento de origens de dados](#DataSources).

Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**contentor**|Cadeia|O nome do contentor onde o conteúdo do diretório está a ser transferido.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica o tamanho máximo do diretório em megabytes.<br /><br /> A predefinição é 0.|  

## <a name="absolute-element"></a>Elemento absoluto  
 Define um caminho absoluto do diretório para monitorizar com a expansão de ambiente opcionais.

 Elemento principal: [DirectoryConfiguration elemento](#DirectoryConfiguration).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**caminho**|Cadeia|Necessário. O caminho absoluto para o diretório a monitorizar.|  
|**expandEnvironment**|Valor booleano|Necessário. Se definido como **verdadeiro**, variáveis de ambiente no caminho são expandidas.|  

## <a name="localresource-element"></a>Elemento LocalResource  
 Define um caminho relativo para um recurso local definido na definição de serviço.

 Elemento principal: [DirectoryConfiguration elemento](#DirectoryConfiguration).  

Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**nome**|Cadeia|Necessário. O nome do recurso local que contém o diretório para monitorizar.|  
|**relativePath**|Cadeia|Necessário. O caminho relativo para o recurso local para monitorizar.|  

## <a name="performancecounters-element"></a>PerformanceCounters elemento  
 Define o caminho para o contador de desempenho para recolher.

 Elemento principal: [DiagnosticMonitorConfiguration elemento](#DiagnosticMonitorConfiguration).


 Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 Define o contador de desempenho para recolher.

 Elemento principal: [PerformanceCounters elemento](#PerformanceCounters).  

 Atributos:  

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**counterSpecifier**|Cadeia|Necessário. O caminho para o contador de desempenho para recolher.|  
|**sampleRate**|Duração|Necessário. Taxa à qual o contador de desempenho deve ser recolhido.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Define os registos de eventos a monitorizar.

 Elemento principal: [DiagnosticMonitorConfiguration elemento](#DiagnosticMonitorConfiguration).

  Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica a quantidade máxima de armazenamento do sistema de ficheiros que está disponível para os dados especificados.<br /><br /> A predefinição é 0.|  
|**scheduledTransferLogLevelFilter**|Cadeia|Opcional. Especifica o nível de gravidade mínimo para as entradas de registo que são transferidos. O valor predefinido é **Undefined**. Outros valores possíveis são **verboso**, **informações**, **aviso**, **erro**, e **crítico**.|  
|**scheduledTransferPeriod**|Duração|Opcional. Especifica o intervalo entre agendada transferências de dados, arredondados para o minuto mais próximo.<br /><br /> A predefinição é PT0S.|  

## <a name="datasource-element"></a>Elemento de origem de dados  
 Define o registo de eventos a monitorizar.

 Elemento principal: [WindowsEventLog elemento](#windowsEventLog).  

 Atributos:

|Atributo|Tipo|Descrição|  
|---------------|----------|-----------------|  
|**nome**|Cadeia|Necessário. Uma expressão de XPath especificando o registo a recolher.|  
