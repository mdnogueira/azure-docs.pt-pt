---
title: "Introdução ao Fornecedor do Multi-Factor Auth do Azure | Microsoft Docs"
description: Saiba como criar um Fornecedor do Multi-Factor Auth do Azure.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 977640041f4b58a751848c96e2aa48eb2b284154
ms.contentlocale: pt-pt
ms.lasthandoff: 06/30/2017

---

# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introdução ao Fornecedor do Multi-Factor Auth do Azure
A verificação de dois passos está disponível por predefinição para os administradores globais que tenham utilizadores do Azure Active Directory e do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](multi-factor-authentication-whats-next.md), deverá comprar a versão completa do Multi-Factor Authentication (MFA) do Azure.

É utilizado um Fornecedor do Multi-Factor Auth do Azure para tirar partido das funcionalidades fornecidas pela versão completa do MFA do Azure. Destina-se aos utilizadores que **não têm licenças do MFA do Azure, Azure AD Premium ou Enterprise Mobility + Security (EMS)**.  Por predefinição, o MFA do Azure, Azure AD Premium e EMS incluem a versão completa do MFA do Azure. Se tiver licenças, não precisará de um Fornecedor do Multi-Factor Auth do Azure.

Para transferir o SDK, é preciso um fornecedor do Multi-Factor Auth do Azure.

> [!IMPORTANT]
> Para transferir o SDK, crie um Fornecedor do Multi-Factor Auth do Azure, mesmo que tenha licenças MFA do Azure, AAD Premium ou EMS.  Se criar um fornecedor do Multi-Factor Auth do Azure para esta finalidade e já tiver licenças, verifique se cria o Fornecedor com o modelo **Por Utilizador Ativado**. Em seguida, associe o Fornecedor ao diretório que contém o MFA do Azure, o Azure AD Premium ou as licenças EMS. Esta configuração garante que só lhe será cobrado qualquer valor se tiver mais utilizadores exclusivos a efetuarem a verificação em dois passos do que o número de licenças.

## <a name="what-is-an-azure-multi-factor-auth-provider"></a>O que é um fornecedor de autenticação multifatores do Azure?

Se não tiver licenças para a autenticação multifatores do Azure, pode criar um fornecedor de autenticação que exija uma verificação dos utilizadores em dois passos. Se estiver a desenvolver uma aplicação personalizada e pretender ativar a MFA do Azure, crie um fornecedor de autenticação e [transfira o SDK](multi-factor-authentication-sdk.md).

Existem dois tipos de fornecedores de autenticação e a diferença reside no facto de a subscrição do Azure ser paga. A opção por autenticação calcula o número de autenticações executadas num mês relativamente ao seu inquilino. Esta é a melhor opção no caso de existirem vários utilizadores que são ocasionalmente autenticados, tal como se exigisse uma MFA para uma aplicação personalizada. A opção por utilizador calcula o número de indivíduos no seu inquilino que executam a verificação de dois passos num mês. Esta é a melhor opção caso existam alguns utilizadores com licenças, mas tem de alargar a MFA a mais utilizadores para além dos limites da sua licença.

## <a name="create-a-multi-factor-auth-provider"></a>Criar um Fornecedor do Multi-Factor Auth
Utilize os passos seguintes para criar um Fornecedor do Multi-Factor Auth do Azure.

1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Na parte superior da página do Active Directory, selecione **Fornecedores do Multi-Factor Auth**.
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Na parte inferior, clique em **Novo**.
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. Em Serviços de Aplicações, selecione **Fornecedor de Autenticação Multifatores**
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Selecione **Criação Rápida**.
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Preencha os campos seguintes e selecione **Criar**
   1. **Nome** – O nome do Fornecedor do Multi-Factor Auth.
   2. **Modelo de Utilização** – Escolha uma das duas opções:
      * Por Autenticação – Modelo de compra que cobra por autenticação. Normalmente utilizado para cenários que utilizam o Multi-Factor Authentication do Azure numa aplicação direcionada para o consumidor.
      * Por Utilizador Ativado – Modelo de compra que cobra por utilizador ativado. Normalmente utilizado para acesso dos empregados a aplicações, como o Office 365. Escolha esta opção se tiver alguns utilizadores licenciados do MFA do Azure.
   3. **Diretório** – O inquilino do Azure Active Directory ao qual o Fornecedor do Multi-Factor Authentication está associado. Tenha em atenção o seguinte:
      * Não é necessário um diretório do Azure AD para criar um Fornecedor do Multi-Factor Auth. Deixe a caixa em branco se estiver apenas a planear utilizar o Servidor Multi-Factor Authentication do Azure ou o SDK.
      * O Fornecedor do Multi-Factor Auth tem de estar associado a um diretório do Azure AD para tirar partido das funcionalidades avançadas.
      * O Azure AD Connect, AAD Sync ou DirSync são apenas um requisito se estiver a sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD.  Se utilizar apenas um diretório do Azure AD que não está sincronizado, não é necessário.
        
        ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Assim que clicar em Criar, o Fornecedor do Multi-Factor Auth é criado e deverá ver uma mensagem a indicar: **Fornecedor do Multi-Factor Auth criado com êxito**. Clique em **OK**.
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

