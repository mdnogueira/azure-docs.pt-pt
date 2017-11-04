---
title: Proteger um cluster do Service Fabric do Azure | Microsoft Docs
description: "Saiba mais sobre cenários de segurança para um cluster do Service Fabric do Azure e as várias tecnologias que pode utilizar para implementá-la."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança de cluster do Service Fabric
Um cluster do Service Fabric do Azure é um recurso que é proprietário. Tem de proteger os clusters para ajudar a impedir que utilizadores não autorizados a ligar aos mesmos. Um cluster seguro é especialmente importante quando estiver a executar cargas de trabalho de produção no cluster. Embora seja possível criar um cluster não segura, se o cluster expõe pontos finais de gestão para a internet pública, os utilizadores anónimos podem ligar à mesma. 

Este artigo é uma descrição geral dos cenários de segurança de clusters do Azure e clusters autónomos e as várias tecnologias que pode utilizar para implementá-la:

* Segurança de nó de nó
* Segurança de nó de cliente
* Controlo de Acesso Baseado em Funções (RBAC)

## <a name="node-to-node-security"></a>Segurança de nó de nó
Nó de nó segurança ajuda a comunicação segura entre as VMs ou computadores num cluster. Neste cenário de segurança garante que apenas os computadores que estejam autorizados para aderirem ao cluster podem participar em aplicações e serviços no cluster de alojamento.

![Diagrama de comunicação de nó de nó][Node-to-Node]

Clusters em execução no Azure e autónoma clusters em execução no Windows, ambos os podem utilizar um [certificado segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

### <a name="node-to-node-certificate-security"></a>Segurança do certificado do nó de nó
O Service Fabric utiliza certificados de servidor de x. 509 que especificar como parte da configuração do tipo de nó quando criar um cluster. No final deste artigo, pode ver uma breve descrição geral dos quais estes certificados são e como pode adquirir ou criá-los.

Como configurar a segurança de certificado quando criar o cluster, no portal do Azure, utilizando um modelo Azure Resource Manager ou utilizando um modelo JSON autónomo. Pode definir um certificado principal e um certificado secundário opcional, que é utilizado para rollovers de certificado. Os certificados primários e secundários definir devem ser diferentes do cliente de administrador e certificados de cliente só de leitura que definiu para [segurança de cliente para o nó](#client-to-node-security).

Para saber como configurar a segurança do certificado num cluster do Azure, consulte [configurar um cluster utilizando um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança do certificado num cluster para um cluster do Windows Server autónomo, consulte [proteger um cluster autónomo no Windows utilizando certificados x. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Segurança do Windows de nó para o nó
Para saber como configurar a segurança do Windows para um cluster do Windows Server autónomo, consulte [proteger um cluster autónomo no Windows utilizando a segurança do Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Segurança de nó de cliente
Segurança de cliente para o nó autentica os clientes e ajuda a comunicação segura entre um cliente e os nós do cluster individuais. Este tipo de segurança ajuda a garantir a que apenas utilizadores autorizados podem aceder o cluster e as aplicações que são implementadas no cluster. Os clientes são identificados exclusivamente através das credenciais de segurança do Windows ou as respetivas credenciais de segurança do certificado.

![Diagrama de comunicação de cliente para o nó][Client-to-Node]

Clusters em execução no Azure e autónoma clusters em execução no Windows, ambos os podem utilizar um [certificado segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança do certificado de cliente para o nó
Configure a segurança do certificado de cliente para o nó quando criar o cluster, no portal do Azure, utilizando um modelo do Resource Manager ou utilizando um modelo JSON autónomo. Para criar o certificado, especifique um certificado de cliente de administração ou um certificado de cliente utilizador. Como melhor prática, os cliente e utilizador certificados de cliente administrativo que especificar devem ser diferentes do certificados primários e secundários que especificar para [segurança de nó de nó](#node-to-node-security). Por predefinição, os certificados de cluster para o nó de nó segurança são adicionados à lista de certificados de cliente permitidas admin.

Os clientes que ligam o cluster utilizando o certificado de admin têm acesso total para as capacidades de gestão. Os clientes que ligam o cluster utilizando o certificado de cliente de utilizador só de leitura têm acesso de leitura para as capacidades de gestão. Estes certificados são utilizados para o RBAC que são descritos mais à frente neste artigo.

Para saber como configurar a segurança do certificado num cluster do Azure, consulte [configurar um cluster utilizando um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para saber como configurar a segurança do certificado num cluster para um cluster do Windows Server autónomo, consulte [proteger um cluster autónomo no Windows utilizando certificados x. 509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Segurança do Azure Active Directory de nó de cliente no Azure
Para clusters em execução no Azure, é também pode proteger o acesso para pontos finais de gestão utilizando o Azure Active Directory (Azure AD). Para saber como criar necessários artefactos do Azure AD, como para preenchê-los ao criar o cluster e como ligar a clusters de posteriormente, consulte o artigo [configurar um cluster utilizando um modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="security-recommendations"></a>Recomendações de segurança
Para os clusters do Azure, para segurança de nó de nó, recomendamos que utilize a segurança do Azure AD para autenticar clientes e certificados.

Para clusters de servidores de Windows de autónomos, se tiver o Windows Server 2012 R2 e Windows Active Directory, recomendamos que utilize a segurança do Windows com contas de serviço geridas de grupo. Caso contrário, utilize a segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode utilizar o controlo de acesso para limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores. Isto ajuda a tornar o cluster mais segura. São suportados dois tipos de controlo de acesso para clientes que se ligam a um cluster: função de utilizador e função de administrador.

Os utilizadores que são atribuídos a função de administrador têm acesso total a capacidades de gestão, incluindo a leitura e escrita capacidades. Os utilizadores que são atribuídos a função de utilizador, por predefinição, tem acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta). Também pode resolvem aplicações e serviços.

Defina o cliente de administrador e utilizador funções quando criar o cluster. Atribua funções fornecendo identidades separadas (por exemplo, ao utilizar certificados ou do Azure AD) para cada tipo de função. Para obter mais informações sobre as definições de controlo de acesso predefinida e como alterar as predefinições, consulte [controlo de acesso baseado em funções para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados x. 509 e o Service Fabric
Os certificados digitais de x. 509 normalmente são utilizados para autenticar servidores e clientes. Também são utilizados para encriptar e assinar digitalmente mensagens. Para obter mais informações sobre os certificados digitais de x. 509, consulte [trabalhar com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Alguns pontos importantes a considerar:

* Para criar certificados para os clusters que executam cargas de trabalho de produção, utilize um serviço de certificado de servidor Windows corretamente configurado ou um uma aprovados [autoridade (AC) de certificado](https://en.wikipedia.org/wiki/Certificate_authority).
* Nunca utilize qualquer temporário ou certificados que criar utilizando ferramentas como o MakeCert.exe num ambiente de produção de teste.
* Pode utilizar um certificado autoassinado, mas apenas num cluster de teste. Não utilize um certificado autoassinado em produção.

### <a name="server-x509-certificates"></a>Certificados de x. 509 do servidor
Certificados de servidor têm a tarefa principal de autenticar um servidor (nó) aos clientes ou autenticar um servidor (nó) para um servidor (nó). Quando um cliente ou um nó efetua a autenticação de um nó, uma das verificações iniciais é o valor do nome comum no **requerente** campo. Este nome comum ou um dos nomes de alternativa dos certificados do requerente (SANs) tem de estar presente na lista de nomes comuns permitidos.

Para saber como gerar os certificados que tenham SANs, consulte [como adicionar um nome alternativo do requerente num certificado de LDAP seguro](http://support.microsoft.com/kb/931351).

O **requerente** campo pode ter vários valores. Cada valor é o prefixo uma inicialização para indicar o tipo de valor. Normalmente, a inicialização é **CN** (para *nome comum*); por exemplo, **CN = www.contoso.com**. O **requerente** campo pode estar em branco. Se a opção **nome alternativo do requerente** campo é preenchido, tem de ter o nome comum do certificado e uma entrada por SAN. Estes são introduzidos como **nome DNS** valores.

O valor da **pretendido fins** campo do certificado deve incluir um valor apropriado, tal como **autenticação de servidor** ou **autenticação de cliente**.

### <a name="client-x509-certificates"></a>Certificados x. 509 de cliente
Normalmente, os certificados de cliente não são emitidos por uma AC de terceiros. Em vez disso, o arquivo pessoal da localização do utilizador atual contém, geralmente, os certificados de cliente colocados na mesma por uma autoridade de raiz com um **pretendido fins** valor **autenticação de cliente**. O cliente pode utilizar este certificado quando é necessária a autenticação mútua.

> [!NOTE]
> Todas as operações de gestão num cluster de Service Fabric requerem certificados de servidor. Não não possível utilizar certificados de cliente para gestão.

## <a name="next-steps"></a>Passos seguintes
* [Criar um cluster no Azure utilizando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Criar um cluster utilizando o portal do Azure](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
