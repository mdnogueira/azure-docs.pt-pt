<properties
    pageTitle="Pré-visualização do Azure Active Directory B2C: Descrição geral | Microsoft Azure"
    description="Desenvolver aplicações direcionadas para o consumidor com o Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/06/2016"
    ms.author="swkrish"/>

# Pré-visualização do Azure Active Directory B2C: inscrever-se e iniciar sessão em consumidores nas suas aplicações

O Azure Active Directory B2C é uma solução de gestão de identidades de nuvem abrangente para as suas aplicações móveis e Web direcionadas para o consumidor. É um serviço global com elevada disponibilidade preparada para centenas de milhões de identidades de consumidor. Incorporado numa plataforma segura de nível empresarial, o Azure Active Directory B2C mantém as aplicações, a sua empresa e os seus consumidores protegidos.

No passado, os programadores de aplicações que pretendiam inscrever-se e iniciar sessão de consumidores nas suas aplicações teriam de escrever o seu próprio código. E teriam de utilizar sistemas ou bases de dados no local para armazenar os nomes de utilizador e as palavras-passe. O Azure Active Directory B2C oferece aos programadores uma melhor forma de integrar a gestão de identidades do consumidor nas suas aplicações com a ajuda de uma plataforma segura, baseada em padrões, e um conjunto avançado de políticas extensíveis. Quando utiliza o Azure Active Directory B2C, os consumidores podem inscrever-se para as aplicações através das respetivas contas de redes sociais existentes (Facebook, Google, Amazon, LinkedIn) ou através da criação de novas credenciais (endereço de correio eletrónico e palavra-passe, ou nome de utilizador e palavra-passe); chamamos a última de "contas locais".

O Azure Active Directory B2C está em pré-visualização. Durante este período, estamos ansiosos por ouvir os seus comentários e experiência à medida que utiliza o mesmo. Com base nos comentários, iremos poder efetuar alterações com o intuito de melhorar o serviço.  Não deve lançar uma aplicação de produção que utilize a pré-visualização durante este período. Dê-nos a conhecer as suas ideias através de [A Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Introdução

Para criar uma aplicação que aceite a inscrição e o início de sessão do consumidor, terá primeiro de registar a aplicação com um inquilino do Azure Active Directory B2C. Obtenha o seu inquilino, utilizando os passos descritos em [Criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

Pode escrever a aplicação no serviço do Azure Active Directory B2C ao selecionar a opção para enviar mensagens de protocolo diretamente, utilizando [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) ou [Abrir ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), ou utilizando as nossas bibliotecas para realizarem o trabalho por si. Escolha a sua plataforma favorita na seguinte tabela para começar.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Novidades

Verifique novamente aqui frequentemente para saber mais sobre as alterações futuras à pré-visualização do Azure Active Directory B2C. Podemos também colocar no Tweet quaisquer atualizações utilizando @AzureAD.

- Saiba mais sobre a nossa [estrutura de políticas extensível](active-directory-b2c-reference-policies.md) e sobre os tipos de políticas que pode criar e utilizar nas aplicações.
- Limitações de [pré-visualização e restrições atuais](active-directory-b2c-limitations.md).

## Artigos de procedimentos

Saiba como utilizar funcionalidades de pré-visualização do Azure Active Directory B2C específicas:

- Configurar contas [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [conta Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) e [LinkedIn](active-directory-b2c-setup-li-app.md) para utilização nas aplicações direcionadas para o consumidor.
- [Utilizar atributos personalizados para recolher informações sobre os consumidores](active-directory-b2c-reference-custom-attr.md).
- [Ativar a multi-Factor Authentication do Azure nas aplicações direcionadas para o consumidor](active-directory-b2c-reference-mfa.md).
- [Configurar a reposição de palavras-passe self-service para os consumidores](active-directory-b2c-reference-sspr.md).
- [Personalizar o aspeto e funcionalidade de inscrição, início de sessão e outras páginas direcionadas para o consumidor](active-directory-b2c-reference-ui-customization.md) que são efetuados pelo Azure Active Directory B2C.
- [Utilizar a Graph API do Azure Active Directory para programaticamente criar, ler, atualizar e eliminar consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) no seu inquilino do Azure Active Directory B2C.

## Passos seguintes

Estas hiperligações serão úteis para explorar o serviço em profundidade:

- Consulte as [Informações sobre preços do Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtenha ajuda na Stack Overflow, utilizando as etiquetas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- Transmita-nos as suas ideias sobre a versão de pré-visualização através de [A Voz do Utilizador](https://feedback.azure.com/forums/169401-azure-active-directory/)--queremos ouvi-las! Utilize a frase "AzureADB2C:" no título da sua publicação para que possa encontrar.
- O Azure Active Directory B2C suporta os protocolos de norma da indústria, OpenID Connect e OAuth 2.0, utilizando um modelo de registo de aplicação a que chamamos "Modelo de aplicação v2.0".
  - [Referência de protocolo do modelo de aplicação v2.0](active-directory-b2c-reference-protocols.md)
  - [Referência de token do modelo de aplicação v2.0](active-directory-b2c-reference-tokens.md)
- Leia as [Perguntas mais frequentes do Azure Active Directory B2C](active-directory-b2c-faqs.md).
- [Pedidos de suporte de ficheiros para o Azure Active Directory B2C](active-directory-b2c-support.md).

## Obter atualizações de segurança dos nossos produtos

Aconselhamo-lo a obter notificações de quando os incidentes de segurança ocorrem, visitando [esta página](https://technet.microsoft.com/security/dd252948) e subscrevendo os alertas de aviso de segurança.



<!--HONumber=Jun16_HO2-->


