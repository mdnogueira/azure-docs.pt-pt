---
title: "Segurança de contentor do Azure Service Fabric | Microsoft Docs"
description: "Saiba como proteger serviços de contentor."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 3e41e293cc5340c0e32cf2cc6ef7ab7534330884
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="container-security"></a>Segurança de contentor

O Service Fabric fornece um mecanismo para serviços no interior de um contentor para aceder a um certificado que está instalado em nós num cluster do Windows ou Linux (versão 5.7 ou superior). Além disso, a Service Fabric também suporta a gMSA (grupo contas de serviço geridas) para contentores do Windows. 

## <a name="certificate-management-for-containers"></a>Gestão de certificados para contentores

Pode proteger os serviços de contentor, especificando um certificado. O certificado tem de estar instalado no LocalMachine em todos os nós do cluster. As informações do certificado são fornecidas no manifesto da aplicação sob o `ContainerHostPolicies` etiquetas como o fragmento seguinte mostra:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Para clusters do windows, ao iniciar a aplicação, o tempo de execução lê os certificados e gera um ficheiro PFX e a palavra-passe para cada certificado. Este ficheiro PFX e a palavra-passe estão acessíveis no interior do contentor utilizando as seguintes variáveis de ambiente: 

* **Certificate_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificate_ServicePackageName_CodePackageName_CertName_Password**

Para os clusters do Linux, o certificates(PEM) simplesmente é copiado através da loja especificada pelo X509StoreName no contentor. As variáveis de ambiente correspondente no linux são:

* **Certificate_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificate_ServicePackageName_CodePackageName_CertName_PrivateKey**

Em alternativa, se já tiver os certificados no formato necessário e pretenda simplesmente para aceder ao mesmo no interior do contentor, pode criar um pacote de dados no interior do seu pacote de aplicação e especifique o seguinte no interior o manifesto da aplicação:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

O serviço de contentor ou processo é responsável por importar os ficheiros de certificado para o contentor. Para importar o certificado, pode utilizar `setupentrypoint.sh` scripts ou executar código personalizado no processo do contentor. Código de exemplo em c# para importar o ficheiro PFX segue:

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Este certificado PFX pode ser utilizado para autenticar a aplicação ou serviço ou uma comunicação segura com outros serviços. Por predefinição, os ficheiros estão ACLed apenas ao sistema. Pode ACL-lo para outras contas conforme exigido pelo serviço.


## <a name="set-up-gmsa-for-windows-containers"></a>Configurar a gMSA para contentores do Windows

Para configurar a gMSA (grupo de contas de serviço gerida), um ficheiro de especificação de credenciais (`credspec`) é colocada em todos os nós do cluster. O ficheiro pode ser copiado em todos os nós a utilizar uma extensão VM.  O `credspec` ficheiro tem de conter as informações de conta gMSA. Para mais informações sobre o `credspec` de ficheiros, consulte [contas de serviço](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). A especificação de credenciais e o `Hostname` etiquetas são especificado no manifesto da aplicação. O `Hostname` etiqueta tem de corresponder ao nome da conta de gMSA o contentor é executado.  O `Hostname` etiquetas permite que o contentor para se autenticar perante outros serviços no domínio utilizando a autenticação Kerberos.  Um exemplo sobre como especificar o `Hostname` e `credspec` na aplicação do manifesto é mostrado no seguinte fragmento:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Passos seguintes

* [Implementar um contentor do Windows para o Service Fabric no Windows Server 2016](service-fabric-get-started-containers.md)
* [Implementar um contentor de Docker Service Fabric no Linux](service-fabric-get-started-containers-linux.md)
