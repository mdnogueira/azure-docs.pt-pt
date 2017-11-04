---
title: "Configurar ligações de cluster do Azure Service Fabric seguras | Microsoft Docs"
description: "Saiba como utilizar o Visual Studio para configurar ligações seguras que são suportadas pelo cluster do Service Fabric do Azure."
services: service-fabric
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: tglee
ms.assetid: 80501867-dd7a-4648-8bd6-d4f26b68402d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: dc07b2f38d6fd2de941ebbe99303f6e63cbf122d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurar ligações seguras para um cluster do Service Fabric do Visual Studio
Saiba como utilizar o Visual Studio para acederem um cluster do Service Fabric do Azure com as políticas de controlo de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de ligação de cluster
Dois tipos de ligações são suportados pelo cluster do Azure Service Fabric: **não segura** ligações e **x509 baseada em certificado** proteger ligações. (Para clusters de Service Fabric alojado no local, **Windows** e **dSTS** autenticações também são suportadas.) Tem de configurar o tipo de ligação do cluster quando o cluster está a ser criado. Depois de criado, é possível alterar o tipo de ligação.

As ferramentas do Visual Studio Service Fabric suportam todos os tipos de autenticação para ligação a um cluster para publicação. Consulte [configurar um cluster do Service Fabric do portal do Azure](service-fabric-cluster-creation-via-portal.md) para obter instruções sobre como configurar um cluster do Service Fabric seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurar as ligações de cluster em perfis de publicação
Se publicar um projeto de Service Fabric do Visual Studio, utilize o **publicar aplicação do serviço Fabric** caixa de diálogo para escolher um cluster do Service Fabric do Azure. Em **ponto final de ligação**, selecione um cluster existente na sua subscrição.

![O * * caixa de diálogo Publicar Service Fabric aplicação * * é utilizada para configurar uma ligação de Service Fabric.][publishdialog]

O **publicar aplicação do serviço Fabric** caixa de diálogo valida automaticamente a ligação de cluster. Se lhe for solicitado, inicie sessão sua conta do Azure. Se a validação é efetuada, significa que o sistema tem os certificados corretos instalados para estabelecer ligação ao cluster de forma segura ou o cluster é não segura. Falhas de validação podem ser causadas por problemas de rede ou por não ter o seu sistema corretamente configurado para ligar a um cluster seguro.

![O * * publicar Service Fabric aplicação * * caixa de diálogo valida existente, configurados corretamente ligação de cluster do Service Fabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para ligar a um cluster seguro
1. Certifique-se pode aceder a um dos certificados de cliente que confie o cluster de destino. O certificado é geralmente partilhado como um ficheiro Personal Information Exchange (. pfx). Consulte [configurar um cluster do Service Fabric do portal do Azure](service-fabric-cluster-creation-via-portal.md) sobre como configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado fidedigno. Para tal, faça duplo clique o ficheiro. pfx ou utilizar o script do PowerShell Import PfxCertificate para importar os certificados. Instale o certificado a **Cert: \LocalMachine\My**. É possível aceitar todas as definições predefinidas ao importar o certificado.
3. Escolha o **publicar...**  comando no menu de atalho do projeto para abrir o **publicar aplicação Azure** caixa de diálogo e, em seguida, selecione o cluster de destino. A ferramenta automaticamente resolve a ligação e guarda os parâmetros de ligação segura no perfil de publicação.
4. Opcional: Pode editar o perfil de publicação para especificar uma ligação segura de cluster.
   
   Uma vez que está a editar manualmente o ficheiro XML do perfil de publicação para especificar as informações do certificado, certifique-se a ter em atenção o nome de arquivo de certificados, armazene a localização e o thumbprint do certificado. Terá de fornecer estes valores para o arquivo de certificados de nome e localização do arquivo. Consulte [como: obter o Thumbprint de um certificado](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) para obter mais informações.
   
   Pode utilizar o *ClusterConnectionParameters* parâmetros para especificar os parâmetros do PowerShell a utilizar ao ligar ao cluster Service Fabric. Os parâmetros válidos são os que são aceites pelo cmdlet Connect-ServiceFabricCluster. Consulte [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx) para obter uma lista dos parâmetros disponíveis.
   
   Se estiver a publicação para um cluster remoto, tem de especificar os parâmetros adequados para esse cluster específico. Segue-se um exemplo de ligação a um cluster não segura:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Eis um exemplo para ligar a um x509 baseada em certificados seguro cluster:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Editar quaisquer outras definições necessárias, tais como parâmetros de atualização e localização do ficheiro de parâmetro de aplicação e, em seguida, publicar a aplicação do **publicar aplicação do serviço Fabric** caixa de diálogo no Visual Studio.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como aceder ao Service Fabric clusters, consulte [visualizar o cluster utilizando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
