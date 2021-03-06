---
title: "Def de serviços em nuvem do Azure Esquema de WebRole | Microsoft Docs"
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 85368e4e-a0db-4c02-8dbc-8e2928fa6091
caps.latest.revision: "60"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: b2873f61dcab9a14089949f27f40ca5bedaf14ee
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-cloud-services-definition-webrole-schema"></a>Esquema de WebRole de definição de serviços em nuvem do Azure
A função da web do Azure é uma função personalizada para a programação de aplicações web como suportada pelo IIS 7, tal como ASP.NET, PHP, Windows Communication Foundation e FastCGI.

A extensão de predefinido para o ficheiro de definição de serviço é. csdef.

## <a name="basic-service-definition-schema-for-a-web-role"></a>Esquema de definição de serviço básico de uma função da web  
Segue-se o formato básico de um ficheiro de definição de serviço que contém uma função da web.

```xml
<ServiceDefinition …>  
  <WebRole name="<web-role-name>" vmsize="<web-role-size>" enableNativeCodeExecution="[true|false]">  
    <Certificates>  
      <Certificate name="<certificate-name>" storeLocation="<certificate-store>" storeName="<store-name>" />  
    </Certificates>      
    <ConfigurationSettings>  
      <Setting name="<setting-name>" />  
    </ConfigurationSettings>  
    <Imports>  
      <Import moduleName="<import-module>"/>  
    </Imports>  
    <Endpoints>  
      <InputEndpoint certificate="<certificate-name>" ignoreRoleInstanceStatus="[true|false]" name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<port-number>" port="<port-number>" loadBalancerProbe="<load-balancer-probe-name>" />  
      <InternalEndpoint name="<internal-endpoint-name>" protocol="[http|tcp|udp|any]" port="<port-number>">  
         <FixedPort port="<port-number>"/>  
         <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
      </InternalEndpoint>  
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">  
         <AllocatePublicPortFrom>  
            <FixedPortRange min="<minium-port-number>" max="<maximum-port-number>"/>  
         </AllocatePublicPortFrom>  
      </InstanceInputEndpoint>  
    </Endpoints>  
    <LocalResources>  
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    </LocalResources>  
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />  
    <Runtime executionContext="[limited|elevated]">  
      <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
      </Environment>  
      <EntryPoint>  
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>  
      </EntryPoint>  
    </Runtime>  
    <Sites>  
      <Site name="<web-site-name>">  
        <VirtualApplication name="<application-name>" physicalDirectory="<directory-path>"/>  
        <VirtualDirectory name="<directory-path>" physicalDirectory="<directory-path>"/>  
        <Bindings>  
          <Binding name="<binding-name>" endpointName="<endpoint-name-bound-to>" hostHeader="<url-of-the-site>"/>  
        </Bindings>  
      </Site>  
    </Sites>  
    <Startup priority="<for-internal-use-only>">  
      <Task commandLine="<command-to=execute>" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">  
        <Environment>  
         <Variable name="<variable-name>" value="<variable-value>">  
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>  
          </Variable>            
        </Environment>  
      </Task>  
    </Startup>  
    <Contents>  
      <Content destination="<destination-folder-name>" >  
        <SourceDirectory path="<local-source-directory>" />  
      </Content>  
    </Contents>  
  </WebRole>  
</ServiceDefinition>  
```  

## <a name="schema-elements"></a>Elementos de esquema  
O ficheiro de definição de serviço inclui estes elementos, descritos detalhadamente nas seções subsequentes deste tópico:  

[WebRole](#WebRole)

[ConfigurationSettings](#ConfigurationSettings)

[Definição](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[Pontos finais](#Endpoints)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[Certificados](#Certificates)

[Certificado](#Certificate)

[Importa](#Imports)

[Importar](#Import)

[Tempo de execução](#Runtime)

[Ambiente](#Environment)

[Variável](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[NetFxEntryPoint](#NetFxEntryPoint)

[Sites](#Sites)

[Site](#Site)

[VirtualApplication](#VirtualApplication)

[VirtualApplication](#VirtualApplication)

[Enlaces](#Bindings)

[Enlace](#Binding)

[Arranque](#Startup)

[Tarefa](#Task)

[Conteúdo](#Contents)

[Conteúdo](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="WebRole"></a>WebRole  
O `WebRole` elemento descreve uma função personalizada para a programação de aplicações web, como suportada pelo IIS 7 e ao ASP.NET. Um serviço pode conter zero ou mais funções da web.

A tabela seguinte descreve os atributos do `WebRole` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. O nome para a função da web. Nome da função tem de ser exclusivo.|  
|enableNativeCodeExecution|Valor booleano|Opcional. O valor predefinido é `true`; nativo a execução do código e fidedignidade total são ativadas por predefinição. Defina este atributo como `false` desativar a execução de código nativo para a função da web e, em vez disso, a utilizar fidedignidade parcial do Azure.|  
|vmsize|Cadeia|Opcional. Defina este valor para alterar o tamanho da máquina virtual que está atribuído à função. O valor predefinido é `Small`. Para obter mais informações, consulte [tamanhos de máquinas virtuais para serviços em nuvem](cloud-services-sizes-specs.md).|  

##  <a name="ConfigurationSettings"></a>ConfigurationSettings  
O `ConfigurationSettings` elemento descreve a coleção de definições de configuração para uma função da web. Este elemento é o principal do `Setting` elemento.

##  <a name="Setting"></a>Definição  
O `Setting` elemento descreve um par de nome e valor que especifica uma definição de configuração para uma instância de uma função.

A tabela seguinte descreve os atributos do `Setting` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome exclusivo para a definição de configuração.|  

As definições de configuração para uma função são pares nome / valor que é declarados no ficheiro de definição de serviço e definida no ficheiro de configuração do serviço.

##  <a name="LocalResources"></a>LocalResources  
O `LocalResources` elemento descreve a coleção de recursos de armazenamento local para uma função da web. Este elemento é o principal do `LocalStorage` elemento.

##  <a name="LocalStorage"></a>LocalStorage  
O `LocalStorage` elemento identifique um recurso de armazenamento local que fornece o espaço do sistema de ficheiros para o serviço em tempo de execução. Uma função pode definir zero ou mais recursos de armazenamento local.

> [!NOTE]
>  O `LocalStorage` elemento pode aparecer como um elemento subordinado do `WebRole` elemento para suportar a compatibilidade com versões anteriores do SDK do Azure.

A tabela seguinte descreve os atributos do `LocalStorage` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome exclusivo para o arquivo local.|  
|cleanOnRoleRecycle|Valor booleano|Opcional. Indica se o arquivo local deve ser limpos quando a função é reiniciada. Valor predefinido é `true`.|  
|que sizeInMb|Int|Opcional. A quantidade de espaço de armazenamento para alocar para o arquivo local, em MB pretendida. Se não for especificado, o espaço de armazenamento de predefinido atribuído é 100 MB. A quantidade mínima de espaço de armazenamento que pode ser atribuída é 1 MB.<br /><br /> O tamanho máximo dos recursos locais está dependente de tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos de máquinas virtuais para serviços em nuvem](cloud-services-sizes-specs.md).|  
  
O nome do diretório alocado ao recurso de armazenamento local corresponde ao valor fornecido para o atributo de nome.

##  <a name="Endpoints"></a>Pontos finais  
O `Endpoints` elemento descreve a coleção de entrada (externo) interna e os pontos finais para uma função de entrada de instância. Este elemento é o principal do `InputEndpoint`, `InternalEndpoint`, e `InstanceInputEndpoint` elementos.

Entrada e de pontos finais internos são alocados em separado. Um serviço pode ter um total de entrada 25, interno, e os pontos finais que podem ser atribuídos a funções 25 permitido num serviço de entrada de instância. Por exemplo, se possuir 5 funções pode alocar 5 pontos finais por função ou pode atribuir 25 pontos finais para uma única função ou pode alocar 1 ponto final de entrada cada 25 funções.

> [!NOTE]
>  Cada função implementada requer uma instância por função. A predefinição para uma subscrição de aprovisionamento está limitada a 20 núcleos e, por conseguinte, está limitada a 20 instâncias de uma função. Se a aplicação requer mais instâncias que é fornecido por predefinição Aprovisionamento consulte [suporte de Quota, gestão de subscrições e faturação](https://azure.microsoft.com/support/options/) para obter mais informações sobre como aumentar a quota.

##  <a name="InputEndpoint"></a>InputEndpoint  
O `InputEndpoint` elemento descreve um ponto final externo para uma função da web.

Pode definir vários pontos finais que são uma combinação de HTTP, HTTPS, UDP e os pontos finais TCP. Pode especificar qualquer número de porta que escolher para um ponto final de entrada, mas os números de porta especificados para cada função no serviço tem de ser exclusivos. Por exemplo, se especificar que uma função da web utiliza a porta 80 para HTTP e a porta 443 para HTTPS, poderá, em seguida, especificar que uma função da web segundo utiliza a porta 8080 para HTTP e a porta 8043 para HTTPS.

A tabela seguinte descreve os atributos do `InputEndpoint` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome exclusivo para o ponto final externo.|  
|Protocolo|Cadeia|Necessário. O protocolo de transporte para o ponto final externo. Para uma função da web, os valores possíveis são `HTTP`, `HTTPS`, `UDP`, ou `TCP`.|  
|porta|Int|Necessário. A porta para o ponto final externo. Pode especificar qualquer número de porta que escolher, mas os números de porta especificados para cada função no serviço tem de ser exclusivos.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).|  
|certificado|Cadeia|Necessário para um ponto final de HTTPS. O nome de um certificado definido por um `Certificate` elemento.|  
|localPort|Int|Opcional. Especifica uma porta utilizada para ligações internas no ponto final. O `localPort` atributo mapeia a porta externa no ponto final para uma porta interna numa função. Isto é útil em cenários em que uma função têm de comunicar para um componente interno numa porta que diferente da que é exposto externamente.<br /><br /> Se não for especificado, o valor de `localPort` é igual a `port` atributo. Defina o valor da `localPort` para "*" para atribuir automaticamente uma porta não alocada que é detetável utilizando o API do tempo de execução.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).<br /><br /> O `localPort` atributo só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.|  
|ignoreRoleInstanceStatus|Valor booleano|Opcional. Quando o valor deste atributo é definido como `true`, o estado de um serviço é ignorado e o ponto final não será removido pelo balanceador de carga. Definir este valor como `true` útil para depuração ocupadas instâncias de um serviço. O valor predefinido é `false`. **Nota:** um ponto final pode continuar a receber tráfego, mesmo quando a função não está no estado pronto.|  
|loadBalancerProbe|Cadeia|Opcional. O nome da sonda de Balanceador de carga associado ao ponto final de entrada. Para obter mais informações, consulte [LoadBalancerProbe esquema](schema-csdef-loadbalancerprobe.md).|  

##  <a name="InternalEndpoint"></a>InternalEndpoint  
O `InternalEndpoint` elemento descreve um ponto final interno para uma função da web. Um ponto final interno está disponível apenas para outras instâncias de função em execução no serviço; não está disponível para os clientes fora de serviço. Web funções que inclua o `Sites` elemento só pode ter um único HTTP, UDP ou TCP ponto final interno.

A tabela seguinte descreve os atributos do `InternalEndpoint` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome exclusivo para o ponto final interno.|  
|Protocolo|Cadeia|Necessário. O protocolo de transporte para o ponto final interno. Os valores possíveis são `HTTP`, `TCP`, `UDP`, ou `ANY`.<br /><br /> Um valor de `ANY` Especifica que qualquer protocolo, qualquer porta é permitida.|  
|porta|Int|Opcional. A porta utilizada para ligações com balanceamento de carga interno no ponto final. Ponto final utiliza duas portas com balanceamento de carga. A porta utilizada para o endereço IP público e a porta utilizada no endereço IP privado. Estes são, normalmente, estes são definidos ao mesmo, mas pode optar por utilizar portas diferentes.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).<br /><br /> O `Port` atributo só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.|  

##  <a name="InstanceInputEndpoint"></a>InstanceInputEndpoint  
O `InstanceInputEndpoint` elemento descreve um instância ponto final de entrada para uma função da web. Um ponto de final de entrada de instância está associado uma instância de função específico utilizando o reencaminhamento de porta no balanceador de carga. Cada ponto de final de entrada de instância está mapeado para uma porta específica de um intervalo de portas possíveis. Este elemento é o principal do `AllocatePublicPortFrom` elemento.

O `InstanceInputEndpoint` elemento só está disponível a utilizar a versão do Azure SDK 1.7 ou superior.

A tabela seguinte descreve os atributos do `InstanceInputEndpoint` elemento.
  
| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome exclusivo para o ponto final.|  
|localPort|Int|Necessário. Especifica a porta interna que todas as instâncias de função escutará para receber o tráfego de entrada reencaminhado do Balanceador de carga. Intervalo de valores possíveis entre 1 e 65535, inclusive.|  
|Protocolo|Cadeia|Necessário. O protocolo de transporte para o ponto final interno. Os valores possíveis são `udp` ou `tcp`. Utilize `tcp` para http/https com base no tráfego.|  
  
##  <a name="AllocatePublicPortFrom"></a>AllocatePublicPortFrom  
O `AllocatePublicPortFrom` elemento descreve o intervalo de portas pública que pode ser utilizado por clientes externos acedam a cada ponto de final de entrada de instância. O número da porta público (VIP) é atribuído a partir deste intervalo e atribuído a cada ponto final de instância de função individual durante a implementação de inquilino e a atualização. Este elemento é o principal do `FixedPortRange` elemento.

O `AllocatePublicPortFrom` elemento só está disponível a utilizar a versão do Azure SDK 1.7 ou superior.

##  <a name="FixedPort"></a>FixedPort  
O `FixedPort` elemento Especifica a porta para o ponto final interno, que permite carregar equilibradas ligações no ponto final.

O `FixedPort` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPort` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|porta|Int|Necessário. A porta para o ponto final interno. Isto tem o mesmo efeito que a definição de `FixedPortRange` min e max à mesma porta.<br /><br /> Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).|  

##  <a name="FixedPortRange"></a>FixedPortRange  
O `FixedPortRange` elemento Especifica o intervalo de portas que são atribuídos para o ponto final interno ou o ponto final de entrada de instância e a porta utilizada para a carga de conjuntos com balanceamento de ligações no ponto final.

> [!NOTE]
>  O `FixedPortRange` elemento funciona de forma diferente consoante o elemento em que reside. Quando o `FixedPortRange` elemento está no `InternalEndpoint` elemento, abre todas as portas no balanceador de carga dentro do intervalo dos atributos min e max todas as máquinas virtuais no qual a função é executada. Quando o `FixedPortRange` elemento está no `InstanceInputEndpoint` elemento, abre-se apenas uma porta dentro do intervalo dos atributos em cada máquina virtual a executar a função min e max.

O `FixedPortRange` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `FixedPortRange` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|min.|Int|Necessário. A porta mínimo no intervalo. Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).|  
|Máx.|Cadeia|Necessário. A porta máxima no intervalo. Intervalo de valores possíveis entre 1 e 65535, inclusive (Azure SDK versão 1.7 ou superior).|  

##  <a name="Certificates"></a>Certificados  
O `Certificates` elemento descreve a coleção de certificados para uma função da web. Este elemento é o principal do `Certificate` elemento. Uma função pode ter qualquer número de certificados associados. Para obter mais informações sobre como utilizar o elemento de certificados, consulte [modificar o ficheiro de definição de serviço com um certificado](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files).

##  <a name="Certificate"></a>Certificado  
O `Certificate` elemento descreve um certificado que está associado uma função da web.

A tabela seguinte descreve os atributos do `Certificate` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Um nome para este certificado, o que é utilizado para fazer referência ao mesmo quando está associada a um HTTPS `InputEndpoint` elemento.|  
|storeLocation|Cadeia|Necessário. A localização do arquivo de certificados em que este certificado pode ser encontrado no computador local. Os valores possíveis são `CurrentUser` e `LocalMachine`.|  
|storeName|Cadeia|Necessário. O nome do arquivo de certificados em que este certificado reside no computador local. Os valores possíveis incluem os nomes de arquivo incorporada `My`, `Root`, `CA`, `Trust`, `Disallowed`, `TrustedPeople`, `TrustedPublisher`, `AuthRoot`, `AddressBook`, ou qualquer nome de arquivo personalizado. Se não for especificado um nome de arquivo personalizado, o arquivo é criado automaticamente.|  
|permissionLevel|Cadeia|Opcional. Especifica as permissões de acesso para os processos de função. Se pretender que processos elevados apenas para poder aceder à chave privada, em seguida, especifique `elevated` permissão. `limitedOrElevated`permissão permite que todos os processos de função aceder à chave privada. Os valores possíveis são `limitedOrElevated` ou `elevated`. O valor predefinido é `limitedOrElevated`.|  

##  <a name="Imports"></a>Importa  
O `Imports` elemento descreve uma coleção de módulos de importação para uma função da web que adicionar componentes para o sistema operativo convidado. Este elemento é o principal do `Import` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de importações. 

O `Imports` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

##  <a name="Import"></a>Importar  
O `Import` elemento Especifica um módulo para adicionar ao sistema operativo convidado.

O `Import` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Import` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|moduleName|Cadeia|Necessário. O nome do módulo para importar. Módulos de importação válidos são:<br /><br /> -RemoteAccess<br />-RemoteForwarder<br />-Diagnóstico<br /><br /> Os módulos RemoteAccess e RemoteForwarder permitem-lhe configurar a sua instância de função para ligações de ambiente de trabalho remotas. Para obter mais informações consulte [ativar a ligação ao ambiente de trabalho remoto](cloud-services-role-enable-remote-desktop-new-portal.md).<br /><br /> O módulo de diagnóstico permite-lhe recolher dados de diagnóstico para uma instância de função.|  

##  <a name="Runtime"></a>Tempo de execução  
O `Runtime` elemento descreve uma coleção de variáveis definições de ambiente para uma função da web que controlam o ambiente de tempo de execução do processo de anfitrião do Azure. Este elemento é o principal do `Environment` elemento. Este elemento é opcional e uma função pode ter apenas um bloco de tempo de execução.

O `Runtime` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Runtime` elemento:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|executionContext|Cadeia|Opcional. Especifica o contexto em que o processo de função é iniciado. O contexto predefinido é `limited`.<br /><br /> -   `limited`– O processo é iniciado sem privilégios de administrador.<br />-   `elevated`– O processo é iniciado com privilégios de administrador.|  

##  <a name="Environment"></a>Ambiente  
O `Environment` elemento descreve uma coleção de definições de variáveis de ambiente para uma função da web. Este elemento é o principal do `Variable` elemento. Uma função pode ter qualquer número de variáveis de ambiente definidas.

##  <a name="Variable"></a>Variável  
O `Variable` elemento Especifica uma variável de ambiente definir de operativo convidado.

O `Variable` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Variable` elemento:  

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. O nome da variável de ambiente para definir.|  
|valor|Cadeia|Opcional. O valor definido para a variável de ambiente. Tem de incluir um atributo de valor ou um `RoleInstanceValue` elemento.|  

##  <a name="RoleInstanceValue"></a>RoleInstanceValue  
O `RoleInstanceValue` elemento Especifica o xPath para obter o valor da variável.

A tabela seguinte descreve os atributos do `RoleInstanceValue` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|XPath|Cadeia|Opcional. Caminho da localização das definições de implementação para a instância. Para obter mais informações, consulte [variáveis de configuração com XPath](cloud-services-role-config-xpath.md).<br /><br /> Tem de incluir um atributo de valor ou um `RoleInstanceValue` elemento.|  

##  <a name="EntryPoint"></a>Ponto de entrada  
O `EntryPoint` elemento Especifica o ponto de entrada para uma função. Este elemento é o principal do `NetFxEntryPoint` elementos. Estes elementos permitem-lhe especificar uma aplicação diferente da predefinição WaWorkerHost.exe para agir como o ponto de entrada da função.

O `EntryPoint` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou posterior.

##  <a name="NetFxEntryPoint"></a>NetFxEntryPoint  
O `NetFxEntryPoint` elemento Especifica o programa a executar para uma função.

> [!NOTE]
>  O `NetFxEntryPoint` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou posterior.

A tabela seguinte descreve os atributos do `NetFxEntryPoint` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|AssemblyName|Cadeia|Necessário. O nome de ficheiro e caminho da assemblagem que contém o ponto de entrada. O caminho é relativo à pasta de  **\\%ROLEROOT%\Approot** (não especificar  **\\%ROLEROOT%\Approot** no `commandLine`, presume-se). **% ROLEROOT %** é uma variável de ambiente é mantida através do Azure e representa a localização da pasta raiz para a sua função. O  **\\%ROLEROOT%\Approot** pasta representa a pasta de aplicação para sua função.<br /><br /> Para funções HWC o caminho é sempre relativa a  **\\%ROLEROOT%\Approot\bin** pasta.<br /><br /> Para IIS completo e IIS Express web funções, se a assemblagem não é possível localizar relativa  **\\%ROLEROOT%\Approot** pasta, o  **\\%ROLEROOT%\Approot\bin** é procurado.<br /><br /> Este Outono novamente o comportamento do IIS completo não é uma melhor prática de Recomendamos e talvez removidas em futuras versões.|  
|targetFrameworkVersion|Cadeia|Necessário. A versão do .NET framework em que a assemblagem foi compilada. Por exemplo, `targetFrameworkVersion="v4.0"`.|  

##  <a name="Sites"></a>Sites  
O `Sites` elemento descreve uma coleção de sites e aplicações web que estão alojados numa função da web. Este elemento é o principal do `Site` elemento. Se não especificar um `Sites` elemento, a função da web está alojada como função web legado e só pode ter de um Web site alojado na sua função web. Este elemento é opcional e uma função pode ter apenas um bloco de sites.

O `Sites` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

##  <a name="Site"></a>Site  
O `Site` elemento Especifica uma Web site ou aplicação web que faz parte da função da web.

O `Site` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Site` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Nome do Web site ou aplicação.|  
|physicalDirectory|Cadeia|A localização do diretório de conteúdo para a raiz do site. A localização pode ser especificada como um caminho absoluto ou relativo à localização. csdef.|  

##  <a name="VirtualApplication"></a>VirtualApplication  
O `VirtualApplication` elemento define uma aplicação nos serviços de informações Internet (IIS) 7 é um agrupamento de ficheiros que oferece conteúdo ou fornece serviços através de protocolos, tal como HTTP. Quando cria uma aplicação no IIS 7, o caminho da aplicação passa a fazer parte do URL do site.

O `VirtualApplication` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `VirtualApplication` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Especifica um nome para identificar a aplicação virtual.|  
|physicalDirectory|Cadeia|Necessário. Especifica o caminho na máquina de desenvolvimento que contém a aplicação virtual. O emulador de computação, o IIS está configurado para obter conteúdo a partir desta localização. Quando implementar o Azure, o conteúdo do diretório físico é reunido juntamente com o resto do serviço. Quando o pacote de serviço é implementado no Azure, o IIS está configurado com a localização do conteúdo descompactada.|  

##  <a name="VirtualDirectory"></a>VirtualDirectory  
O `VirtualDirectory` elemento Especifica um nome de diretório (também referido como caminho) que especificar no IIS e mapear para um diretório físico num servidor local ou remoto.

O `VirtualDirectory` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `VirtualDirectory` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Especifica um nome para identificar o diretório virtual.|  
|valor|physicalDirectory|Necessário. Especifica o caminho na máquina de desenvolvimento que contém o Web site ou o conteúdo do diretório Virtual. O emulador de computação, o IIS está configurado para obter conteúdo a partir desta localização. Quando implementar o Azure, o conteúdo do diretório físico é reunido juntamente com o resto do serviço. Quando o pacote de serviço é implementado no Azure, o IIS está configurado com a localização do conteúdo descompactada.|  

##  <a name="Bindings"></a>Enlaces  
O `Bindings` elemento descreve um conjunto de enlaces para um Web site. É o elemento principal de `Binding` elemento. O elemento não é necessário para cada `Site` elemento. Para obter mais informações sobre como configurar pontos finais, consulte [ativar comunicação para instâncias de função](cloud-services-enable-communication-role-instances.md).

O `Bindings` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

##  <a name="Binding"></a>Enlace  
O `Binding` elemento Especifica as informações de configuração necessárias para os pedidos para comunicar com uma Web site ou aplicação web.

O `Binding` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|nome|Cadeia|Necessário. Especifica um nome para identificar o enlace.|  
|EndpointName|Cadeia|Necessário. Especifica o nome de ponto final à qual vincular.|  
|hostHeader|Cadeia|Opcional. Especifica um nome de anfitrião que lhe permite alojar vários sites, com nomes de anfitrião diferente, numa combinação de número único no endereço IP/porta.|  

##  <a name="Startup"></a>Arranque  
O `Startup` elemento descreve uma coleção de tarefas que são executados quando é iniciada a função. Este elemento pode ser o principal do `Variable` elemento. Para obter mais informações sobre como utilizar as tarefas de arranque de função, consulte [como configurar tarefas de arranque](cloud-services-startup-tasks.md). Este elemento é opcional e uma função pode ter apenas um bloco de arranque.

A tabela seguinte descreve o atributo do `Startup` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|prioridade|Int|Apenas para utilização interna.|  

##  <a name="Task"></a>Tarefa  
O `Task` elemento Especifica as tarefas de arranque que ocorre quando a função é iniciado. Tarefas de arranque podem ser utilizadas para efetuar tarefas preparar a função de executar essa instalação componentes de software ou executar outras aplicações. Tarefas são executadas pela ordem em que aparecem dentro de `Startup` blocos de elemento.

O `Task` elemento só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

A tabela seguinte descreve os atributos do `Task` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|linha de comando|Cadeia|Necessário. Um script, tal como um ficheiro CMD, que contém os comandos para executar. Ficheiros de comando e o lote de arranque tem de ser guardados num formato de ANSI. Formatos de ficheiro que definir um marcador de ordem de bytes no início do ficheiro não irão processar corretamente.|  
|executionContext|Cadeia|Especifica o contexto em que o script é executado.<br /><br /> -   `limited`[Predefinição] – execute com os mesmos privilégios como a função que aloja o processo.<br />-   `elevated`– Executado com privilégios de administrador.|  
|taskType|Cadeia|Especifica o comportamento de execução do comando.<br /><br /> -   `simple`[Predefinição-] sistema aguarda que a tarefa sair antes de quaisquer outras tarefas são iniciadas.<br />-   `background`– Sistema não aguardar a tarefa sair.<br />-   `foreground`– Semelhante em segundo plano, exceto a função não for reiniciada, até que todas as tarefas de primeiro plano sair.|  

##  <a name="Contents"></a>Conteúdo  
O `Contents` elemento descreve a coleção de conteúdo para uma função da web. Este elemento é o principal do `Content` elemento.

O `Contents` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou posterior.

##  <a name="Content"></a>Conteúdo  
O `Content` elemento define a localização de origem do conteúdo seja copiado para a máquina virtual do Azure e o caminho de destino para o qual é copiado.

O `Content` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou posterior.

A tabela seguinte descreve os atributos do `Content` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|Destino|Cadeia|Necessário. Localização na máquina virtual do Azure para o qual o conteúdo é colocado. Esta localização é relativo à pasta de **%ROLEROOT%\Approot**.|  

Este elemento é o elemento principal de `SourceDirectory` elemento.

##  <a name="SourceDirectory"></a>SourceDirectory  
O `SourceDirectory` elemento define o diretório local a partir do qual o conteúdo é copiado. Utilize este elemento para especificar o conteúdo local para copiar para a máquina virtual do Azure.

O `SourceDirectory` elemento só está disponível a utilizar a versão do SDK do Azure 1.5 ou posterior.

A tabela seguinte descreve os atributos do `SourceDirectory` elemento.

| Atributo | Tipo | Descrição |  
| --------- | ---- | ----------- |  
|Caminho|Cadeia|Necessário. Caminho relativo ou absoluto de um diretório local cujo conteúdo será copiado para a máquina virtual do Azure. É suportada a expansão de variáveis de ambiente no caminho do diretório.|  
  
## <a name="see-also"></a>Veja Também
[Esquema de definição (clássica) serviço em nuvem](schema-csdef-file.md)
