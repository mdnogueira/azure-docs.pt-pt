---
title: "Proteger o PaaS web e aplicações móveis com o App Service do Azure | Microsoft Docs"
description: " Saiba mais sobre a segurança do App Service do Azure melhores práticas para proteger o seu web de PaaS e as aplicações móveis. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: dec45d91ad1a73306b3e2656dd9bf7fdbe456720
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Proteger o PaaS web e aplicações móveis com o App Service do Azure

Neste artigo, discutimos a uma coleção de [App Service do Azure](https://azure.microsoft.com/services/app-service/) melhores práticas de segurança para proteger o seu web de PaaS e as aplicações móveis. Estas melhores práticas são derivadas da nossa experiência com o Azure e as experiências dos clientes, como por si.

## <a name="azure-app-service"></a>Serviço de Aplicações do Azure
[App Service do Azure](../app-service/app-service-web-overview.md) é uma PaaS oferta que permite-lhe criar web apps e móveis para qualquer plataforma ou dispositivo e ligar aos dados em qualquer local e na nuvem ou no local. O App Service inclui as capacidades web e móveis que foram anteriormente entregue em separado como Web sites do Azure e Mobile Services do Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um serviço integrado único, o serviço de aplicações fornece um conjunto avançado de capacidades para a web, móveis e cenários de integração.

## <a name="best-practices"></a>Melhores práticas

Quando utilizar o App Service, siga estas práticas recomendadas:

- [Autenticar através do Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). Serviço de aplicações fornece um serviço OAuth 2.0 para o fornecedor de identidade. OAuth 2.0 está centrado nas simplicidade de programador do cliente ao fornecer autorização específico fluxos de telemóveis, aplicações de ambiente de trabalho e aplicações Web. Azure AD utiliza OAuth 2.0 para que possa autorizar o acesso ao móveis e aplicações web.
- Restringir o acesso com base na necessidade de conhecer e princípios de segurança do menor privilégio. Restringir o acesso é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados. Controlo de acesso baseado em funções (RBAC) pode ser utilizado para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. Para saber mais sobre conceder aos utilizadores acesso a aplicações, consulte o artigo [introdução à gestão de acesso](../active-directory/role-based-access-control-what-is.md).
- Proteger as suas chaves. É irrelevante como boa a segurança é se perder as chaves de subscrição. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Ao utilizar o cofre de chaves do Azure, pode encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, ficheiros .PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSM). Para maior segurança, pode importar ou gerar chaves nos HSMs. Consulte [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md) para obter mais informações. Também pode utilizar o Cofre de chaves para gerir os certificados TLS com a renovação automática.
- Restringir os endereços IP de origem recebidos. [Ambiente de serviço de aplicações](../app-service/environment/intro.md) tem uma funcionalidade de integração de rede virtual que o ajuda a restringir os endereços recebidos de IP de origem através de grupos de segurança de rede (NSGs). Se estiver familiarizado com o Azure redes virtuais (VNETs), esta é uma funcionalidade que permite-lhe colocar muitos dos seus recursos do Azure numa rede de internet não, encaminhável que controlam o acesso a. Para obter mais informações, consulte [integrar a sua aplicação com uma Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Passos seguintes
Este artigo introduzidos uma coleção de serviço de aplicações segurança melhores práticas para proteger o seu web de PaaS e as aplicações móveis. Para obter mais informações sobre como proteger as suas implementações PaaS, consulte:

- [Proteger implementações PaaS](security-paas-deployments.md)
- [Proteger o PaaS web e aplicações móveis com a SQL Database do Azure e do armazém de dados do SQL Server](security-paas-applications-using-sql.md)
