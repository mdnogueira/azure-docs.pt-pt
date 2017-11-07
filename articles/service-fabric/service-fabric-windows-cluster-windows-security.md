---
title: "Proteger um cluster em execução no Windows através de segurança do Windows | Microsoft Docs"
description: "Saiba como configurar a segurança de nó de nó e nó de cliente num cluster autónomo com o Windows através da utilização de segurança do Windows."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: e093a631b0cf81195981a8e3d345504ebce02723
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Proteger um cluster autónomo no Windows utilizando a segurança do Windows
Para impedir o acesso não autorizado para um cluster do Service Fabric, tem de proteger o cluster. Segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Este artigo descreve como configurar a segurança de nó de nó e nó de cliente através da utilização de segurança do Windows no *Clusterconfig* ficheiro.  O processo correspondente para o passo de segurança de configuração do [criar um cluster autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md). Para obter mais informações sobre como o Service Fabric utiliza a segurança do Windows, consulte [cenários de segurança do Cluster](service-fabric-cluster-security.md).

> [!NOTE]
> Deve considerar a seleção do nó de nó segurança cuidadosamente porque não existe nenhuma atualização do cluster da opção de segurança para outro. Para alterar a seleção de segurança, tem de recriar o cluster completo.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Configurar a segurança do Windows utilizam a gMSA  
O exemplo *ClusterConfig.gMSA.Windows.MultiMachine.JSON* ficheiro de configuração transferido com o [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.Zip](http://go.microsoft.com/fwlink/?LinkId=730690) pacote do cluster autónomo contém um modelo para a configuração através de segurança do Windows [conta de serviço gerida da grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```  
"security": {  
            "WindowsIdentities": {  
                "ClustergMSAIdentity": "accountname@fqdn"  
                "ClusterSPN": "fqdn"  
                "ClientIdentities": [  
                    {  
                        "Identity": "domain\\username",  
                        "IsAdmin": true  
                    }  
                ]  
            }  
        }  
```  
  
| **Definição de configuração** | **Descrição** |  
| --- | --- |  
| WindowsIdentities |Contém as identidades de cliente e de cluster. |  
| ClustergMSAIdentity |Configura a segurança de nó de nó. Um grupo de conta de serviço gerida. |  
| ClusterSPN |Domínio completamente qualificado SPN de conta gMSA|  
| ClientIdentities |Configura a segurança de nó de cliente. Uma matriz de contas de utilizador do cliente. |  
| Identidade |A identidade do cliente, um utilizador de domínio. |  
| IsAdmin |VERDADEIRO Especifica que o utilizador de domínio tem acesso de cliente de administrador, FALSO para acesso de cliente do utilizador. |  
  
[Nó de segurança de nó](service-fabric-cluster-security.md#node-to-node-security) é configurada através da definição **ClustergMSAIdentity** quando recursos de infraestrutura de serviço tem de ser executado sob a gMSA. Para criar relações de confiança entre os nós, estes têm de ser efetuadas em consideração entre si. Isto pode ser conseguido de duas formas diferentes: Especifique o grupo de conta de serviço gerida que inclui todos os nós no cluster ou especifique o grupo de computador de domínio que inclui todos os nós do cluster. Recomendamos vivamente a utilização de [conta de serviço gerida da grupo (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) abordagem, especialmente para clusters maiores (mais de 10 nós) ou para os clusters que se prevê a aumentar ou diminuir.  
Esta abordagem não requer a criação de um grupo de domínio para o qual os administradores de cluster foi concedidos direitos de acesso para adicionar e remover membros. Estas contas também são úteis para a gestão de palavra-passe automática. Para obter mais informações, consulte [introdução às contas de serviço geridas de grupo](http://technet.microsoft.com/library/jj128431.aspx).  
 
[Cliente de segurança de nó](service-fabric-cluster-security.md#client-to-node-security) é configurada utilizando **ClientIdentities**. Para poder estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster de saber qual cliente identidades, que pode confiar. Isto pode ser feito de duas formas diferentes: especificar os utilizadores do grupo de domínio que podem ligar ou especificar os utilizadores de nó de domínio podem ligar-se. Recursos de infraestrutura de serviço suporta dois tipos de controlo de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e utilizador. Controlo de acesso fornece a capacidade para o administrador do cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais segura.  Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços. Para obter mais informações sobre controlos de acesso, consulte [baseado em funções controlo de acesso para clientes de Service Fabric](service-fabric-cluster-security-roles.md).  
 
O exemplo seguinte **segurança** secção configura a segurança do Windows utilizam a gMSA e especifica que as máquinas no *ServiceFabric.clusterA.contoso.com* gMSA fazem parte do cluster e que *CONTOSO\usera* tem acesso de cliente de administrador:  
  
```  
"security": {  
    "WindowsIdentities": {  
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",  
        "ClusterSPN" : "clusterA.contoso.com",  
        "ClientIdentities": [{  
            "Identity": "CONTOSO\\usera",  
            "IsAdmin": true  
        }]  
    }  
}  
```  
  
## <a name="configure-windows-security-using-a-machine-group"></a>Configurar a segurança do Windows utilizando um grupo de máquinas  
O exemplo *ClusterConfig.Windows.MultiMachine.JSON* ficheiro de configuração transferido com o [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.Zip](http://go.microsoft.com/fwlink/?LinkId=730690) pacote do cluster autónomo contém um modelo para a configuração de segurança do Windows.  Segurança do Windows está configurada no **propriedades** secção: 

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Definição de configuração** | **Descrição** |
| --- | --- |
| ClusterCredentialType |**ClusterCredentialType** está definido como *Windows* se ClusterIdentity Especifica um nome de grupo de computador do Active Directory. |  
| ServerCredentialType |Definido como *Windows* para ativar a segurança do Windows para clientes.<br /><br />Isto indica que os clientes do cluster e o próprio cluster estão em execução dentro de um domínio do Active Directory. |  
| WindowsIdentities |Contém as identidades de cliente e de cluster. |  
| ClusterIdentity |Utilize um nome de grupo do computador, domain\machinegroup, para configurar a segurança de nó de nó. |  
| ClientIdentities |Configura a segurança de nó de cliente. Uma matriz de contas de utilizador do cliente. |  
| Identidade |Adicione o utilizador de domínio, o domínio ome de utilizador para a identidade do cliente. |  
| IsAdmin |Definido como verdadeiro para especificar que o utilizador de domínio possui acesso de cliente de administrador ou FALSO para acesso de cliente do utilizador. |  

[Nó de segurança de nó](service-fabric-cluster-security.md#node-to-node-security) estiver configurado utilizando a definição **ClusterIdentity** se pretender utilizar um grupo de computador dentro de um domínio do Active Directory. Para obter mais informações, consulte [criar um grupo de computador no Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Segurança de cliente para o nó](service-fabric-cluster-security.md#client-to-node-security) é configurada utilizando **ClientIdentities**. Estabelecer confiança entre um cliente e o cluster, tem de configurar o cluster para saber o cliente identidades, que pode confiar o cluster. Pode estabelecer confiança de duas formas diferentes:

- Especifique os utilizadores do grupo de domínio que possam ligar.
- Especifique os utilizadores de nó de domínio podem ligar-se.

Recursos de infraestrutura de serviço suporta dois tipos de controlo de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e utilizador. Controlo de acesso permite o administrador do cluster limitar o acesso a determinados tipos de operações de cluster para diferentes grupos de utilizadores, que faz com que o cluster mais segura.  Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.  

O exemplo seguinte **segurança** secção configura a segurança do Windows, especifica que as máquinas no *ServiceFabric/clusterA.contoso.com* fazem parte do cluster e especifica que *CONTOSO\usera* tem acesso de cliente de administrador:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric não devem ser implementado num controlador de domínio. Certifique-se de que Clusterconfig não incluem o endereço IP do controlador de domínio, ao utilizar um grupo de computador ou grupo a conta de serviço gerida (gMSA).
>
>

## <a name="next-steps"></a>Passos seguintes
Depois de configurar a segurança do Windows no *Clusterconfig* ficheiro, retomar o processo de criação de cluster no [criar um cluster autónomo em execução no Windows](service-fabric-cluster-creation-for-windows-server.md).

Para obter mais informações sobre consulte de segurança, segurança de nó de cliente e o controlo de acesso baseado em funções, como nó ao nó [cenários de segurança do Cluster](service-fabric-cluster-security.md).

Consulte [ligar a um cluster seguro](service-fabric-connect-to-secure-cluster.md) para obter exemplos de ligar utilizando o PowerShell ou FabricClient.
