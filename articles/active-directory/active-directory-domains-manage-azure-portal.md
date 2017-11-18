---
title: "Gerir nomes de domínio personalizados no Azure Active Directory | Microsoft Docs"
description: "Conceitos de gestão e how-tos para gerir um nome de domínio no Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: michael.tillman
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: e77ea5c3b04a6717e6434f03ca61084af883c31c
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gerir nomes de domínio personalizados no Azure Active Directory
Um nome de domínio é uma parte importante do identificador de vários recursos de diretório: faz parte de um utilizador nome ou endereço de e-mail para um utilizador, parte do endereço de um grupo e podem fazer parte da aplicação do ID de URI para uma aplicação. Um recurso no Azure Active Directory (Azure AD) pode incluir um nome de domínio que já é verificado como pertencentes ao diretório que contém o recurso. Apenas um administrador global pode efetuar tarefas de gestão do domínio no Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Definir o nome de domínio primário para o diretório do Azure AD
Quando é criado o seu diretório, o nome de domínio inicial, tais como 'contoso.onmicrosoft.com,' também é o nome de domínio primário. O domínio primário é o nome de domínio predefinido para um novo utilizador quando criar um novo utilizador. Definir um nome de domínio primário simplifica o processo para um administrador criar novos utilizadores no portal. Para alterar o nome de domínio primário:

1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **do Azure Active Directory**.
3. Selecione **nomes de domínio personalizado**.
     
   ![Gestão de utilizadores de abertura](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. Selecione o nome do domínio que pretende ser o domínio primário.
5. Selecione o **tornar primário** comando. Confirme a sua escolha quando lhe for pedido.
   
   ![Se um nome de domínio primário](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

Pode alterar o nome de domínio primário para o diretório a qualquer domínio personalizado verificado que não está federado. Alterar o domínio primário para o diretório não irá alterar os nomes de utilizador para os utilizadores existentes.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Adicionar nomes de domínio personalizado ao seu inquilino do Azure AD
Pode adicionar a cópia de segurança para um máximo de 900 nomes de domínio gerido. Se estiver a configurar todos os seus domínios para federação com o Active Directory no local, pode adicionar cópias de segurança para um máximo de 450 nomes de domínio em cada diretório. Para obter mais informações, consulte [Federated e nomes de domínio geridos](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Adicionar os subdomínios de um domínio personalizado
Se pretender adicionar um nome de domínio de terceiro nível, tais como 'europe.contoso.com' ao seu diretório, primeiro deve adicionar e verificar o domínio de segundo nível, tal como contoso.com. O subdomínio será verificado automaticamente pelo Azure AD. Para ver que o subdomínio que acabou de adicionar foi verificado, atualize a página no browser que lista os domínios.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>O que fazer se alterar a entidade de registo DNS para o nome de domínio personalizado
Se alterar a entidade de registo DNS para o nome de domínio personalizado, pode continuar a utilizar o seu nome de domínio personalizado com o Azure AD próprio sem interrupção e sem as tarefas de configuração adicionais. Se utilizar o seu nome de domínio personalizado com o Office 365, o Intune ou outros serviços que dependem de nomes de domínio personalizados no Azure AD, consulte a documentação para esses serviços.

## <a name="delete-a-custom-domain-name"></a>Eliminar um nome de domínio personalizado
Pode eliminar um nome de domínio personalizado do seu Azure AD, se a sua organização já não utiliza esse nome de domínio ou se precisar de utilizar esse nome de domínio com o Azure AD outro.

Para eliminar um nome de domínio personalizado, primeiro tem de se certificar de que não existem recursos no seu diretório baseiam-se no nome de domínio. Não é possível eliminar um nome de domínio do seu diretório se:

* Qualquer utilizador tem um nome de utilizador, o endereço de e-mail ou o endereço de proxy que inclui o nome de domínio.
* Qualquer grupo tem um endereço de e-mail ou um endereço de proxy que inclui o nome de domínio.
* Qualquer aplicação no seu Azure AD tem uma URI de ID, que inclui o nome de domínio de aplicação.

Tem de alterar ou eliminar qualquer recurso deste tipo no diretório do Azure AD antes de poder eliminar o nome de domínio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utilize o PowerShell ou Graph API para gerir nomes de domínio
Também pode ser concluída a maioria das tarefas de gestão para nomes de domínio no Azure Active Directory através do Microsoft PowerShell ou através de programação utilizando AD Graph API do Azure.

* [Utilizar o PowerShell para gerir nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Graph API a utilizar para gerir nomes de domínio no Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passos seguintes
* [Adicionar nomes de domínio personalizado](add-custom-domain.md)

