---
title: "Proteger um cluster do Service Fabric do Azure no Windows através da utilização de certificados | Microsoft Docs"
description: "Proteger a comunicação dentro de um cluster do Azure Service Fabric autónomo ou no local, bem como entre clientes e o cluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: dd09a4df42c1022c2a9f96daf69591bbfc777d79
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Proteger um cluster autónomo no Windows utilizando certificados x. 509
Este artigo descreve como proteger a comunicação entre os vários nós do cluster autónomo Windows. Descreve também como autenticar clientes que se ligam a este cluster utilizando certificados x. 509. Autenticação garante que apenas utilizadores autorizados podem aceder ao cluster e as aplicações implementadas e executar tarefas de gestão. Segurança do certificado deve ser ativada no cluster quando o cluster for criado.  

Para obter mais informações sobre a segurança do cluster, como nó de nó segurança, segurança de nó de cliente e o controlo de acesso baseado em funções, consulte [cenários de segurança do Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Os certificados precisa?
Para começar, [transferir o pacote de recursos de infraestrutura de serviço para o Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) para um de nós do cluster. O pacote transferido, encontrar um ficheiro de ClusterConfig.X509.MultiMachine.json. Abra o ficheiro e reveja a secção de segurança na secção de propriedades:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

Esta secção descreve os certificados de que precisa de proteger o seu cluster do Windows autónomo. Se especificar um certificado de cluster, defina o valor da ClusterCredentialType para _X509_. Se especificar um certificado de servidor para ligações externos, como o ServerCredentialType _X509_. Embora não seja obrigatório, recomendamos que tem ambos estes certificados para um cluster corretamente protegido. Se definir estes valores para *X509*, também tem de especificar os certificados correspondentes ou de Service Fabric emite uma exceção. Em alguns cenários, pode pretender especificar apenas o _ClientCertificateThumbprints_ ou _ReverseProxyCertificate_. Os cenários, não precisa de definir _ClusterCredentialType_ ou _ServerCredentialType_ para _X509_.


> [!NOTE]
> A [thumbprint](https://en.wikipedia.org/wiki/Public_key_fingerprint) é a identidade do principal de um certificado. Para obter o thumbprint dos certificados que criar, ver [obter um thumbprint de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

A tabela seguinte lista os certificados que necessita na sua configuração de cluster:

| **Definição de CertificateInformation** | **Descrição** |
| --- | --- |
| ClusterCertificate |Recomendado para um ambiente de teste. Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode utilizar dois certificados diferentes, um servidor principal e secundária, para a atualização. Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary. |
| ClusterCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado é necessário para proteger a comunicação entre os nós num cluster. Pode utilizar um ou dois certificados comuns os nomes de clusters. O CertificateIssuerThumbprint corresponde ao thumbprint do emissor deste certificado. Se for utilizado mais do que um certificado com o mesmo nome comum, pode especificar vários thumbprints de emissor.|
| ServerCertificate |Recomendado para um ambiente de teste. Este certificado é apresentado para o cliente ao tentar ligar a este cluster. Para sua comodidade, pode optar por utilizar o mesmo certificado para ClusterCertificate e ServerCertificate. Pode utilizar dois certificados de servidor diferente, um servidor principal e secundária, para a atualização. Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary. |
| ServerCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado é apresentado para o cliente ao tentar ligar a este cluster. O CertificateIssuerThumbprint corresponde ao thumbprint do emissor deste certificado. Se for utilizado mais do que um certificado com o mesmo nome comum, pode especificar vários thumbprints de emissor. Para sua comodidade, pode optar por utilizar o mesmo certificado para ClusterCertificateCommonNames e ServerCertificateCommonNames. Pode utilizar nomes do certificado de servidor comuns de um ou dois. |
| ClientCertificateThumbprints |Instale este conjunto de certificados nos clientes autenticados. Pode ter um número de certificados de cliente diferente instalado nas máquinas que pretende permitir o acesso ao cluster. Defina o thumbprint de cada certificado na variável CertificateThumbprint. Se definir IsAdmin para *verdadeiro*, o cliente com este certificado instalado no mesmo pode fazer administrador atividades de gestão no cluster. Se for IsAdmin *falso*, o cliente com este certificado pode efetuar as ações só são permitidas para direitos de acesso de utilizador, normalmente só de leitura. Para obter mais informações sobre as funções, consulte [controlo de acesso baseado em funções (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Defina o nome comum do certificado de cliente primeiro para o CertificateCommonName. O CertificateIssuerThumbprint é o thumbprint do emissor deste certificado. Para obter mais informações sobre nomes comuns e o emissor, consulte [trabalhar com certificados](https://msdn.microsoft.com/library/ms731899.aspx). |
| ReverseProxyCertificate |Recomendado para um ambiente de teste. Este certificado opcional pode ser especificado se pretender proteger os seus [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que reverseProxyEndpointPort está definido no nodeTypes se utilizar este certificado. |
| ReverseProxyCertificateCommonNames |Recomendado para um ambiente de produção. Este certificado opcional pode ser especificado se pretender proteger os seus [proxy inverso](service-fabric-reverseproxy.md). Certifique-se de que reverseProxyEndpointPort está definido no nodeTypes se utilizar este certificado. |

Eis um exemplo de configuração de cluster onde foram fornecidos o cluster, servidor e certificados de cliente. Para certificados de servidor/cluster/reverseProxy, o thumbprint e o nome comum não podem ser configuradas em conjunto para o mesmo tipo de certificado.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 66 8d 9f a8 ee 71 2b a2 f4 37 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Rollover de certificado
Quando utiliza um nome comum do certificado em vez de uma impressão digital, rollover de certificado não necessita de uma atualização da configuração de cluster. Para atualizações de thumbprint do emissor, certifique-se de que a nova lista de thumbprint intersetar com a lista antiga. Primeiro tem de efetuar uma atualização da configuração com os thumbprints de emissor novo e, em seguida, instale os novos certificados (certificado de cluster/servidor e certificados de emissor) no arquivo. Mantenha o antigo certificado de emissor no arquivo de certificados para, pelo menos, duas horas depois de instalar o novo certificado de emissor.

## <a name="acquire-the-x509-certificates"></a>Adquirir os certificados x. 509
Para proteger a comunicação do cluster, terá primeiro de obter certificados x. 509 para os nós do cluster. Além disso, para limitar a ligação a este cluster para máquinas/utilizadores autorizados, terá de obter e instalar certificados para as máquinas de cliente.

Para clusters que executam cargas de trabalho de produção, utilize um [autoridade (AC) de certificado](https://en.wikipedia.org/wiki/Certificate_authority)-assinado certificado x. 509 para proteger o cluster. Para obter mais informações sobre como obter estes certificados, consulte [como obter um certificado](http://msdn.microsoft.com/library/aa702761.aspx).

Para os clusters que utilizar para fins de teste, pode optar por utilizar um certificado autoassinado.

## <a name="optional-create-a-self-signed-certificate"></a>Opcional: Criar um certificado autoassinado
Uma forma de criar um certificado autoassinado que pode ser protegido corretamente consiste em utilizar o script de CertSetup.ps1 na pasta SDK de Service Fabric no diretório C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edite este ficheiro para alterar o nome predefinido do certificado. (Procure o valor CN = ServiceFabricDevClusterCert.) Execute este script como `.\CertSetup.ps1 -Install`.

Agora exporte o certificado para um ficheiro. pfx com uma palavra-passe protegida. Em primeiro lugar, obter o thumbprint do certificado. 
1. Do **iniciar** menu, execute **gerir certificados de computador**. 

2. Vá para o **Local\Pessoal.** pasta e localizar o certificado que criou. 

3. Faça duplo clique o certificado para abri-lo, selecione o **detalhes** separador e desloque para baixo até o **Thumbprint** campo. 

4. Remover os espaços e copie o valor do thumbprint para o seguinte comando do PowerShell. 

5. Alterar o `String` valor para uma palavra-passe segura adequado para protegê-lo e execute o seguinte no PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Para ver os detalhes de um certificado instalado no computador, execute o seguinte comando do PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Em alternativa, se tiver uma subscrição do Azure, siga os passos na secção [adicionar certificados para o seu Cofre de chaves](service-fabric-cluster-creation-via-arm.md#add-certificates-to-your-key-vault).

## <a name="install-the-certificates"></a>Instalar os certificados
Depois de ter certificados, pode instalá-los em nós de cluster. Os nós têm de ter o Windows PowerShell mais recente 3 instalado nos mesmos. Repita estes passos em cada nó de cluster e certificados de servidor e quaisquer certificados secundários.

1. Copie o ficheiro. pfx ou ficheiros para o nó.

2. Abra uma janela do PowerShell como administrador e introduza os comandos seguintes. Substitua *$pswd* com a palavra-passe que utilizou para criar este certificado. Substitua *$PfxFilePath* com o caminho completo de. pfx copiado para este nó.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Agora defina o controlo de acesso neste certificado para que o processo de Service Fabric, o que é executado sob a conta de serviço de rede, pode utilizá-lo executando o seguinte script. Forneça a impressão digital do certificado e **serviço de rede** para a conta de serviço. Pode verificar que as ACLs no certificado estão corretas ao abrir o certificado no **iniciar** > **gerir certificados de computador** e observar **todas as tarefas**  >  **Gerir chaves privadas**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Repita os passos anteriores para cada certificado de servidor. Também pode utilizar estes passos para instalar os certificados de cliente nas máquinas que pretende permitir o acesso ao cluster.

## <a name="create-the-secure-cluster"></a>Criar o cluster seguro
Depois de configurar a secção de segurança do ficheiro ClusterConfig.X509.MultiMachine.json, pode avançar para o [criar o cluster](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) secção para configurar os nós e criar o cluster autónomo. Não se esqueça de utilizar o ficheiro de ClusterConfig.X509.MultiMachine.json enquanto cria o cluster. Por exemplo, o comando aspeto que poderá ter o seguinte:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Depois de ter o Windows autónomo segura cluster ser executada com êxito e tiver configurado os clientes autenticados para estabelecer ligação ao mesmo, siga os passos na secção [ligar a um cluster com o PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) estabelecer ligação à mesma. Por exemplo:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Em seguida, pode executar outros comandos do PowerShell para trabalhar com este cluster. Por exemplo, pode executar [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) para mostrar uma lista de nós neste cluster segura.


Para remover o cluster, ligar para o nó no cluster onde transferiu o pacote de recursos de infraestrutura de serviço, abra uma linha de comandos e vá para a pasta do pacote. Agora, execute o seguinte comando:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Configuração de certificado incorreto poderá impedir o cluster de futuras cópias de segurança durante a implementação. Para Self-diagnosticar problemas de segurança, ter um aspeto de eventos grupo Visualizador **registos de serviços e aplicações** > **Microsoft Service Fabric**.
> 
> 

