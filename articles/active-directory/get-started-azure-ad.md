---
title: "Introdução ao Azure Active Directory | Microsoft Docs"
description: 
keywords: 
author: jeffgilb
manager: femila
ms.author: jeffgilb
ms.reviewer: jsnow
ms.date: 10/04/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
custom: it-pro
ms.openlocfilehash: 714f90155dbf53c5728e27995cee95f132fa452b
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-azure-ad"></a>Introdução ao Azure AD
Gestão de identidades moderna requer reliablity scaleable e consistente para garantir a disponibilidade da a das aplicações e serviços apenas aos utilizadores autenticados. Para suportar adequadamente as necessidades de gestão de identidades de utilizadores, TI precisam de uma forma de fornecer acesso ao software aprovado, público como um serviço (SaaS) de aplicações, uma forma de anfitrião interno aplicações de linha de negócio e até mesmo formas de melhorar no local desenvolvimento da aplicação e a utilização. Todos estes requisitos apontam para a necessidade de uma solução de gestão de identidade baseada na nuvem.      

Azure Active Directory (Azure AD) é Microsoft do multi-inquilino, o serviço de gestão de identidades e diretórios baseados na nuvem. Azure AD combina serviços de diretório de principais, governação de identidades avançada e gestão de acesso de aplicação. A estrutura de multi-inquilino, geo-distribuição, de elevada disponibilidade do Azure AD significa que pode confiar na mesma para as suas necessidades de negócio mais importantes.

AD do Azure inclui um conjunto completo de capacidades de gestão de identidade, incluindo a capacidade para sincronizar informações de recursos no local, imagem corporativa da empresa personalizável, gestão de licenças simples e gestão de palavra-passe do mesmo self-service.  Mais fácil configurar capacidades pode ajudar a começar a utilizar o Azure AD para aplicações baseadas na nuvem seguras, simplificar processos de TI, cortar os custos e ajudar a garantir que são cumpridos os objetivos de conformidade empresarial.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

O resto deste artigo mostra-lhe como começar a utilizar com o Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Inscrever-se no Azure Active Directory Premium
Para [introdução ao Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), pode comprar licenças e associá-las com a sua subscrição do Azure. Se criar uma nova subscrição do Azure, também terá de ativar o seu plano de licenciamento e acesso de serviço do Azure AD. 

Tem várias opções para se inscrever no Active Directory Premium: 

- Utilizar a sua subscrição do Azure ou do Office 365
- Utilizar um plano de licenciamento Enterprise Mobility + Security
- Utilizar um plano de Licenciamento em Volume da Microsoft

> ### <a name="verification-step"></a>Passo de verificação
> Depois de ativar a subscrição, certifique-se de que pode iniciar sessão no serviço.

## <a name="add-a-custom-domain-name"></a>Adicionar um nome de domínio personalizado
Cada diretório do Azure AD é fornecido com um nome de domínio inicial sob a forma de *domainname*. c o m. O nome de domínio inicial não pode ser alterado ou eliminado, mas também pode [adicionar o seu nome de domínio empresarial para o Azure AD](add-custom-domain.md). Por exemplo, a organização tem provavelmente outros nomes de domínio utilizados para fazer negócios e os utilizadores que inicie sessão com o nome de domínio empresarial. Adicionar nomes de domínio personalizado ao Azure AD permite-lhe atribuir nomes de utilizador no diretório que estão familiarizados aos seus utilizadores, tais como 'alice@contoso.com.' em vez de 'alice@.onmicrosoft.com'. O processo é simples:

1. Adicione o nome de domínio personalizado ao seu diretório
2. Adicione uma entrada DNS do nome de domínio à entidade registo do nome de domínio
3. Verifique o nome de domínio personalizado no Azure AD

> ### <a name="verification-step"></a>Passo de verificação
> Depois de adicionar um domínio personalizado, certifique-se de que tem o **Verified** estado é apresentado no **nomes de domínio** painel do portal do Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Adicionar a imagem corporativa à sua página de início de sessão 
Para evitar confusões, muitas empresas pretendem aplicar um aspeto e funcionalidade consistentes em todos os Web sites e serviços que gerem. Azure Active Directory (Azure AD) fornece esta capacidade, permitindo-lhe [personalize o aspeto da página de início de sessão com o logótipo da empresa e esquemas de cores personalizados](customize-branding.md). A página de início de sessão é a página que aparece quando inicia sessão no Office 365 ou outras aplicações baseadas na web que estão a utilizar o Azure AD como o seu fornecedor de identidade. Interagir com esta página para introduzir as suas credenciais.

> ### <a name="verification-step"></a>Passo de verificação
> Inicie sessão no portal do Azure e certifique-se de que a página de início de sessão personalizada e personalizações de idiomas adicionais foram configuradas corretamente. 

## <a name="add-new-users"></a>Adicionar novos utilizadores
Pode [adicionar novos utilizadores da sua organização Azure AD](add-users-azure-active-directory.md) um cada vez no portal do Azure ou ao sincronizar os dados de recursos do Windows Server AD no local. Pode adicionar utilizadores baseados na nuvem diretamente a partir do portal do Azure AD ou sincronizar informações de utilizador no local.

Para ativar a sincronização de identidades no local com o Azure AD, tem de instalar e configurar o [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) num servidor da sua organização. Esta aplicação processa a sincronização de utilizadores e grupos da sua origem de identidades existente com o seu inquilino do Azure AD.

> ### <a name="verification-step"></a>Passo de verificação
> Depois de criar ou sincronizar novos utilizadores, certifique-se de que estão visíveis no Azure AD.

## <a name="assign-licenses"></a>Atribuir licenças
Apesar de obter uma subscrição é tudo o que precisa para configurar funcionalidades pagas, terá ainda [atribuir licenças de utilizador](license-users-groups.md) paga funcionalidades do Azure AD Premium. Qualquer utilizador que devem ter acesso ao ou que é gerido através de um Azure AD paga funcionalidade tem de ser atribuída uma licença. Atribuição de licenças é um mapeamento entre um utilizador e um serviço adquirido, como o Azure AD Premium, Basic ou Enterprise Mobility + Security.

Pode utilizar a atribuição baseada em grupo de licenças para configurar regras, tal como nos exemplos seguintes:

- Todos os utilizadores no seu diretório obtém automaticamente uma licença
- Todos os utilizadores com o título de trabalho adequada obtém uma licença
- Pode delegar a decisão de outros gestores na organização (através da utilização de grupos self-service)

> ### <a name="verification-step"></a>Passo de verificação
> Revisão atribuída e licenças disponíveis em **do Azure Active Directory** > **licenças** > **todos os produtos**.

## <a name="configure-self-service-password-reset"></a>Configurar a reposição de palavra-passe self-service
[Reposição de palavra-passe self-service (SSPR)](active-directory-passwords-getting-started.md) oferece uma simples significa para administradores de TI permitir que os utilizadores reponham ou as respetivas contas ou palavras-passe de desbloqueio. O sistema inclui relatórios detalhados para controlar quando os utilizadores utilizam o sistema, juntamente com notificações para o alertar quanto a utilizações indevidas ou abusos.

> ### <a name="verification-step"></a>Passo de verificação
> Reveja ativada propriedades SSPR em **do Azure Active Directory** > **reposição de palavra-passe** para garantir que o utilizador adequado e atribuições de grupo foram efetuadas. 


### <a name="learn-more"></a>Saiba mais
[Página de produto do Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Azure página de informações de preços de outros do Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)