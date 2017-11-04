---
title: "Configurar a autenticação multifator - SQL do Azure | Microsoft Docs"
description: "Saiba como utilizar Multi-Factored autenticação com o SSMS para a base de dados SQL e do armazém de dados do SQL Server."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/27/2017
ms.author: rickbyh
ms.openlocfilehash: a6895f7a145c7b925703e4deb32411d51e7a3cab
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurar o multi-factor authentication para SQL Server Management Studio e o Azure AD

Este tópico mostra como utilizar a autenticação multifator (MFA) do Azure Active Directory com o SQL Server Management Studio. MFA do Azure AD podem ser utilizado quando estabelecem ligação SSMS ou SqlPackage.exe a SQL Database do Azure e Azure SQL Data Warehouse.

Para obter uma descrição geral da autenticação multifator de SQL Database do Azure, consulte [Universal autenticação com base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Passos de configuração

1. **Configurar um Azure Active Directory** – para obter mais informações, consulte [administrar o seu diretório do Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrar as identidades no local ao Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar o seu próprio nome de domínio para o Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure suporta agora a Federação com o Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), e [gerir do Azure AD com o Windows PowerShell ](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurar a MFA** - para obter instruções passo a passo, consulte [que é o Azure multi-factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md), [condicional acesso (MFA) com a SQL Database do Azure e do armazém de dados](sql-database-conditional-access.md). (O acesso condicional completa requer um Premium do Azure Active Directory (Azure AD). Limitado a MFA está disponível com um padrão do Azure AD.)
3. **Configurar a base de dados SQL ou SQL Server do armazém de dados para a autenticação do Azure AD** - para obter instruções passo a passo, consulte [ligar à base de dados SQL ou SQL dados do armazém por utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md).
4. **Transferir o SSMS** - no computador cliente, transfira o SSMS mais recente, de [transferir SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Para todas as funcionalidades neste tópico, utilize, pelo menos, Julho de 2017, versão 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ligar utilizando a autenticação universal com o SSMS

Os passos seguintes mostram como ligar à base de dados SQL ou SQL Data Warehouse, utilizando o SSMS mais recente.

1. Para ligar através da autenticação Universal, no **ligar ao servidor** caixa de diálogo, selecione **do Active Directory - Universal com suporte MFA**. (Se vir **autenticação de Universal do Active Directory** não está na versão mais recente do SSMS.)  
   ![ligar-1mfa universal][1]  
2. Concluir o **nome de utilizador** caixa com as credenciais do Azure Active Directory, no formato `user_name@domain.com`.  
   ![1mfa universal-connect-utilizador](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Se estiver a ligar como utilizador convidado, tem de clicar em **opções**e no **ligação propriedade** da caixa de diálogo concluir o **ID de nome ou o inquilino de domínio do AD** caixa. Para obter mais informações, consulte [Universal autenticação com base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)](sql-database-ssms-mfa-authentication.md).
   ![ssms de inquilino de mfa](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Como habitualmente para base de dados SQL e do armazém de dados do SQL Server, tem de clicar em **opções** e especificar a base de dados no **opções** caixa de diálogo. (Se o utilizador ligado é um utilizador convidado (ou seja, joe@outlook.com), tem a caixa de verificação e adicione o nome de domínio do AD atual ou ID de inquilino como parte das opções. Consulte [Universal autenticação com base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)]()(SQL Server-base de dados-ssms-mfa-authentication.md. Em seguida, clique em **Connect**.  
5. Quando o **iniciar sessão na sua conta** aparece a caixa de diálogo, forneça a conta e palavra-passe de identidade do Azure Active Directory. Nenhuma palavra-passe é obrigatório se um utilizador faz parte de um domínio Federado com o Azure AD.  
   ![2mfa-início de sessão][2]  

   > [!NOTE]
   > Para a autenticação Universal com uma conta que não exigir a MFA, estabelece ligação neste momento. Para os utilizadores exigir a MFA, continue com os seguintes passos:
   >  
   
6. Podem aparecer duas caixas de diálogo de configuração da MFA. Este uma vez operação depende do administrador MFA definição e, por conseguinte, pode ser opcional. Para um domínio MFA ativada este passo é por vezes predefinido (por exemplo, o domínio requer que os utilizadores utilizem um smart card e o pin).  
   ![programa de configuração 3mfa][3]  
7. O segundo possível uma vez que a caixa de diálogo permite-lhe selecionar os detalhes do seu método de autenticação. As possíveis opções são configuradas pelo seu administrador.  
   ![4mfa-Certifique-se de-1][4]  
8. O Azure Active Directory envia as informações confirming para si. Quando receber o código de verificação, introduza-o para o **introduza o código de verificação** caixa e clique em **sessão**.  
   ![5mfa-Certifique-se-2][5]  

Quando a verificação estiver concluída, o SSMS liga-se normalmente presuming credenciais válidas e acesso de firewall.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral da autenticação multifator de SQL Database do Azure, consulte autenticação Universal com [base de dados SQL e SQL Data Warehouse (SSMS suporte para a MFA)](sql-database-ssms-mfa-authentication.md).  
- Outras pessoas conceder acesso à base de dados: [autorização e autenticação de base de dados do SQL Server: conceder acesso](sql-database-manage-logins.md)  
- Certifique-se outros utilizadores podem ligar através da firewall: [configurar uma regra de firewall ao nível do servidor da SQL Database do Azure no portal do Azure](sql-database-configure-firewall-settings.md)  
- Quando utilizar **Active Directory - Universal com a MFA** autenticação, o rastreio ADAL está disponível a partir com [SSMS 17.3](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). Desativado por predefinição, pode ativar o rastreio ADAL utilizando o **ferramentas**, **opções** menu, em **serviços do Azure**, **nuvem do Azure**,  **Nível de rastreio de janela de saída ADAL**, seguido pela ativação **saída** no **vista** menu. Os rastreios estão disponíveis na janela de saída quando selecionar **opção do Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

