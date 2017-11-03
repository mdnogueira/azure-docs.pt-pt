---
title: "Lista de verificação de segurança de recursos de infraestrutura de serviço do Azure | Microsoft Docs"
description: "Este artigo fornece um conjunto de lista de verificação de segurança de segurança de recursos de infraestrutura do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: d0441f1e96e94352d4112ec387058b47074d8b0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-security-checklist"></a>Lista de verificação de segurança do Azure Service Fabric
Este artigo fornece uma lista de verificação de fácil utilização que irão ajudar a proteger o seu ambiente do Azure Service Fabric.

## <a name="introduction"></a>Introdução
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, a implementação e a gestão de microsserviços dimensionáveis e fiáveis. O Service Fabric também faz face aos desafios significativos no desenvolvimento e na gestão de aplicações na cloud. Permite, assim, que os programadores e administradores evitem problemas complexos de infraestrutura e se concentrem na implementação de cargas de trabalho exigentes e fundamentais que sejam dimensionáveis, fiáveis e geríveis.

## <a name="checklist"></a>Lista de verificação
Utilize a lista de verificação seguinte para ajudar a certificar-se de que ainda não ignoradas problemas importantes na gestão e configuração de uma solução de Azure Service Fabric segura.


|Categoria de lista de verificação| Descrição |
| ------------ | -------- |
|[Controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles) | <ul><li>Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais segura.</li><li>Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). </li><li>   Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.</li></ul>|
|[Certificados x. 509 e o Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>[Certificados](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates) utilizados em clusters de cargas de trabalho de produção em execução devem ser criadas utilizando um serviço de certificado de servidor Windows corretamente configurado ou obtidas a partir de um aprovados [autoridade de certificação (CA)](https://en.wikipedia.org/wiki/Certificate_authority).</li><li>Nunca utilize qualquer [temporário ou testar certificados](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) na produção que são criados com ferramentas como [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Pode utilizar um [certificado autoassinado](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security) mas, deve apenas fazer para clusters de teste e não na produção.</li></ul>|
|[Segurança do cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security) | <ul><li>Os cenários de segurança do cluster incluem a segurança do nó para o nó, segurança de nó de cliente, [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security-roles).</li></ul>|
|[Autenticação do cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica [comunicação de nó de nó](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) para Federação de cluster. </li></ul>|
|[Autenticação de servidor](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) | <ul><li>Autentica o [pontos finais de gestão de cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-portal) para um cliente de gestão.</li></ul>|
|[Segurança da aplicação](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)| <ul><li>A encriptação e desencriptação de valores de configuração de aplicação.</li><li> Encriptação dos dados em nós durante a replicação.</li></ul>|
|[Certificado de cluster](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security) | <ul><li>Este certificado é necessário para proteger a comunicação entre os nós num cluster.</li><li>  Defina o thumbprint do certificado principal na secção de Thumbprint e que o elemento secundário nas variáveis de ThumbprintSecondary.</li></ul>|
|[ServerCertificate](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-x509-security)| <ul><li>Este certificado é apresentado para o cliente ao tentar ligar a este cluster. Pode utilizar dois certificados de servidor diferente, um servidor principal e secundária para a atualização.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Este é um conjunto de certificados que pretende instalar nos clientes autenticados. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Defina o nome comum do certificado de cliente primeiro para o CertificateCommonName. O CertificateIssuerThumbprint é o thumbprint do emissor deste certificado. </li></ul>|
|ReverseProxyCertificate| <ul><li>Este é um certificado opcional que pode ser especificado se pretender proteger os seus [Proxy inverso](https://docs.microsoft.com/en-in/azure/service-fabric/service-fabric-reverseproxy). </li></ul>|
|Cofre de Chaves| <ul><li>Utilizado para gerir os certificados para clusters de Service Fabric no Azure.  </li></ul>|


## <a name="next-steps"></a>Passos seguintes
- [Processo de atualização de Cluster do Service Fabric e as expectativas do utilizador](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-upgrade)
- [Gerir as aplicações de Service Fabric no Visual Studio](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-manage-application-in-visual-studio).
- [Introdução de modelo de estado de funcionamento do serviço Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-health-introduction).
