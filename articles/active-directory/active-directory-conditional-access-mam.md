---
title: "Azure Active Directory com base na aplicação acesso condicional | Microsoft Docs"
description: "Saiba como funciona o acesso condicional de baseado em aplicações do Azure Active Directory."
services: active-directory
keywords: "acesso condicional para aplicações, o acesso condicional com o Azure AD, o acesso seguro aos recursos da empresa, as políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 48c9f55e2296b88acc697ab818f13787695643a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-app-based-conditional-access"></a>Azure Active Directory com base na aplicação acesso condicional  

Os seus empregados utilizam dispositivos móveis para tarefas pessoais e profissionais. Enquanto a certificar-se de que os seus empregados pode ser produtivo, também quer evitar a perda de dados. Com o Azure Active Directory (Azure AD) com base na aplicação acesso condicional, pode restringir o acesso às suas aplicações em nuvem para aplicações de cliente que podem proteger os dados empresariais.  

Este tópico explica como configurar o Azure AD com base na aplicação com acesso condicional.

## <a name="overview"></a>Descrição geral

Com [acesso condicional do Azure AD](active-directory-conditional-access-azure-portal.md), pode ajustar a forma como os utilizadores autorizados podem aceder aos seus recursos. Por exemplo, pode limitar o acesso às suas aplicações em nuvem para dispositivos fidedignos.

Pode utilizar [políticas de proteção de aplicações do Intune](https://docs.microsoft.com/intune/app-protection-policy) para ajudar a proteger dados da sua empresa. Políticas de proteção de aplicações do Intune não necessitam de solução de gestão de dispositivos móveis (MDM), o que permite-lhe proteger os dados da sua empresa com ou sem inscrição de dispositivos numa solução de gestão de dispositivos.

Azure Active Directory acesso condicional baseado na aplicação ativa limitar o acesso às suas aplicações em nuvem para aplicações de cliente que suportam as políticas de proteção de aplicação do Intune. Por exemplo, pode restringir o acesso ao Exchange Online para a aplicação do Outlook.

Na terminologia de acesso condicional, estas aplicações de cliente são conhecidas como **aprovados aplicações de cliente**.  


![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)


Para obter uma lista de aplicações de cliente aprovados, consulte [aprovados requisito da aplicação de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


Pode combinar políticas de acesso condicional baseado na aplicação com outras políticas, como [políticas de acesso condicional baseado no dispositivo](active-directory-conditional-access-policy-connected-applications.md) para fornecer flexibilidade em como proteger os dados para dispositivos pessoais e empresariais.

 


##<a name="before-you-begin"></a>Antes de começar

Este tópico parte do princípio de que está familiarizado com:

- O [aprovados requisito da aplicação de cliente](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) referência técnica.


- Os conceitos básicos do [acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- Como [configurar uma política de acesso condicional](active-directory-conditional-access-azure-portal-get-started.md).

- O [migração das políticas de acesso condicional](active-directory-conditional-access-best-practices.md#policy-migration).
 

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma política de acesso condicional baseado na aplicação, tem de ter um Enterprise Mobility + Security ou uma subscrição do Azure Active Directory premium, e os utilizadores têm de estar licenciados para o EMS ou do Azure AD. 


## <a name="exchange-online-policy"></a>Política do Exchange Online 

Este cenário é composta por uma política de acesso condicional baseado na aplicação para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Manual de comunicação social cenário

Este cenário pressupõe que um utilizador:

- Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange

- Recebe uma mensagem de e-mail que indica que o acesso apenas está disponível através do Outlook app

- Transferir a aplicação com a ligação

- Abre a aplicação do Outlook e inicia sessão com as credenciais do Azure AD

- É-lhe pedido para instalar Authenticor (iOS) ou do Portal da empresa (Android), para continuar

- Instala a aplicação e pode regressar à aplicação Outlook para continuar

- É-lhe pedido para registar um dispositivo

- É capaz de aceder ao e-mail

As políticas de proteção de aplicação do Intune são ativados no momento de aceder a dados empresariais e poderá solicitar ao utilizador para reiniciar a aplicação, utilize um etc adicionais do PIN (se configurada para a plataforma e de aplicação).

### <a name="configuration"></a>Configuração 

**Passo 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/01.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**:

    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **aplicações de cliente**, selecione **aplicações móveis e aplicações de ambiente de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **controlos de acesso**, tem de ter **requerem a aplicação de cliente aprovada (pré-visualização)** selecionado.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)
 

**Passo 2 - configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.


3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **condições**, terá de configurar **aplicações de cliente**. 

    a. Como **aplicações de cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **controlos de acesso**, tem de ter **requerem a aplicação de cliente aprovada (pré-visualização)** selecionado.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)


**Passo 3 - configurar a política de proteção de aplicação do Intune para iOS e aplicações de cliente do Android**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.


## <a name="exchange-online-and-sharepoint-online-policy"></a>Política do Exchange Online e SharePoint Online

Este cenário é composta por um acesso condicional com política de gestão de aplicações móveis para aceder ao Exchange Online e SharePoint Online com aplicações aprovadas.

### <a name="scenario-playbook"></a>Manual de comunicação social cenário

Este cenário pressupõe que um utilizador:

- Tenta utilizar a aplicação do SharePoint para ligar e também para ver os respetivos sites empresariais

- Tentativa de início de sessão com as mesmas credenciais que as credenciais de aplicação do Outlook

- Não tem de voltar a registar e aceder aos recursos


### <a name="configuration"></a>Configuração

**Passo 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/71.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.


3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365** e **do Office 365 SharePoint Online**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**:

    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **aplicações de cliente**, selecione **aplicações móveis e aplicações de ambiente de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **controlos de acesso**, tem de ter **requerem a aplicação de cliente aprovada (pré-visualização)** selecionado.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Passo 2 - configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/06.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365**. Online 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **condições**, terá de configurar **aplicações de cliente**:

    a. Como **aplicações de cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

    b. Como **controlos de acesso**, tem de ter **requerem a aplicação de cliente aprovada (pré-visualização)** selecionado.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/05.png)




**Passo 3 - configurar a política de proteção de aplicação do Intune para iOS e aplicações de cliente do Android**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.


## <a name="app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivos baseado na aplicação ou em conformidade para o Exchange Online e SharePoint Online

Este cenário é composta por uma política de acesso condicional com base na aplicação ou em conformidade de dispositivo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Manual de comunicação social cenário

Este cenário pressupõe que:
 
- Alguns utilizadores já inscritos (com ou sem dispositivos empresariais)

- Os utilizadores que não estejam inscritos e registados no Azure AD através de uma aplicação protegida necessidade de aplicação para registar um dispositivo para aceder a recursos

- Os utilizadores inscritos utilizando a aplicação de aplicações protegidas não tem de voltar a registar o dispositivo


### <a name="configuration"></a>Configuração

**Passo 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365** e **do Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **aplicações de cliente**, selecione **aplicações móveis e aplicações de ambiente de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

    - **Exigir marcadas como estando em conformidade do dispositivo**

    - **Exigir a aplicação de cliente aprovada (pré-visualização)**

    - **Exigir um dos controlos selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Passo 2 - configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **condições**, terá de configurar **aplicações de cliente**. 

    Como **aplicações de cliente*, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **controlos de acesso**, tem de ter **requerem a aplicação de cliente aprovada (pré-visualização)** selecionado.
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)




**Passo 3 - configurar a política de proteção de aplicação do Intune para iOS e aplicações de cliente do Android**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.





## <a name="app-based-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Política de dispositivos baseado na aplicação e em conformidade para o Exchange Online e SharePoint Online

Este cenário é composta por uma política de acesso condicional com base na aplicação e em conformidade de dispositivo para acesso ao Exchange Online.


### <a name="scenario-playbook"></a>Manual de comunicação social cenário

Este cenário pressupõe que um utilizador:
 
-   Configura o e-mail através de uma aplicação de e-mail nativa no iOS ou Android para ligar ao Exchange
-   Recebe uma mensagem de e-mail que indica que o acesso requer o dispositivo seja inscrito
-   Transfere o portal da empresa e inicia sessão no portal da empresa
-   Verifica o correio e é pedido ao utilizar a aplicação do Outlook
-   Transferir a aplicação do Outlook
-   Abre a aplicação do Outlook e introduz as credenciais utilizadas na inscrição
-   O utilizador é capaz de aceder ao e-mail

As políticas de proteção de aplicação do Intune são ativadas no momento da acesso aos dados empresariais e poderão solicitar ao utilizador para reiniciar a aplicação, utilize um etc adicionais do PIN (se configurada para a aplicação e a plataforma)


### <a name="configuration"></a>Configuração

**Passo 1 – configurar uma política de acesso condicional do Azure AD para o Exchange Online e SharePoint Online**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/62.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365** e **do Office 365 SharePoint Online**. 

     ![Acesso condicional](./media/active-directory-conditional-access-mam/02.png)

4. **Condições:** como **condições**, terá de configurar **plataformas de dispositivos** e **aplicações de cliente**. 
 
    a. Como **plataformas de dispositivos**, selecione **Android** e **iOS**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/03.png)

    b. Como **aplicações de cliente**, selecione **aplicações móveis e aplicações de ambiente de trabalho**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/04.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

    - **Exigir marcadas como estando em conformidade do dispositivo**

    - **Exigir a aplicação de cliente aprovada (pré-visualização)**

    - **Exigir um dos controlos selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/11.png)



**Passo 2 - configurar uma política de acesso condicional do Azure AD para o Exchange Online com o Active Directory Sync (EAS)**

Para a política de acesso condicional neste passo, terá de configurar os seguintes componentes:

![Acesso condicional](./media/active-directory-conditional-access-mam/61.png)

1. O **nome** da sua política de acesso condicional.

2. **Utilizadores e grupos**: cada política de acesso condicional tem de ter, pelo menos, um utilizador ou grupo selecionado.

3. **Aplicações em nuvem:** como aplicações na nuvem, tem de selecionar **Exchange Online do Office 365**. 

    ![Acesso condicional](./media/active-directory-conditional-access-mam/07.png)

4. **Condições:** como **condições**, terá de configurar **aplicações de cliente**. 

    Como **aplicações de cliente**, selecione **Exchange Active Sync**.

    ![Acesso condicional](./media/active-directory-conditional-access-mam/08.png)

5. Como **controlos de acesso**, tem de ter o seguinte selecionado:

    - **Exigir marcadas como estando em conformidade do dispositivo**

    - **Exigir a aplicação de cliente aprovada (pré-visualização)**

    - **Exigir todos os controlos selecionados**   
 
    ![Acesso condicional](./media/active-directory-conditional-access-mam/64.png)




**Passo 3 - configurar a política de proteção de aplicação do Intune para iOS e aplicações de cliente do Android**


![Acesso condicional](./media/active-directory-conditional-access-mam/09.png)

Consulte [proteger aplicações e dados com o Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) para obter mais informações.






## <a name="next-steps"></a>Passos seguintes

Se quiser saber como configurar uma política de acesso condicional, consulte [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, consulte o [melhores práticas para acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 