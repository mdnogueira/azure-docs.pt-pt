---
title: "Diretrizes de imagem corporativa para aplicações | Microsoft Docs"
description: Obter um guia abrangente para recursos orientadas para programadores do Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 72f4e464-1352-4a49-a18f-c37f58e7d5c4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: skwan
ms.custom: aaddev
ms.openlocfilehash: 4f6806cde52ce965a8f78a5cce8a24c3d1248594
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="branding-guidelines-for-applications"></a>Diretrizes de imagem corporativa para aplicações
Este tópico descreve as diretrizes de imagem corporativa que deve utilizar quando desenvolver aplicações com o Azure Active Directory (Azure AD). Estas diretrizes irão ajudá-lo a direcionar os seus clientes quando se pretendem utilizar a respetiva conta escolar ou profissional, gerida no Azure AD, ou os respetivos pessoal de contas para inscrição e o início de sessão à sua aplicação.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Contas pessoais vs trabalho contas escolares ou profissionais da Microsoft
Microsoft gere dois tipos de contas de utilizador:

* **Contas pessoais** (anteriormente conhecido como Windows Live ID). Estas contas representam a relação entre *individuais* utilizadores e Microsoft e são utilizados para aceder aos dispositivos de consumo e serviços da Microsoft. Estas contas foram concebidas para utilização pessoal.
* **Contas profissionais ou escolares.** Estas contas são geridas pela Microsoft em nome as organizações que utilizam o Azure Active Directory. Estas contas são utilizadas para iniciar sessão Office 365 e outros serviços empresariais da Microsoft.

Microsoft contas profissionais ou escolares, normalmente, são atribuídas aos utilizadores finais (empregados, estudantes, funcionários federais) através das suas organizações (da empresa, escola, agência government). Estas contas são controladas diretamente na nuvem, na plataforma do Azure AD, ou sincronizadas com o Azure AD, a partir de um diretório no local, como o Windows Server Active Directory. A Microsoft está a *conservador* do trabalho ou escola contas, mas as contas são propriedade e controladas pela organização.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Referência a contas do Azure AD na sua aplicação
Microsoft não expõe os utilizadores finais para o Azure ou os nomes de marca de Active Directory e nenhum deve.

* Assim que os utilizadores tem sessão iniciados, deve utilizar o nome da organização e o logótipo quanto possível. Este é melhor do que utilizar termos genéricos como "organização".
* Quando os utilizadores não a sessão, devem consultar às suas contas como "trabalho contas escolares ou profissionais" e utilize o logótipo da Microsoft para transmitir a que estas contas são geridas pela Microsoft. Não utilize os termos, como "conta de empresa", "conta de negócio" ou "conta empresarial" criar confusão de utilizador.

## <a name="user-account-pictogram"></a>Pictograma de conta de utilizador
Numa versão anterior destas diretrizes, recomenda-se utilizando uma pictograma "destaque azul". Com base nos comentários do utilizador e de programador, agora Recomendamos a utilização do logótipo da Microsoft em vez disso. Isto irá ajudar os utilizadores a compreender de que estes podem reutilizar a conta que utiliza no Office 365 ou outro Microsoft serviços empresariais para iniciar sessão na sua aplicação.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Inscrever-se e iniciar sessão no Azure AD
A aplicação poderá apresentar caminhos separados para inscrição e o início de sessão e as secções seguintes fornecem orientações visual para ambos os cenários.

**Se a sua aplicação suporta o início de sessão do utilizador final (por exemplo, gratuita ao modelo de avaliação ou freemium) de cópia de segurança**: pode mostrar um **início de sessão** botão que permite aos utilizadores aceder à sua aplicação com a respetiva conta profissional ou a conta pessoal. Azure AD irá mostrar um pedido de consentimento na primeira vez que acederem a sua aplicação.

**Se a aplicação necessita de permissões que apenas os administradores podem autorizar ou se a aplicação requer licenciamento organizacional**: deve de separar aquisição de administração de início de sessão de utilizador. O **"obter esta aplicação" botão** irá redirecionar admins para iniciar sessão, em seguida, peça-lhes conceder consentimento em nome dos utilizadores na sua organização. Isto tem o benefício adicional suprimir pede consentimento dos utilizadores finais para a sua aplicação.

## <a name="visual-guidance-for-app-acquisition"></a>Visual orientações para a aquisição de aplicação
A ligação "obter a aplicação" tem de redirecionar o utilizador para conceder acesso do Azure AD (autorizar) página, para permitir que o administrador da organização autorizar a aplicação para ter acesso aos dados da sua organização que estão alojados pela Microsoft. Detalhes sobre como pedir acesso são abordados no [integrar aplicações com o Azure Active Directory](active-directory-integrating-applications.md) artigo.

Depois de admins de consentimento para a sua aplicação, pode escolher adicioná-lo a experiência de iniciador de aplicações dos utilizadores do Office 365 (acessível a partir de waffle e [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se pretende anunciar esta capacidade, pode utilizar os termos, tal como "Adicionar esta aplicação a sua organização" e apresentar um botão como esta:

![Cenários e tipos de aplicações](./media/active-directory-branding-guidelines/add-to-my-org.png)

No entanto, recomendamos que escreve o texto explicativo em vez de depender de botões. Por exemplo:

> *Se já utilizar o Office 365 ou outro serviço de empresas da Microsoft, pode conceder simplesmente < your_app_name > acesso aos dados da sua organização. Isto permitirá aos utilizadores aceder < your_app_name > com a conta profissional existente.*
> 
> 

## <a name="visual-guidance-for-sign-in"></a>Documentação de orientação Visual para início de sessão
A aplicação deve apresentar um início de sessão no botão que redireciona os utilizadores para o início de sessão no ponto final correspondente para o protocolo a que utilizar para integrar com o Azure AD. A secção seguinte fornece detalhes sobre o que este botão deverá ser semelhante.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictograma e "Iniciar sessão com Microsoft"
É a associação do logótipo da Microsoft e os termos de licenciamento de "Início de sessão in with Microsoft" que representa exclusivamente do Azure AD, entre outros fornecedores de identidade pode suportar a sua aplicação. Se não tiver espaço suficiente para "Início de sessão in with Microsoft", está ok para abreviá-la para "Início de sessão".

![Cenários e tipos de aplicações](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Cenários e tipos de aplicações](./media/active-directory-branding-guidelines/sign-in-light.png)

Também pode utilizar um esquema de cores escuro dos botões.

![Cenários e tipos de aplicações](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Cenários e tipos de aplicações](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Imagem corporativa Do's e que não deve fazer
**EFETUE** utilize "conta escolar ou profissional" em combinação com o botão "Início de sessão in with Microsoft" para fornecer explicação adicional para ajudar os utilizadores finais reconhecer se que podem utilizá-lo. **Não** utilizar outros termos, como "conta de empresa", "conta de negócio" ou "conta empresarial".

**Não** utilizar "ID do Office 365" ou "ID do Azure". Office 365 também é o nome de um consumidor de oferta da Microsoft que não a utilizar o Azure AD para autenticação.

**Não** alterar o logótipo da Microsoft.

**Não** expor os utilizadores finais para as marcas do Azure ou do Active Directory. Há problema contudo para utilizar estes termos com os programadores, os profissionais de TI e administradores.

## <a name="navigation-dos-and-donts"></a>Navegação Do's e que não deve fazer
**EFETUE** fornecem uma forma dos utilizadores terminar sessão e mudar para outra conta de utilizador. Embora a maioria das pessoas tenham uma única conta pessoal do Microsoft/Facebook/Google/Twitter, pessoas, muitas vezes, estão associadas a mais do que uma organização. Suporte para vários utilizadores com sessão iniciada está disponível em breve.

