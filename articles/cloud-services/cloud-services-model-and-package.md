---
title: "O que é um modelo de serviço em nuvem e o pacote | Microsoft Docs"
description: "Descreve o modelo de serviço de nuvem (. csdef,. cscfg) e o pacote (. cspkg) no Azure"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4ce2feb5-0437-496c-98da-1fb6dcb7f59e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: b7210c944e2f99aacdc2f554409552007286c5da
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>O que é o modelo de serviço em nuvem e como pacote-lo?
Um serviço em nuvem é criado a partir de três componentes, a definição de serviço *(. csdef)*, a configuração de serviço *(. cscfg)*e um pacote de serviço *(. cspkg)*. Tanto o **servicedefinition. Csdef** e **ServiceConfig.cscfg** ficheiros são baseados em XML e descrevem a estrutura do serviço em nuvem e como está configurada; coletivamente chamado o modelo. O **ServicePackage.cspkg** é um ficheiro zip que é gerado a partir de **servicedefinition. Csdef** e entre outras coisas, contém todos os com base em binário dependências necessárias. Azure cria um serviço em nuvem a partir de ambos os **ServicePackage.cspkg** e **ServiceConfig.cscfg**.

Assim que o serviço em nuvem está em execução no Azure, pode reconfigurá-la através de **ServiceConfig.cscfg** ficheiro, mas não é possível alterar a definição.

## <a name="what-would-you-like-to-know-more-about"></a>O que pretende saber mais sobre?
* Pretender saber mais sobre o [servicedefinition. Csdef](#csdef) e [ServiceConfig.cscfg](#cscfg) ficheiros.
* Já conhece sobre o que, dar-me [alguns exemplos](#next-steps) na qual pode configurar.
* Pretender criar o [ServicePackage.cspkg](#cspkg).
* Estou a utilizar o Visual Studio e pretender...
  * [Criar um serviço em nuvem][vs_create]
  * [Reconfigure o serviço cloud existente][vs_reconfigure]
  * [Implementar um projeto de serviço em nuvem][vs_deploy]
  * [Ambiente de trabalho remoto numa instância de serviço em nuvem][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>Servicedefinition. Csdef
O **servicedefinition. Csdef** ficheiro Especifica as definições que são utilizadas pelo Azure para configurar um serviço em nuvem. O [esquema de definição de serviço do Azure (. csdef ficheiro)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fornece o formato permitido para um ficheiro de definição de serviço. O exemplo seguinte mostra as definições que podem ser definidas para as funções da Web e de trabalho:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Pode consultar o [esquema de definição de serviço](https://msdn.microsoft.com/library/azure/ee758711.aspx) para uma melhor compreensão do esquema XML utilizado aqui, no entanto, eis uma explicação mais rápida de alguns dos elementos:

**Sites**  
Contém as definições para aplicações web ou de Web sites que estão alojadas em IIS7.

**InputEndpoints**  
Contém as definições para os pontos finais que são utilizadas para contactar o serviço em nuvem.

**InternalEndpoints**  
Contém as definições para pontos finais que são utilizados por instâncias de função para comunicar entre si.

**ConfigurationSettings**  
Contém as definições de configuração de funcionalidades de uma função específica.

**Certificados**  
Contém as definições para certificados que são necessários para uma função. Exemplo de código anterior mostra um certificado que é utilizado para a configuração do Azure Connect.

**LocalResources**  
Contém as definições para os recursos de armazenamento local. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual em que está em execução uma instância de uma função.

**Importa**  
Contém as definições de módulos importados. Exemplo de código anterior mostra os módulos para ligação ao ambiente de trabalho remoto e ligar do Azure.

**Arranque**  
Contém tarefas que são executadas quando a função é iniciado. As tarefas são definidas num ficheiro executável ou. cmd.

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>Serviceconfiguration. Cscfg
A configuração das definições do serviço em nuvem é determinada pelos valores no **serviceconfiguration. Cscfg** ficheiro. Especifique o número de instâncias que pretende implementar para cada função neste ficheiro. Os valores para as definições de configuração definida no ficheiro de definição de serviço são adicionados ao ficheiro de configuração de serviço. Os thumbprints para quaisquer certificados de gestão que estão associados com o serviço em nuvem também são adicionados ao ficheiro. O [esquema de configuração de serviço do Azure (. cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fornece o formato permitido para um ficheiro de configuração do serviço.

O ficheiro de configuração de serviço não é compactado com a aplicação, mas é carregado para o Azure como um ficheiro separado e é utilizado para configurar o serviço em nuvem. Pode carregar um novo ficheiro de configuração de serviço sem voltar a implementar o serviço em nuvem. Os valores de configuração para o serviço em nuvem podem ser alterados enquanto o serviço em nuvem está em execução. O exemplo seguinte mostra as definições de configuração que podem ser definidas para as funções da Web e de trabalho:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Pode consultar o [esquema de configuração do serviço](https://msdn.microsoft.com/library/azure/ee758710.aspx) para melhor compreender o esquema XML utilizado aqui, no entanto, eis uma explicação mais rápida dos elementos:

**Instâncias**  
Configura o número de instâncias para a função em execução. Para impedir que o seu serviço em nuvem potencialmente se tornar indisponível durante as atualizações, é recomendado que implementar mais do que uma instância do seu funções orientado para o web. Ao implementar mais do que uma instância, está a cumprir os as diretrizes no [Azure computação nível contrato serviço (SLA)](http://azure.microsoft.com/support/legal/sla/), que garante 99,95% conectividade externa para funções de acesso à Internet quando duas ou mais instâncias de função são implementadas para um serviço.

**ConfigurationSettings**  
Configura as definições para as instâncias em execução para uma função. O nome do `<Setting>` elementos têm de corresponder às definições de configuração no ficheiro de definição de serviço.

**Certificados**  
Configura os certificados que são utilizados pelo serviço. Exemplo de código anterior mostra como definir o certificado para o módulo de acesso remoto. O valor da *thumbprint* tem de ser definido para o thumbprint do certificado a utilizar.

<p/>

> [!NOTE]
> O thumbprint do certificado pode ser adicionado ao ficheiro de configuração utilizando um editor de texto. Em alternativa, é possível adicionar o valor no **certificados** separador do **propriedades** página de função no Visual Studio.
> 
> 

## <a name="defining-ports-for-role-instances"></a>Definir portas para instâncias de função
Azure permite que o ponto de entrada apenas um para uma função da web. O que significa que todo o tráfego ocorre através de um endereço IP. Pode configurar os sites partilhem uma porta configurando o cabeçalho de anfitrião para direcionar o pedido para a localização correta. Também pode configurar as suas aplicações para escutar as portas bem conhecidas do endereço IP.

O exemplo seguinte mostra a configuração para uma função da web com uma Web site e aplicação web. O Web site está configurado como a localização de entrada predefinida na porta 80 e as aplicações web estão configuradas para receber pedidos de um cabeçalho de anfitrião alternativo que é chamado "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostheader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>A alteração da configuração de uma função
Pode atualizar a configuração do seu serviço em nuvem enquanto estiver em execução no Azure, sem colocar offline o serviço. Para alterar as informações de configuração, pode carregar um novo ficheiro de configuração, ou edite o ficheiro de configuração no local e aplicá-la ao seu serviço em execução. A configuração de um serviço podem ser efetuadas as seguintes alterações:

* **Alterar os valores das definições de configuração**  
  Quando uma definição de alterações de configuração, pode escolher uma instância de função aplicar a alteração enquanto a instância está online ou reciclar a instância de transições e aplicar a alteração à instância está offline.
* **Alterar a topologia de serviço de instâncias de função**  
  Alterações à topologia não afetam as instâncias em execução, exceto em que uma instância está a ser removida. Todas as instâncias restantes geralmente não têm de ser reciclado; No entanto, pode optar por reciclar instâncias de função em resposta a uma alteração de topologia.
* **Alterar o thumbprint do certificado**  
  Apenas pode atualizar um certificado quando uma instância de função está offline. Se um certificado é adicionado, eliminado ou alterado durante uma instância de função está online, Azure normalmente demora a instância offline para atualizar o certificado e colocá-lo novamente online após concluir a alteração.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Processamento de alterações de configuração com eventos de tempo de execução do serviço
O [Biblioteca Runtime do Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) inclui o [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) espaço de nomes, que fornece as classes para interagir com o ambiente do Azure a partir de uma função. O [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) classe define os seguintes eventos são gerados antes e após uma alteração da configuração:

* **[A alteração](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx) eventos**  
  Isto ocorre antes da alteração da configuração é aplicada a uma instância especificada de uma função que lhe confere a possibilidade de tirá as instâncias de função, se necessário.
* **[Alterar](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx) eventos**  
  Ocorre depois da alteração da configuração é aplicada a uma instância de uma função especificada.

> [!NOTE]
> Porque as alterações de certificado sempre tirar o máximo de instâncias de uma função offline, não podem aumentar os eventos RoleEnvironment.Changing ou RoleEnvironment.Changed.
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Para implementar uma aplicação como um serviço em nuvem no Azure, tem primeiro de pacote de aplicação no formato adequado. Pode utilizar o **CSPack** ferramenta da linha de comandos (instalado com o [Azure SDK](https://azure.microsoft.com/downloads/)) para criar o ficheiro de pacote como uma alternativa ao Visual Studio.

**CSPack** utiliza o conteúdo do ficheiro de definição de serviço e o ficheiro de configuração de serviço para definir o conteúdo do pacote. **CSPack** gera um ficheiro de pacote de aplicação (. cspkg) que pode carregar para o Azure utilizando o [portal do Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Por predefinição, o pacote é denominado `[ServiceDefinitionFileName].cspkg`, mas pode especificar um nome diferente, utilizando o `/out` opção **CSPack**.

**CSPack** está localizado em  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (no windows) está disponível, executando o **linha de comandos do Microsoft Azure** atalho que é instalado com o SDK.  
> 
> Execute o programa de CSPack.exe autonomamente para ver a documentação sobre todos os comandos e comutadores possíveis.
> 
> 

<p />

> [!TIP]
> Executar localmente no seu serviço em nuvem a **emulador de computação do Microsoft Azure**, utilize o **/copyonly** opção. Esta opção copia os ficheiros binários para a aplicação para um esquema de diretório a partir da qual pode executar no emulador de computação.
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>Comando de exemplo para um serviço em nuvem do pacote
O exemplo seguinte cria um pacote de aplicação que contém as informações de uma função da web. O comando Especifica o ficheiro de definição de serviço a utilizar, o diretório onde podem encontrar os ficheiros binários, e o nome do ficheiro do pacote.

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

Se a aplicação contém uma função da web e uma função de trabalho, é utilizado o seguinte comando:

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

Em que as variáveis são definidas do seguinte modo:

| Variável | Valor |
| --- | --- |
| \[DirectoryName\] |O subdiretório sob o diretório de projeto de raiz que contém o ficheiro. csdef do projeto do Azure. |
| \[ServiceDefinition\] |O nome do ficheiro de definição de serviço. Por predefinição, este ficheiro é denominado servicedefinition. Csdef. |
| \[OutputFileName\] |O nome do ficheiro de pacote gerado. Normalmente, isto é definido com o nome da aplicação. Não se for especificado nenhum nome de ficheiro, o pacote de aplicação é criado como \[ApplicationName\]. cspkg. |
| \[RoleName\] |O nome da função, tal como definido no ficheiro de definição de serviço. |
| \[RoleBinariesDirectory] |A localização dos ficheiros binários para a função. |
| \[VirtualPath\] |Os diretórios físicos para cada caminho virtual definido na secção de Sites da definição de serviço. |
| \[PhysicalPath\] |Os diretórios físicos do conteúdo para cada caminho virtual definido no nó de site da definição de serviço. |
| \[RoleAssemblyName\] |O nome do ficheiro binário para a função. |

## <a name="next-steps"></a>Passos seguintes
Estou a criar um pacote de serviço de nuvem e pretender...

* [Configurar o ambiente de trabalho remoto para uma instância de serviço em nuvem][remotedesktop]
* [Implementar um projeto de serviço em nuvem][deploy]

Estou a utilizar o Visual Studio e pretender...

* [Criar um novo serviço de nuvem][vs_create]
* [Reconfigure o serviço cloud existente][vs_reconfigure]
* [Implementar um projeto de serviço em nuvem][vs_deploy]
* [Configurar o ambiente de trabalho remoto para uma instância de serviço em nuvem][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
