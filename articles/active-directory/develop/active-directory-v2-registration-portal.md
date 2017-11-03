---
title: "Tópicos de ajuda de Portal de registo de aplicação | Microsoft Docs"
description: "Uma descrição das várias funcionalidades no portal de registo de aplicações da Microsoft."
services: active-directory
documentationcenter: 
author: lnalepa
manager: mbaldwin
editor: 
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: lenalepa
ms.custom: aaddev
ms.openlocfilehash: c60499c425a7fd800f7ca9a5bac1fed5af73b801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="app-registration-reference"></a>Referência de registo de aplicação
Este documento fornece contexto e descrições de diversas funcionalidades encontradas no Portal de registo de aplicações do Microsoft [https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).

## <a name="my-applications"></a>As Minhas Aplicações
Esta lista contém todas as aplicações registadas para utilização com o ponto de final de v 2.0 do Azure AD.  Estas aplicações têm a capacidade para iniciar sessão em utilizadores com ambas as contas pessoais da conta Microsoft e contas de trabalho/escola do Azure Active Directory.  Para saber mais sobre o ponto final v 2.0 do Azure AD, consulte a nossa [descrição geral de v 2.0](active-directory-appmodel-v2-overview.md).  Estas aplicações também podem ser utilizadas para integrar com o Microsoft conta ponto final de autenticação, `https://login.live.com`.

## <a name="live-sdk-applications"></a>Aplicações do SDK em direto
Esta lista contém todas as aplicações registadas para utilização apenas com a conta Microsoft.  Não estão ativadas para utilização com o Azure Active Directory contratutal.  Este é o qual irá obter todas as aplicações que tinham sido registadas com o portal de programador MSA em `https://account.live.com/developers/applications`.  Todas as funções que efetuou anteriormente em `https://account.live.com/developers/applications` agora pode ser efetuada neste portal novo, `https://apps.dev.microsoft.com`.  Se tiver alguma questão mais sobre as aplicações de conta Microsoft, contacte-nos.

## <a name="application-secrets"></a>Segredos de aplicação
Os segredos de aplicação são credenciais que permitem que a aplicação efetuar fiável [autenticação de cliente](http://tools.ietf.org/html/rfc6749#section-2.3) com o Azure AD.  OAuth & OpenID Connect, segredos uma aplicação é frequentemente referido como um `client_secret`.  No protocolo v 2.0, qualquer aplicação que recebe um token de segurança numa localização endereçável web (através de um `https` esquema) tem de utilizar um segredo de aplicação para se identificar para o Azure AD após resgate esse de token de segurança.  Além disso, qualquer cliente nativo que recieves tokens no dispositivo irá ser proibido de utilizar um segredo de aplicação para efetuar a autenticação de cliente, desencorajar-o armazenamento de segredos em ambientes inseguras.

Cada aplicação pode conter dois segredos de aplicação válida dado momento.  Mantendo dois segredos, terá de ablilty para efetuar o rollover da chave periódico em todo o ambiente da sua aplicação.  Depois de ter migrado da totalidade da sua aplicação para um novo segredo, pode eliminar o segredo antigo e aprovisionar uma nova.

Neste momento, apenas dois tipos de segredos de aplicação são permitidos no portal de registo de aplicação.  Escolher **gerar a nova palavra-passe** irá gerar e armazenar um segredo partilhado no arquivo de dados correspondentes, que pode utilizar na sua aplicação.  Escolher **gerar novo par de chaves** irá criar um novo par de chaves públicas/privadas que pode ser transferido e utilizado para autenticação de cliente para o Azure AD.

## <a name="profile"></a>Perfil
A secção de perfil de portal de registo da aplicação pode ser utilizada para personalizar a página para a sua aplicação de início de sessão.  Neste momento, pode alterar o logótipo da aplicação da página, termos da declaração de privacidade e o URL do serviço de início de sessão.  O logótipo tem de ser um ficheiro de imagem GIF, PNG ou JPEG transparente com 48 x 48 ou 50 x 50 pixéis. Limite de tamanho: 15 KB.  Tente alterando os valores e visualizar o início de sessão resultante na página!

## <a name="live-sdk-support"></a>Suporte do SDK em direto
Quando ativa "Live SDK suporte", os segredos de aplicações que cria serão aprovisionados para o Azure AD e arquivos de dados de Account Microsoft.  Isto permitirá a sua aplicação para integrar diretamente com o serviço Microsoft Account (login.live.com).  Se pretender criar uma aplicação utilizando o Microsoft Account diretamente (por oposição a utilizar o ponto final v 2.0 do Azure AD), deve certificar-se de que o suporte de SDK em direto está ativado.

Desativar o suporte de Live SDK irá garantir que o segredo de aplicação só é escrito nos dados do Azure AD armazenar.  Os dados do Azure AD arquivo incorpora regulamentos de nível empresarial que lhe permite cumprir determinadas normas, como a conformidade de FISMA.  Se ativar o suporte de SDK em direto, a aplicação não pode obter a conformidade com algumas dessas normas.

Se planeia utilizar o ponto de final de v 2.0 do Azure AD apenas alguma vez, pode desativar em segurança o suporte da Live SDK.

