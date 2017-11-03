---
title: 'Azure Active Directory B2C: Multi-factor Authentication | Microsoft Docs'
description: "Como ativar a multi-factor Authentication nas aplicações direcionadas para o consumidor protegidas pelo Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 62ec48ab067cf02bc8409aca6da704a5418ec270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>O Azure Active Directory B2C: Ativar a multi-factor Authentication nas aplicações direcionadas para o consumidor
Azure Active Directory (Azure AD) B2C integra-se diretamente [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) para que possa adicionar uma segunda camada de segurança para experiências de inscrição e o início de sessão nas aplicações direcionadas para o consumidor. E pode fazê-lo sem ter de escrever uma única linha de código. Atualmente, fornecemos suporte verificação de mensagem de texto e de chamada telefónica. Se já tiver criado políticas de inscrição e o início de sessão, ainda pode ativar a multi-factor Authentication.

> [!NOTE]
> Também pode ser ativada a multi-factor Authentication quando criar políticas de inscrição e o início de sessão, não apenas ao editar as políticas existentes.
> 
> 

Esta funcionalidade ajuda a aplicações processar cenários tais como o seguinte:

* Não necessitam de multi-factor Authentication aceder a uma aplicação, mas precisa de aceder a outro ao. Por exemplo, o consumidor pode iniciar sessão numa aplicação automática insurance com uma conta local ou redes social, mas tem de verificar o número de telefone antes de aceder à aplicação a raiz insurance registado no mesmo diretório.
* Não necessitam de multi-factor Authentication aceder a uma aplicação em geral, mas precisa de aceder as partes confidenciais dentro do mesmo ao. Por exemplo, o consumidor pode iniciar sessão para uma aplicação de banca com uma conta local ou redes social e o saldo da conta de verificação, mas tem de verificar o número de telefone antes de tentar uma transferência durante a transmissão.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar a política de inscrição para ativar a multi-factor Authentication
1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **Políticas de inscrição**.
3. Clique em política de inscrição (por exemplo, "B2C_1_SiUp") para abri-lo.
4. Clique em **multi-factor authentication** e ative o **estado** para **ON**. Clique em **OK**.
5. Clique em **guardar** na parte superior do painel.

Pode utilizar a funcionalidade de "Executar agora" na política para verificar a experiência de consumidor. Confirme se:

Uma conta de consumidor é criada no seu diretório antes de ocorre o passo de multi-factor Authentication. Durante o passo consumidor é-lhe pedido para fornecer o respetivo número de telefone e verificar. Se a verificação for bem sucedida, o número de telefone está ligado à conta de consumidor para utilização posterior. Mesmo se o consumidor cancela ou ignora, seja pode ser-lhe pedido para verificar um número de telefone novamente durante o próximo início de sessão (com o multi-factor Authentication ativada).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar a política de início de sessão para ativar a multi-factor Authentication
1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Clique em **políticas de início de sessão**.
3. Clique a início de sessão política (por exemplo, "B2C_1_SiIn") para abri-lo. Clique em **editar** na parte superior do painel.
4. Clique em **multi-factor authentication** e ative o **estado** para **ON**. Clique em **OK**.
5. Clique em **guardar** na parte superior do painel.

Pode utilizar a funcionalidade de "Executar agora" na política para verificar a experiência de consumidor. Confirme se:

Quando o consumidor inicia sessão (utilizando uma conta local ou redes social), se um número de telefone verificado está ligado à conta de consumidor, seja é-lhe pedido para confirmar. Não se está ligado a nenhum número de telefone, o consumidor é-lhe pedido para fornecer uma e certifique-se de. Verificação bem sucedida, o número de telefone está ligado à conta de consumidor para utilização posterior.

## <a name="multi-factor-authentication-on-other-policies"></a>Multi-factor Authentication em outras políticas
Tal como descrito para políticas de inscrição & início de sessão acima, também é possível ativar a autenticação multifator na inscrição ou políticas de início de sessão e palavra-passe repor as políticas. Estará disponível em breve no perfil editar políticas.

