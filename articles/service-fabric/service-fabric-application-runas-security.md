---
title: "Saiba mais sobre as políticas de segurança do Azure micro-serviços | Microsoft Docs"
description: "Uma descrição geral de como executar uma aplicação de Service Fabric em sistema e contas de segurança local, incluindo o ponto de SetupEntry onde uma aplicação tem de efetuar qualquer ação privilegiada antes de iniciar"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: aae828489b708a5b538df1d63c12be23d0423da7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para a sua aplicação
Ao utilizar o Service Fabric do Azure, pode proteger aplicações em execução no cluster em contas de utilizador diferente. Serviço de recursos de infraestrutura também ajuda a proteger os recursos utilizados por aplicações no momento da implementação sob as contas de utilizador - por exemplo, ficheiros, diretórios e certificados. Isto faz com que aplicações em execução, mesmo num ambiente alojado partilhado, mais segura entre si.

Por predefinição, as aplicações de Service Fabric executam sob a conta que executa o processo de Fabric.exe em. Serviço de recursos de infraestrutura também fornece a capacidade para executar aplicações com uma conta de utilizador local ou a conta de sistema local, o que é especificada no manifesto da aplicação. Tipos de conta de sistema local suportados são **Utilizador_local**, **NetworkService**, **LocalService**, e **LocalSystem**.

 Ao executar o Service Fabric no Windows Server no seu centro de dados utilizando o instalador autónomo, pode utilizar contas de domínio do Active Directory, incluindo contas de serviço geridas de grupo.

Pode definir e criar grupos de utilizadores que podem ser adicionados um ou mais utilizadores para cada grupo de ser geridos em conjunto. Isto é útil quando existem vários utilizadores para pontos de entrada de serviço diferente e que precisam para tem determinados privilégios comuns que estão disponíveis ao nível do grupo.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configure a política para um ponto de entrada de configuração de serviço
Conforme descrito no [modelo de aplicação](service-fabric-application-model.md), o ponto de entrada de configuração, **SetupEntryPoint**, é um ponto de entrada com privilégios que é executada com as mesmas credenciais de Service Fabric (normalmente o *NetworkService* conta) antes de qualquer outro ponto de entrada. O executável que é especificado por **EntryPoint** é, geralmente, o anfitrião do serviço de execução longa. Por isso, ter uma ponto de entrada de configuração individual evita a ter de executar o executável do anfitrião do serviço com privilégios elevados para períodos de tempo prolongados. O executável que **EntryPoint** Especifica for executado após **SetupEntryPoint** sai com sucesso. O processo de resultante é monitorizado e reiniciado e começa novamente com **SetupEntryPoint** se alguma vez termina ou falhas.

Segue-se um exemplo de manifesto do serviço simples que mostra o SetupEntryPoint e o ponto de entrada principal para o serviço.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Configurar a política utilizando uma conta local
Depois de configurar o serviço ter uma ponto de entrada de configuração, pode alterar as permissões de segurança que é executada no manifesto da aplicação. O exemplo seguinte mostra como configurar o serviço para ser executado com privilégios de conta de administrador do utilizador.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

Em primeiro lugar, crie um **principais** secção com um nome de utilizador, tais como SetupAdminUser. Isto indica que o utilizador é membro do grupo de administradores de sistema.

Em seguida, sob o **ServiceManifestImport** secção, configure uma política para aplicar este principal para **SetupEntryPoint**. Isto indica Service Fabric que, à **MySetup.bat** ficheiros é executado, deve ser `RunAs` com privilégios de administrador. Uma vez que tiver *não* aplicada uma política para o ponto de entrada principal, o código no **MyServiceHost.exe** é executado o sistema **NetworkService** conta. Esta é a conta predefinida que todos os pontos de entrada de serviço são executados como.

Vamos adicionar agora o ficheiro MySetup.bat para o projeto do Visual Studio para testar os privilégios de administrador. No Visual Studio, clique com o botão direito no projeto de serviço e adicione um novo ficheiro chamado MySetup.bat.

Em seguida, certifique-se de que o ficheiro MySetup.bat está incluído no pacote de serviço. Por predefinição, não é. Selecione o ficheiro, faça duplo clique para obter o menu de contexto e escolha **propriedades**. Na caixa de diálogo de propriedades, certifique-se de que **copiar para o diretório de saída** está definido como **copiar se for mais recente**. Veja a captura de ecrã abaixo.

![Visual Studio CopyToOutput para o ficheiro de batch SetupEntryPoint][image1]

Agora, abra o ficheiro de MySetup.bat e adicionar os seguintes comandos:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Em seguida, criar e implementar a solução para um cluster de desenvolvimento local. Depois do serviço foi iniciado, conforme mostrado no Service Fabric Explorer, pode ver que o ficheiro MySetup.bat foi concluída com êxito um duas formas. Abra uma linha de comandos do PowerShell e escreva:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Em seguida, tome nota do nome do nó onde o serviço foi implementado e foi iniciada no Service Fabric Explorer – por exemplo, nó 2. Em seguida, navegue para a pasta de trabalho de instância de aplicação para localizar o ficheiro de out.txt que mostra o valor de **TestVariable**. Por exemplo, se este serviço foi implementado para o nó 2, em seguida, pode ir para este caminho para o **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurar a política através da utilização de contas do sistema local
Muitas vezes, é preferível executar o script de arranque utilizando uma conta de sistema local em vez de uma conta de administrador. Com a política de RunAs como um membro do grupo Administradores, normalmente, não funciona corretamente porque as máquinas têm controlo de acesso utilizador (UAC) ativada por predefinição. Nestes casos, **a recomendação está a ser executado o SetupEntryPoint como LocalSystem, em vez de como um utilizador local adicionado ao grupo de administradores**. O exemplo seguinte mostra a definição SetupEntryPoint para ser executado como LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

Para os clusters do Linux, para executar um serviço ou a configuração entrada apontar como **raiz**, pode especificar o **AccountType** como **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Iniciar os comandos do PowerShell a partir de um ponto de entrada de configuração
Para executar o PowerShell do **SetupEntryPoint** ponto, pode executar **PowerShell.exe** num ficheiro batch que aponta para um ficheiro do PowerShell. Primeiro, adicione um ficheiro de PowerShell para o projeto de serviço – por exemplo, **MySetup.ps1**. Não se esqueça de definir o *copiar se for mais recente* propriedade para que o ficheiro também está incluído no pacote de serviço. O exemplo seguinte mostra um ficheiro de batch de exemplo que inicia um ficheiro de PowerShell chamado MySetup.ps1, que define uma variável de ambiente de sistema chamada **TestVariable**.

MySetup.bat para iniciar um ficheiro de PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

No ficheiro de PowerShell, adicione o seguinte para definir uma variável de ambiente de sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Por predefinição, quando executa o ficheiro batch, analisa a pasta de aplicação denominada **trabalhar** para ficheiros. Neste caso, quando é executada MySetup.bat, queremos esta opção para encontrar o ficheiro de MySetup.ps1 na mesma pasta, o que é a aplicação **pacote do código** pasta. Para alterar esta pasta, defina a pasta de trabalho:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>Utilize o redirecionamento de consola de depuração local
Ocasionalmente, é útil ver o resultado da execução de um script para fins de depuração consola. Para tal, pode definir uma política de redirecionamento da consola, que escreve a saída para um ficheiro. O resultado de ficheiro é escrito na pasta de aplicação chamado **registo** no nó em que a aplicação é implementada e executar. (Ver onde pode encontrá-lo no exemplo anterior).

> [!WARNING]
> Nunca utilize a política de redirecionamento de consola numa aplicação que é implementada na produção porque isto pode afetar a aplicação de ativação pós-falha. *Apenas* utilizá-lo para o desenvolvimento local e fins de depuração.  
> 
> 

O exemplo seguinte mostra a definição o redirecionamento de consola com um valor de FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se alterar agora o ficheiro de MySetup.ps1 para escrever um **eco** comando, este irá escrever no ficheiro de saída para fins de depuração:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**Depois de depurar o script, imediatamente remover esta política de redirecionamento de consola**.

## <a name="configure-a-policy-for-service-code-packages"></a>Configurar uma política para pacotes de código do serviço
Nos passos anteriores, vimos como aplicar uma política de RunAs para SetupEntryPoint. Vamos ver um pouco mais profundo como criar principais diferentes que podem ser aplicadas as políticas do serviço.

### <a name="create-local-user-groups"></a>Criar grupos de utilizadores locais
Pode definir e criar grupos de utilizadores que permitem aos utilizadores de um ou mais ser adicionado a um grupo. Isto é útil se existirem vários utilizadores para pontos de entrada de serviço diferente e que precisam para tem determinados privilégios comuns que estão disponíveis ao nível do grupo. O exemplo seguinte mostra um grupo local chamado **LocalAdminGroup** que tem privilégios de administrador. Dois utilizadores, Customer1 e Customer2, que são efetuados os membros deste grupo local.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>Criar utilizadores locais
Pode criar um utilizador local que pode ser utilizado para ajudar a proteger um serviço na aplicação. Quando um **Utilizador_local** tipo de conta é especificada na secção de principais de manifesto da aplicação, Service Fabric cria contas de utilizador local em máquinas em que a aplicação é implementada. Por predefinição, estas contas não têm os mesmos nomes que as especificadas no manifesto da aplicação (por exemplo, Customer3 no seguinte exemplo). Em vez disso, são geradas dinamicamente e ter palavras-passe aleatórias.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Se uma aplicação requer que a conta de utilizador e palavra-passe ser a mesma em todas as máquinas (por exemplo, para ativar a autenticação NTLM), o manifesto do cluster tem de definir NTLMAuthenticationEnabled como verdadeiro. O manifesto do cluster tem de especificar também um NTLMAuthenticationPasswordSecret que é utilizado para gerar a mesma palavra-passe em todas as máquinas.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Atribuir políticas para os pacotes de código do serviço
O **RunAsPolicy** secção para um **ServiceManifestImport** Especifica a conta da secção principais que deve ser utilizada para executar um pacote do código. Também associa os pacotes de código do manifesto de serviço com contas de utilizador na secção principais. Pode especificar este para o programa de configuração ou pontos de entrada principal, ou pode especificar `All` para aplicá-la para ambos. O exemplo seguinte mostra as políticas diferentes a serem aplicadas:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Se **EntryPointType** não for especificado, a predefinição está definida como EntryPointType = "Principal". Especificar **SetupEntryPoint** é especialmente útil quando pretender executar determinadas operações de configuração de privilégio elevado com uma conta do sistema. O código do serviço real pode ser executados com uma conta de menor privilégio.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Aplicar uma política predefinida para todos os pacotes de código do serviço
Utilizar o **DefaultRunAsPolicy** secção para especificar uma conta de utilizador predefinido para todo o código de pacotes que não tem um específico **RunAsPolicy** definido. Se a maioria dos pacotes de código que são especificados no manifesto de serviço utilizado por uma aplicação precisa de ser executado sob o mesmo utilizador, a aplicação apenas pode definir uma política de RunAs predefinida com essa conta de utilizador. O exemplo seguinte especifica que, se um pacote do código não tem um **RunAsPolicy** especificado, o pacote do código deve ser executado o **MyDefaultAccount** especificados na secção principais.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Utilize um utilizador ou grupo de domínio do Active Directory
Para uma instância do Service Fabric que foi instalado no Windows Server utilizando o instalador autónomo, pode executar o serviço sob as credenciais para uma conta de grupo ou utilizador do Active Directory. Este é o Active Directory no local dentro do seu domínio e não está no Azure Active Directory (Azure AD). Através da utilização de um utilizador de domínio ou grupo, em seguida, pode aceder a outros recursos no domínio (por exemplo, as partilhas de ficheiros) tem sido concedidos permissões.

O exemplo seguinte mostra um utilizador do Active Directory chamado *TestUser* com o seu domínio, denominada palavra-passe encriptado utilizando um certificado *MyCert*. Pode utilizar o `Invoke-ServiceFabricEncryptText` comando do PowerShell para criar o texto de cifra secreta. Consulte [gerir segredos em aplicações de Service Fabric](service-fabric-application-secret-management.md) para obter mais detalhes.

Tem de implementar a chave privada do certificado para desencriptar a palavra-passe para o computador local utilizando um método fora de banda (no Azure, este é através do Azure Resource Manager). Em seguida, quando o Service Fabric implementa o pacote de serviço para a máquina, é capaz de desencriptar o segredo e (juntamente com o nome de utilizador) autenticar com o Active Directory para ser executado sob essas credenciais.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Utilize um grupo de conta de serviço gerida.
Para uma instância do Service Fabric que foi instalado no Windows Server utilizando o instalador autónomo, pode executar o serviço como um grupo de conta de serviço gerida (gMSA). Tenha em atenção que se trata do Active Directory no local dentro do seu domínio e não com o Azure Active Directory (Azure AD). Ao utilizar uma gMSA não existe nenhuma palavra-passe ou palavra-passe encriptada armazenados no `Application Manifest`.

O exemplo seguinte mostra como criar uma conta de gMSA denominada *svc teste$*; como implementar essa conta de serviço geridas para os nós de cluster e como configurar o principal de utilizador.

##### <a name="prerequisites"></a>Pré-requisitos.
- O domínio tem uma chave de raiz KDS.
- O domínio tem de estar num Windows Server 2012 ou posterior nível funcional.

##### <a name="example"></a>Exemplo
1. Solicite a um administrador de domínio do Active Directory, criar um geridas de grupo conta de serviço utilizando o `New-ADServiceAccount` commandlet e certifique-se de que o `PrincipalsAllowedToRetrieveManagedPassword` inclui todos os nós de cluster de recursos de infraestrutura de serviço. Tenha em atenção que `AccountName`, `DnsHostName`, e `ServicePrincipalName` tem de ser exclusivo.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. Em cada um de nós de cluster de recursos de infraestrutura de serviço (por exemplo, `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), instalar e testar a gMSA.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Configure o principal de utilizador e a RunAsPolicy para fazer referência ao utilizador.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Atribuir uma política de acesso de segurança para pontos finais HTTP e HTTPS
Se aplicar uma política de RunAs para um serviço e o manifesto do serviço declara recursos de ponto final com o protocolo HTTP, tem de especificar um **SecurityAccessPolicy** para garantir que as portas atribuídas com estes pontos finais estão corretamente controlo de acesso listado para a conta de utilizador de RunAs que o serviço é executado. Caso contrário, **http.sys** não tem acesso ao serviço e obter falhas com as chamadas do cliente. O exemplo seguinte aplica-se a conta de Customer1 para um ponto final chamado **EndpointName**, que fornece direitos de acesso total.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Para o ponto final HTTPS, também tem de indicar o nome do certificado para devolver ao cliente. Pode fazê-lo utilizando **EndpointBindingPolicy**, com o certificado definido uma secção de certificados no manifesto da aplicação.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Atualizar várias aplicações com pontos finais de https
Tem de ser cuidado para não utilizar o **mesma porta** para várias instâncias da mesma aplicação ao utilizar http**s**. O motivo é que Service Fabric não será possível atualizar o certificado para uma das instâncias da aplicação. Por exemplo, se quiser atualizar os respetivos certificados 1 para o certificado 2 aplicação 1 ou aplicação 2 ambas. Quando a atualização acontece, Service Fabric poderá ter limpar o registo de certificados 1 com o http.sys, apesar da outra aplicação ainda está a utilizá-la. Para evitar esta situação, o Service Fabric Deteta que não existe já está registada na porta com o certificado (devido a http.sys) outra instância da aplicação e falhará a operação.

Por conseguinte, Service Fabric não suporta a atualizar dois os diferentes serviços utilizando **a mesma porta** em instâncias de aplicação diferente. Por outras palavras, não é possível utilizar o mesmo certificado no serviços diferentes na mesma porta. Se precisar de ter um certificado partilhado na mesma porta, tem de garantir que os serviços são colocados em computadores diferentes com restrições de posicionamento. Ou, considere utilizar portas dinâmicas do Service Fabric para cada serviço em cada instância da aplicação, se possível. 

Se vir uma atualização falhar com https, um erro de aviso a indicar "O servidor de HTTP API do Windows não suporta vários certificados para as aplicações que partilham uma porta."

## <a name="a-complete-application-manifest-example"></a>Um exemplo de manifesto de aplicação completa
O manifesto da aplicação seguinte mostra muitas das definições diferentes:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Compreender o modelo de aplicação](service-fabric-application-model.md)
* [Especificar recursos num manifesto de serviço](service-fabric-service-manifest-resources.md)
* [Implementar uma aplicação](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
