---
title: "Azure Active Directory B2C: Descrição geral | Microsoft Docs"
description: "Desenvolver aplicações direcionadas para o consumidor com o Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: c465dbde-f800-4f2e-8814-0ff5f5dae610
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: f37fa3a212a0dca46c76b3928cf78756722f0340
ms.openlocfilehash: 2c6765e28f40210730823a9af0b6fb80a5ddc4d1


---
# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C: inscrever-se e iniciar sessão em consumidores nas suas aplicações
O Azure Active Directory B2C é uma solução de gestão de identidades de nuvem abrangente para as suas aplicações móveis e Web direcionadas para o consumidor. É um serviço global com elevada disponibilidade preparada para centenas de milhões de identidades de consumidor. Incorporado numa plataforma segura de nível empresarial, o Azure Active Directory B2C mantém as aplicações, a sua empresa e os seus consumidores protegidos.

No passado, os programadores de aplicações que pretendiam inscrever-se e iniciar sessão de consumidores nas suas aplicações teriam de escrever o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Active Directory B2C oferece aos programadores uma melhor forma de integrar a gestão de identidades do consumidor nas suas aplicações com a ajuda de uma plataforma segura, baseada em padrões, e um conjunto avançado de políticas extensíveis. Quando utiliza o Azure Active Directory B2C, os consumidores podem inscrever-se para as aplicações através das respetivas contas de redes sociais existentes (Facebook, Google, Amazon, LinkedIn) ou através da criação de novas credenciais (endereço de correio eletrónico e palavra-passe, ou nome de utilizador e palavra-passe); chamamos a última de "contas locais".

## <a name="get-started"></a>Introdução
Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

Pode escrever a aplicação no serviço do Azure Active Directory B2C ao selecionar a opção para enviar mensagens de protocolo diretamente, utilizando [OAuth 2.0 ou Abrir ID Connect](active-directory-b2c-reference-protocols.md), ou utilizando as nossas bibliotecas para realizarem o trabalho por si. Escolha a sua plataforma favorita na seguinte tabela para começar.

[!INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Novidades
Verifique novamente aqui com frequência para saber mais sobre as alterações futuras ao Azure Active Directory B2C. Podemos também colocar no Tweet quaisquer atualizações utilizando o @AzureAD.

* Saiba mais sobre a nossa [estrutura de políticas extensível](active-directory-b2c-reference-policies.md) e sobre os tipos de políticas que pode criar e utilizar nas aplicações.
* Marque o nosso [blogue de serviços](https://blogs.msdn.microsoft.com/azureadb2c/) para obter notificações sobre problemas secundários de serviço, atualizações, estado e atenuações. Continue a monitorizar também o [Dashboard de estado do Azure](https://azure.microsoft.com/status/).
* [Limitações de serviço e restrições](active-directory-b2c-limitations.md) atuais.
* Por último, um [exemplo de código](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) utilizando o Azure AD B2C e o ASP.NET Core.

## <a name="how-to-articles"></a>Artigos de procedimentos
Saiba como utilizar funcionalidades específicas do Azure Active Directory B2C:

* Configurar contas [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [conta Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) para utilização nas aplicações direcionadas para o consumidor.
* [Utilizar atributos personalizados para recolher informações sobre os consumidores](active-directory-b2c-reference-custom-attr.md).
* [Ativar a multi-Factor Authentication do Azure nas aplicações direcionadas para o consumidor](active-directory-b2c-reference-mfa.md).
* [Configurar a reposição de palavras-passe self-service para os consumidores](active-directory-b2c-reference-sspr.md).
* [Personalizar o aspeto e funcionalidade de inscrição, início de sessão e outras páginas direcionadas para o consumidor](active-directory-b2c-reference-ui-customization.md) que são efetuados pelo Azure Active Directory B2C.
* [Utilizar a Graph API do Azure Active Directory para programaticamente criar, ler, atualizar e eliminar consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) no seu inquilino do Azure Active Directory B2C.

## <a name="next-steps"></a>Passos seguintes
Estas hiperligações serão úteis para explorar o serviço em profundidade:

* Veja as [Informações sobre preços do Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
* Veja os nossos [exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory&term=b2c) do Azure Active Directory B2C. 
* Obtenha ajuda na Stack Overflow, utilizando as etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
* Indique-nos a sua opinião sobre como utilizar [A Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/), queremos ouvi-la! Utilize a frase "AzureADB2C:" no título da sua publicação para que possa encontrar.
* Reveja a [Referência de Protocolo do Azure AD B2C](active-directory-b2c-reference-protocols.md).
* Reveja a [Referência de Token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
* Leia as [Perguntas mais frequentes do Azure Active Directory B2C](active-directory-b2c-faqs.md).
* [Pedidos de suporte de ficheiros para o Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança dos nossos produtos
Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.




<!--HONumber=Feb17_HO1-->


