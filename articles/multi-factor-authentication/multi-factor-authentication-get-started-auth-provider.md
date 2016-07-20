<properties 
    pageTitle="Introdução ao Fornecedor do Multi-Factor Auth do Microsoft Azure" 
    description="Saiba como criar um Fornecedor do Multi-Factor Auth do Azure." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>



# Introdução ao Fornecedor do Multi-Factor Auth do Azure
A autenticação multifator está disponível por predefinição para os administradores globais que tenham utilizadores do Azure Active Directory e do Office 365. No entanto, se pretender tirar partido das [funcionalidades avançadas](multi-factor-authentication-whats-next.md), tem de comprar a versão completa do MFA do Azure. 

> [AZURE.NOTE]  É utilizado um Fornecedor do Multi-Factor Auth do Azure para tirar partido das funcionalidades fornecidas pela versão completa do MFA do Azure. Destina-se aos utilizadores que **não têm licenças do MFA do Azure, Azure AD Premium ou EMS**.  Por predefinição, o MFA do Azure, Azure AD Premium e EMS incluem a versão completa do MFA do Azure.  Se tiver licenças, não precisa de um Fornecedor do Multi-Factor Auth do Azure. 
 
Utilize os passos seguintes para criar um Fornecedor do Multi-Factor Auth do Azure.

## Para criar um Fornecedor do Multi-Factor Auth
--------------------------------------------------------------------------------

1. Inicie sessão no **portal clássico do Azure** como Administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Na parte superior da página do Active Directory, selecione **Fornecedores do Multi-Factor Auth**.
![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Na parte inferior, clique em **Novo**.
![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Em **Serviços Aplicacionais**, selecione **Fornecedor do Multi-Factor Auth**
![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Selecione **Criação Rápida**.
![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Preencha os campos seguintes e selecione **Criar**
    1. **Nome** – O nome do Fornecedor do Multi-Factor Auth.
    2. **Modelo de Utilização** – O modelo de utilização do Fornecedor do Multi-Factor Auth.
        - Por Autenticação – Modelo de compra que cobra por autenticação. Normalmente utilizado para cenários que utilizam o Multi-Factor Authentication do Azure numa aplicação direcionada para o consumidor.
        - Por Utilizador Ativado – Modelo de compra que cobra por utilizador ativado. Normalmente utilizado para acesso dos empregados a aplicações, como o Office 365.
    2. **Diretório** – O inquilino do Azure Active Directory ao qual o Fornecedor do Multi-Factor Authentication está associado. Tenha em atenção o seguinte:
        - Não é necessário um diretório do Azure AD para criar um Fornecedor do Multi-Factor Auth.  Simplesmente, deixe a caixa em branco se estiver apenas a planear utilizar o Servidor Multi-Factor Authentication do Azure ou o SDK.
        - O Fornecedor do Multi-Factor Auth tem de estar associado a um diretório do Azure AD para tirar partido das funcionalidades avançadas.
        - O Azure AD Connect, AAD Sync ou DirSync são apenas um requisito se estiver a sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD.  Se utilizar apenas um diretório do Azure AD que não está sincronizado, ![Crie um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)    
5. Assim que clicar em Criar, o Fornecedor do Multi-Factor Auth é criado e deverá ver uma mensagem a indicar: **Fornecedor do Multi-Factor Auth criado com êxito**. Clique em **OK**.
![Criar um Fornecedor de MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)    


<!--HONumber=Jun16_HO2-->


