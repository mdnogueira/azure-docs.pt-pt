---
title: "Pré-requisitos para o Azure AD API do relatório de acesso | Microsoft Docs"
description: "Saiba mais sobre os pré-requisitos para o Azure AD API do relatório de acesso"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 58ebf74e166cdcae2c54b134cb99552333fafa24
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Pré-requisitos para o Azure AD API do relatório de acesso

O [relatório APIs do Azure AD](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) fornecer-lhe acesso programático para os dados através de um conjunto de APIs baseado em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

A Reporting Services utiliza API [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar o acesso a web APIs. 

Para obter acesso aos dados de relatórios através da API, terá de ter uma das seguintes funções atribuídas:

- Leitor de segurança
- Administrador de segurança
- Administrador global


Para preparar o acesso à API do Reporting Services, tem de:

1. Registar uma aplicação 
2. Conceder permissões 
3. Recolher as definições de configuração 

Para perguntas, problemas ou comentários, consulte [um pedido de suporte de ficheiros](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="register-an-azure-active-directory-application"></a>Registar uma aplicação do Azure Active Directory

Tem de registar uma aplicação, mesmo que se estão a aceder a API de relatórios através de um script. Isto dá-lhe um **ID da aplicação**, que é necessário para uma chamada de autorização e permite que o código receber tokens.

Para configurar o seu diretório para o Azure AD API do relatório de acesso, tem de iniciar sessão portal do Azure com uma conta de administrador do Azure que também seja membro do **Administrador Global** função de diretório no seu inquilino do Azure AD.

> [!IMPORTANT]
> As aplicações em execução sob credenciais com privilégios de "admin" como este podem ser muito poderosas, por isso, certifique-se a manter segura a credenciais de ID/segredo da aplicação.
> 


**Para registar uma aplicação do Azure Active Directory:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. No **do Azure Active Directory** painel, clique em **registos de aplicação**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. No **registos de aplicação** painel, na barra de ferramentas na parte superior, clique em **novo registo de aplicação**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. No **criar** painel, execute os seguintes passos:

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. No **nome** caixa de texto, tipo `Reporting API application`.

    b. Como **tipo de aplicação**, selecione **aplicação Web / API**.

    c. No **URL de início de sessão** caixa de texto, tipo `https://localhost`.

    d. Clique em **Criar**. 


## <a name="grant-permissions"></a>Conceder permissões 

O objetivo deste passo é conceder a aplicação **ler os dados de diretório** permissões para o **Windows Azure Active Directory** API.

![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**Para conceder permissão para utilizar a API a aplicação:**

1. No **registos de aplicação** painel, na lista de aplicações, clique em **aplicação API de relatórios**.

2. No **aplicação API de relatórios** painel, na barra de ferramentas na parte superior, clique em **definições**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. No **definições** painel, clique em **as permissões necessárias**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. No **as permissões necessárias** painel, no **API** lista, clique em **Windows Azure Active Directory**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. No **ativar o acesso ao** painel, selecione **ler os dados de diretório**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. Na barra de ferramentas na parte superior, clique em **guardar**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. Clique em **conceder permissões**e, em seguida, clique em **Sim**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>Recolher as definições de configuração 
Esta secção mostra como obter as seguintes definições do diretório:

* Nome de domínio
* ID de cliente
* Segredo do cliente

Necessitar destes valores quando configurar chamadas à API do Reporting Services. 

### <a name="get-your-domain-name"></a>Obter o nome de domínio

**Para obter o seu nome de domínio:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. No **do Azure Active Directory** painel, clique em **nomes de domínio**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. Copie o seu nome de domínio na lista de domínios.


### <a name="get-your-applications-client-id"></a>Obter ID de cliente da aplicação

**Para obter o ID de cliente da aplicação:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. No **registos de aplicação** painel, na lista de aplicações, clique em **aplicação API de relatórios**.

3. No **aplicação API de relatórios** painel, no **ID da aplicação**, clique em **clique para copiar**.

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>Obter o segredo do cliente da aplicação
Para obter o segredo do cliente da sua aplicação, terá de criar uma nova chave e guarde o respetivo valor após guardar a nova chave porque não é possível obter este valor já mais tarde.

**Para obter o segredo do cliente da aplicação:**

1. No [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, clique em **do Active Directory**.
   
    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. No **registos de aplicação** painel, na lista de aplicações, clique em **aplicação API de relatórios**.


3. No **aplicação API de relatórios** painel, na barra de ferramentas na parte superior, clique em **definições**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. No **definições** painel, no **APIR acesso** secção, clique em **chaves**. 

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. No **chaves** painel, execute os seguintes passos:

    ![Registar a aplicação](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. No **Descrição** caixa de texto, tipo `Reporting API`.

    b. Como **expira**, selecione **nos 2 anos**.

    c. Clique em **Guardar**.

    d. Copie o valor da chave.


## <a name="next-steps"></a>Passos Seguintes
* Gostaria de aceder aos dados do Azure AD API do relatório de forma programática? Veja [começar a utilizar a API do Azure Active Directory Reporting](active-directory-reporting-api-getting-started.md).
* Se quiser saber mais sobre os relatórios do Azure Active Directory, consulte o [do Azure Active Directory guia de relatórios](active-directory-reporting-guide.md).  

