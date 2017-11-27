---
title: "Introdução ao Fornecedor do Multi-Factor Auth do Azure | Microsoft Docs"
description: Saiba como criar um Fornecedor do Multi-Factor Auth do Azure.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: dc1664d382c6e59c125ef00d02a8848079d8bf8d
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Introdução ao Fornecedor de Multi-Factor Authentication do Azure
A verificação de dois passos está disponível por predefinição para os administradores globais que tenham utilizadores do Azure Active Directory e do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](multi-factor-authentication-whats-next.md), deverá comprar a versão completa do Multi-Factor Authentication (MFA) do Azure.

É utilizado um Fornecedor do Multi-Factor Auth do Azure para tirar partido das funcionalidades fornecidas pela versão completa do MFA do Azure. Destina-se aos utilizadores que **não têm licenças do MFA do Azure, Azure AD Premium ou Enterprise Mobility + Security (EMS)**.  Por predefinição, o MFA do Azure, Azure AD Premium e EMS incluem a versão completa do MFA do Azure. Se tiver licenças, não precisará de um Fornecedor do Multi-Factor Auth do Azure.

Para transferir o SDK, é preciso um fornecedor do Multi-Factor Auth do Azure.

> [!IMPORTANT]
> Tem sido anunciada a descontinuação do Azure Multi-Factor Authentication Software Development Kit (SDK). Esta funcionalidade já não é suportada para novos clientes. Os clientes atuais podem continuar a utilizar o SDK até 14 de novembro de 2018. Após esse tempo, as chamadas para o SDK irão falhar.
> [!IMPORTANT]
>Para transferir o SDK, tem de criar um Fornecedor do Multi-Factor Auth do Azure, mesmo que tenha licenças MFA do Azure, AAD Premium ou EMS.  Se criar um fornecedor do Multi-Factor Auth do Azure para esta finalidade e já tiver licenças, verifique se cria o Fornecedor com o modelo **Por Utilizador Ativado**. Em seguida, associe o Fornecedor ao diretório que contém o MFA do Azure, o Azure AD Premium ou as licenças EMS. Esta configuração garante que só lhe será cobrado qualquer valor se tiver mais utilizadores exclusivos a efetuarem a verificação em dois passos do que o número de licenças. 

## <a name="what-is-an-mfa-provider"></a>O que é um Fornecedor de MFA?

Se não tiver licenças para a autenticação multifatores do Azure, pode criar um fornecedor de autenticação que exija uma verificação dos utilizadores em dois passos.

Existem dois tipos de fornecedores de autenticação e a diferença reside no facto de a subscrição do Azure ser paga. A opção por autenticação calcula o número de autenticações executadas num mês relativamente ao seu inquilino. Esta é a melhor opção no caso de existirem vários utilizadores que são ocasionalmente autenticados. A opção por utilizador calcula o número de indivíduos no seu inquilino que executam a verificação de dois passos num mês. Esta é a melhor opção caso existam alguns utilizadores com licenças, mas tem de alargar a MFA a mais utilizadores para além dos limites da sua licença.

## <a name="create-an-mfa-provider---public-preview"></a>Criar um Fornecedor de MFA - pré-visualização pública

Utilize os passos seguintes para criar um Fornecedor do Multi-Factor Authentication no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador. 
2. Selecione **Azure Active Directory** > **Servidor MFA**.
3. Selecione **Fornecedores**.
4. Selecione **Adicionar**.
5. Preencha os campos seguintes e, em seguida, selecione **Adicionar**:
   - **Nome** – o nome do Fornecedor.
   - **Modelo de Utilização** – Escolha uma das duas opções:
      * Por Autenticação – Modelo de compra que cobra por autenticação. Normalmente utilizado para cenários que utilizam o Multi-Factor Authentication do Azure numa aplicação direcionada para o consumidor.
      * Por Utilizador Ativado – Modelo de compra que cobra por utilizador ativado. Normalmente utilizado para acesso dos empregados a aplicações, como o Office 365. Escolha esta opção se tiver alguns utilizadores licenciados do MFA do Azure.
   - **Subscrição** – a subscrição do Azure que é cobrada para a atividade de verificação de dois passos através do Fornecedor. 
   - **Diretório** – o inquilino do Azure Active Directory ao qual o Fornecedor está associado.
      * Não é necessário um diretório do Azure AD para criar um Fornecedor. Deixe esta caixa em branco se estiver apenas a planear transferir o Servidor Multi-Factor Authentication do Azure.
      * O Fornecedor tem de estar associado a um diretório do Azure AD para tirar partido das funcionalidades avançadas.
      * Só pode estar associado um Fornecedor a qualquer diretório do Azure AD.

## <a name="create-an-mfa-provider"></a>Criar um Fornecedor de MFA
Utilize os passos seguintes para criar um Fornecedor do Multi-Factor Authentication no portal clássico:

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
   1. **Nome** – o nome do Fornecedor.
   2. **Modelo de Utilização** – Escolha uma das duas opções:
      * Por Autenticação – Modelo de compra que cobra por autenticação. Normalmente utilizado para cenários que utilizam o Multi-Factor Authentication do Azure numa aplicação direcionada para o consumidor.
      * Por Utilizador Ativado – Modelo de compra que cobra por utilizador ativado. Normalmente utilizado para acesso dos empregados a aplicações, como o Office 365. Escolha esta opção se tiver alguns utilizadores licenciados do MFA do Azure.
   3. **Diretório** – o inquilino do Azure Active Directory ao qual o Fornecedor está associado.
      * Não é necessário um diretório do Azure AD para criar um Fornecedor. Deixe esta caixa em branco se estiver apenas a planear transferir o Servidor Multi-Factor Authentication do Azure.
      * O Fornecedor tem de estar associado a um diretório do Azure AD para tirar partido das funcionalidades avançadas.
      * Só pode estar associado um Fornecedor a qualquer diretório do Azure AD.  
      ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Assim que clicar em Criar, o Fornecedor do Multi-Factor Auth é criado e deverá ver uma mensagem a indicar: **Fornecedor do Multi-Factor Auth criado com êxito**. Clique em **OK**.  
   
   ![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-mfa-provider"></a>Gerir o Fornecedor de MFA

Não pode alterar o modelo de utilização (por utilizador ativado ou por autenticação) após a criação de um fornecedor do MFA. No entanto, pode eliminar o fornecedor do MFA e, em seguida, criar um com um modelo de utilização diferente.

Se o Fornecedor do Multi-Factor Auth atual estiver associado a um diretório do Azure AD (também conhecido como inquilino do Azure AD), pode eliminar o fornecedor do MFA com segurança e criar um que esteja associado ao mesmo inquilino do Azure AD. Em alternativa, se comprou licenças suficientes do MFA, Azure AD Premium ou Enterprise Mobility + Security (EMS) para abranger todos os utilizadores ativados para o MFA, pode eliminar totalmente o fornecedor do MFA.

Se o fornecedor do MFA não estiver associado a um inquilino do Azure AD ou associar o novo fornecedor do MFA a um inquilino diferente do Azure AD, as definições do utilizador e as opções de configuração não são transferidas. Além disso, os Servidores MFA do Azure existentes têm de ser reativados com as credenciais de ativação geradas através do novo Fornecedor do MFA. Reativar os Servidores MFA para associá-los ao novo Fornecedor do MFA não afeta a autenticação por chamada telefónica e mensagem de texto, mas as notificações da aplicação móvel deixarão de funcionar para todos os utilizadores até que reativem a aplicação móvel.

## <a name="next-steps"></a>Passos seguintes

[Configurar as Definições do Multi-Factor Authentication](multi-factor-authentication-whats-next.md)
