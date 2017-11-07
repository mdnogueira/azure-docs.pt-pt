---
title: "Implementar uma aplicação Node.js que utiliza o MongoDB | Microsoft Docs"
description: "Obter instruções sobre como compactar várias executáveis de convidados para implementar um cluster do Service Fabric do Azure"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: b76bb756-c1ba-49f9-9666-e9807cf8f92f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell;mikhegn
ms.openlocfilehash: d7a37d7c04f85e9031cab52fa86026e56315c882
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="deploy-multiple-guest-executables"></a>Implementar vários executáveis convidados
Este artigo mostra como o pacote e a implementar vários executáveis de convidado do Azure Service Fabric. Para criar e implementar um único pacote de Service Fabric Leia como para [implementar um executável de convidado para o Service Fabric](service-fabric-deploy-existing-app.md).

Embora estas instruções mostram como implementar uma aplicação com um Node.js front-end que utiliza o MongoDB como arquivo de dados, pode aplicar os passos para qualquer aplicação que tiver dependências de outra aplicação.   

Pode utilizar o Visual Studio para produzir o pacote de aplicação que contém vários executáveis de convidado. Consulte [utilizando o Visual Studio para uma aplicação existente do pacote](service-fabric-deploy-existing-app.md). Depois de ter adicionado o executável de convidado primeiro, o botão direito do rato clique no projeto de aplicação e selecione o **adicionar -> serviço de recursos de infraestrutura de serviço novo** para adicionar o segundo projeto executável do convidado para a solução. Nota: Se optar por associar a origem no projeto do Visual Studio, compilar a solução do Visual Studio, será Certifique-se de que o pacote de aplicação é atualizado com as alterações da origem. 

## <a name="samples"></a>Amostras
* [Exemplo de empacotamento e implementação de um executável de convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomes através de REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>O pacote manualmente a aplicação executável de convidado várias
Em alternativa pode compactar manualmente o convidado executável. Para o empacotamento manual, este artigo utiliza a ferramenta de empacotamento do Service Fabric, que está disponível em [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empacotar a aplicação Node.js
Este artigo pressupõe que o Node.js não está instalado em nós no cluster de Service Fabric. Como consequence, terá de adicionar Node.exe para o diretório de raiz da sua aplicação de nó antes de empacotamento. A estrutura de diretórios da aplicação Node.js (utilizando a estrutura do Express web e de motor de modelo Jade) deve ter um aspeto semelhante ao abaixo:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Como passo seguinte, criar um pacote de aplicação para a aplicação Node.js. O código abaixo cria um pacote de aplicação de Service Fabric que contém a aplicação Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Segue-se uma descrição dos parâmetros que estão a ser utilizado:

* **/Source** aponta para o diretório da aplicação que deve ser empacotada.
* **/ de destino** define o diretório no qual o pacote deve ser criado. Este diretório tem de ser diferente do diretório de origem.
* **/appname** define o nome da aplicação da aplicação existente. É importante compreender que isto traduz-se para o nome do serviço no manifesto e não o nome da aplicação de Service Fabric.
* **/exe** define o executável que deveria Service Fabric para iniciar, neste caso `node.exe`.
* **/Ma** define o argumento que está a ser utilizado para iniciar o executável. Como Node.js não estiver instalado, tem de Service Fabric iniciar o servidor de web de Node.js executando `node.exe bin/www`.  `/ma:'bin/www'`Indica a ferramenta de empacotamento utilizar `bin/www` como argumento para node.exe.
* **/ AppType** define o nome de tipo de aplicação de Service Fabric.

Se navegar para o diretório que foi especificado no parâmetro /target, pode ver que a ferramenta criou um pacote de recursos de infraestrutura de serviço ao funcionamento integral conforme mostrado abaixo:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
O ServiceManifest.xml gerado tem agora uma secção que descreve a forma como o servidor de web de Node.js deve ser iniciado, conforme ilustrado no fragmento de código abaixo:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Neste exemplo, o servidor de web de Node.js escuta a porta 3000, por isso terá de atualizar as informações de ponto final no ficheiro ServiceManifest.xml conforme mostrado abaixo.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empacotar a aplicação do MongoDB
Agora que tem empacotada a aplicação Node.js, pode avançar e pacotes MongoDB. Tal como mencionado anteriormente, os passos que se passar por agora não são específicos para Node.js e MongoDB. Na verdade, aplicam-se a todas as aplicações que se destinam a ser compactado em conjunto como uma aplicação de Service Fabric.  

Para compactar o MongoDB, pretende assegurar-se de que o pacote Mongod.exe e Mongo.exe. Ambos os binários estão localizados no `bin` diretório do seu diretório de instalação do MongoDB. A estrutura de diretórios semelhante o um abaixo.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric tem de iniciar o MongoDB com um comando semelhante ao abaixo, por isso terá de utilizar o `/ma` parâmetro quando empacotamento MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> Os dados não estão a ser preservados no caso de uma falha de nó se colocar o diretório de dados de MongoDB no diretório local do nó. Deve utilizar o armazenamento durável ou implementar uma réplica de MongoDB definida para evitar a perda de dados.  
>
>

No PowerShell ou a shell de comandos, iremos executar a ferramenta de empacotamento com os seguintes parâmetros:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Para poder adicionar o MongoDB para o pacote de aplicação de Service Fabric, tem de certificar-se de que os pontos de parâmetro /target para o mesmo diretório que já contém a aplicação manifestam juntamente com a aplicação Node.js. Terá também de certificar-se de que está a utilizar o mesmo nome de ApplicationType.

Vamos procure o diretório e examine a que a ferramenta que criou.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Como pode ver, a ferramenta adicionada uma nova pasta, MongoDB, para o diretório que contém os binários do MongoDB. Se abrir o `ApplicationManifest.xml` ficheiro, pode ver que o pacote contém agora a aplicação Node.js e o MongoDB. O código abaixo mostra o conteúdo do manifesto da aplicação.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publicação da aplicação
O último passo consiste em publicar a aplicação para o cluster do Service Fabric local, utilizando os scripts do PowerShell abaixo:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Assim que a aplicação for publicada com êxito ao local cluster, pode aceder a aplicação Node.js na porta que foi introduzido no manifesto da aplicação Node.js - por exemplo http://localhost:3000 serviço.

Neste tutorial, constatou como compactar facilmente duas aplicações existentes como uma aplicação de Service Fabric. Também aprendeu como implementá-la para o Service Fabric para que este pode beneficiar de algumas das funcionalidades do Service Fabric, tais como de elevada disponibilidade e a integração do sistema de estado de funcionamento.


## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>A adição de mais executáveis de convidado para uma aplicação existente utilizando Yeoman no Linux

Para adicionar outro serviço a uma aplicação já criada com o `yo`, execute os seguintes passos: 
1. Altere o diretório para a raiz da aplicação existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é a aplicação criada por Yeoman.
2. Executar `yo azuresfguest:AddService` e forneça os detalhes necessários.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a implementação de contentores com [descrição geral do Service Fabric e os contentores](service-fabric-containers-overview.md)
* [Exemplo de empacotamento e implementação de um executável de convidado](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemplo de dois convidado executáveis (c# e nodejs) comunicar através do serviço de nomes através de REST](https://github.com/Azure-Samples/service-fabric-containers)
