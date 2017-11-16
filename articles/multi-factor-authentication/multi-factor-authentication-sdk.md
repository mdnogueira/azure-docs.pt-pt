---
title: "Kit de desenvolvimento de software MFA para aplicações personalizadas | Microsoft Docs"
description: "Este artigo mostra como transferir e utilizar o MFA do Azure SDK para ativar a verificação de dois passos para as suas aplicações personalizadas."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: 1c152f67-be02-42a5-a0c7-246fb6b34377
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: 653166235a2fec79945a8e54aafdb8e697e8b634
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Edifício multi-factor Authentication em aplicações personalizadas (SDK)

O Azure multi-factor Authentication Software Development Kit (SDK) permite-lhe criar verificação em dois passos diretamente para os processos de início de sessão ou a transação de aplicações no seu inquilino do Azure AD.

O SDK do multi-factor Authentication está disponível para c#, Visual Basic (.NET), Java, Perl, PHP e Ruby. O SDK fornece um dinâmico de embrulho à volta de verificação em dois passos. Inclui tudo o que precisa para escrever código, incluindo ficheiros de código de origem comentado, ficheiros de exemplo e um ficheiro Leia-me detalhado. Cada SDK também inclui um certificado e chave privada de encriptação de transações que são exclusivas para o fornecedor de autenticação Multifator. Desde que tenham um fornecedor, pode transferir o SDK em formatos e idiomas tantas conforme necessário.

A estrutura das APIs no SDK do multi-factor Authentication é simple. Fazer uma única função chamada a uma API com os parâmetros de opção de multi-factor (por exemplo, o modo de verificação) e os dados de utilizador (como o número de telefone para chamar ou o número PIN para validar). As APIs traduzir a chamada de função para pedidos de serviços web para o serviço baseado na nuvem do Azure multi-factor Authentication. Todas as chamadas tem de incluir uma referência ao certificado privada que está incluída em cada SDK.

Porque as APIs não tiverem acesso a utilizadores registados no Azure Active Directory, tem de fornecer informações de utilizador de um ficheiro ou a base de dados. Além disso, as APIs não fornecem funcionalidades de gestão de inscrição ou utilizador, por isso terá de criar estes processos na sua aplicação.

> [!IMPORTANT]
> Para transferir o SDK, tem de criar um Fornecedor do Multi-Factor Auth do Azure, mesmo que tenha licenças MFA do Azure, AAD Premium ou EMS. Se criar um fornecedor do multi-factor Auth do Azure para esta finalidade e já tiver licenças, certifique-se ao criar o fornecedor com o **por utilizador ativado** modelo. Em seguida, associe o Fornecedor ao diretório que contém o MFA do Azure, o Azure AD Premium ou as licenças EMS. Esta configuração assegura que apenas são cobrados se tiver mais utilizadores exclusivos com o SDK do que o número de licenças que possui.


## <a name="download-the-sdk"></a>Transferir o SDK
Transferir o SDK do multi-factor do Azure requer um [fornecedor do Azure multi-factor Auth](multi-factor-authentication-get-started-auth-provider.md).  Isto requer uma subscrição do Azure completo, mesmo que pertencem a licenças de MFA do Azure, Azure AD Premium ou Enterprise Mobility Suite.  Para transferir o SDK, navegue para o Portal de gestão de multi-factor. Pode aceder ao portal ao gerir diretamente o fornecedor de autenticação Multifator, ou clicando a **"Aceda ao portal do"** ligação na página de definições do serviço de MFA.

### <a name="download-from-the-azure-classic-portal"></a>Transferir a partir do portal clássico do Azure
1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como Administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Na página do Active Directory, ao selecionar superior **fornecedores do multi-factor Auth**
4. Na parte inferior, selecione **gerir**. Uma nova página é aberta.
5. À esquerda, na parte inferior, clique em **SDK**.
   <center>![Transferir](./media/multi-factor-authentication-sdk/download.png)</center>
6. Selecione o idioma que pretende e clique nas ligações de transferência associados.
7. Guarde a transferência.

### <a name="download-from-the-service-settings"></a>Transferir a partir das definições do serviço
1. Inicie sessão no [portal clássico do Azure](https://manage.windowsazure.com) como Administrador.
2. No lado esquerdo, selecione **Active Directory**.
3. Faça duplo clique na sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
5. Em autenticação multifator, selecione **gerir definições do serviço**
   ![transferir](./media/multi-factor-authentication-sdk/download2.png)
6. Na página de definições do serviço, na parte inferior do ecrã, clique em **Ir para o portal**. Uma nova página é aberta.
   ![Transferência](./media/multi-factor-authentication-sdk/download3a.png)
7. À esquerda, na parte inferior, clique em **SDK**.
8. Selecione o idioma que pretende e clique nas ligações de transferência associados.
9. Guarde a transferência.

## <a name="whats-in-the-sdk"></a>O que está no SDK
O SDK inclui os seguintes itens:

* **LEIA-ME**. Explica como utilizar as APIs de autenticação Multifator numa aplicação nova ou existente.
* **Ficheiros de origem** para multi-factor Authentication
* **Certificado de cliente** que utilizar para comunicar com o serviço de multi-factor Authentication
* **Chave privada** para o certificado
* **Chame resultados.** Uma lista de códigos de resultado de chamada. Para abrir este ficheiro, utilize uma aplicação com formatação, tais como o WordPad de texto. Utilize os códigos de resultado da chamada para testar e resolver problemas relacionados com a implementação de multi-factor Authentication na sua aplicação. Não são códigos de estado de autenticação.
* **Exemplos.** Código de exemplo para uma implementação de trabalho básico de multi-factor Authentication.

> [!WARNING]
> O certificado de cliente é um certificado privado exclusivo que foi gerado especialmente para si. Não partilhe ou perder este ficheiro. É a chave para garantir a segurança das suas comunicações com o serviço de multi-factor Authentication.

## <a name="code-sample"></a>Exemplo de código
Código de exemplo mostra como utilizar as APIs no SDK do multi-factor Authentication do Azure para adicionar a verificação de chamada de voz de modo padrão à sua aplicação. Modo padrão é uma chamada telefónica, que o utilizador responde ao premir a tecla #.

Este exemplo utiliza o c# .NET 2.0 multi-factor Authentication SDK numa aplicação ASP.NET básica com c# lógica de lado do servidor, mas o processo é semelhante em outros idiomas. Porque o SDK inclui ficheiros de origem, ficheiros executáveis não, pode criar os ficheiros e de referência-los ou inclui-los diretamente na sua aplicação.

> [!NOTE]
> Quando implementa o multi-factor Authentication, utilize os métodos adicionais (chamada telefónica ou mensagem de texto) como secundária ou terciária verificação para complementar o método de autenticação primária (nome de utilizador e palavra-passe). Estes métodos não são concebidos como métodos de autenticação principal.

### <a name="code-sample-overview"></a>Descrição geral de exemplo de código
Este código de exemplo para uma aplicação de demonstração simples web utiliza uma chamada telefónica com uma resposta da chave de # para verificar a autenticação do utilizador. Este fator de chamada telefónica é conhecido no multi-factor Authentication como modo padrão.

O código do lado do cliente não inclui quaisquer elementos específicos do multi-factor Authentication. Porque os fatores de autenticação adicionais são independentes da autenticação primária, pode adicioná-los sem alterar a interface de início de sessão existente. As APIs do SDK do multi-factor permitem-lhe personalizar a experiência de utilizador, mas poderá não ser necessário alterar nada de todo.

O código do lado do servidor adiciona a autenticação no modo padrão no passo 2. Cria um objeto de PfAuthParams com os parâmetros que são necessários para o modo padrão de verificação: nome de utilizador, um número e modo e o caminho para o certificado de cliente (CertFilePath), que é necessária em cada chamada de telefone. Para uma demonstração de todos os parâmetros no PfAuthParams, consulte o ficheiro de exemplo no SDK.

Em seguida, o código transmite o objeto de PfAuthParams para a função de pf_authenticate(). O valor de retorno indica o êxito ou falha da autenticação do. A saída parâmetros, callStatus e; errorID, contêm informações de resultado de chamada adicionais. Os códigos de resultado de chamada estão documentados no ficheiro de resultados de chamada no SDK.

Esta implementação mínima pode ser escrita em algumas linhas. No entanto, no código de produção, deverá incluir processamento de erros mais sofisticado, código de base de dados adicionais e uma experiência de utilizador.

### <a name="web-client-code"></a>Código de cliente Web
Segue-se código de cliente web para uma página de demonstração.

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código do Lado do Servidor
No seguinte código do lado do servidor, o multi-factor Authentication está configurado e execute no passo 2. Modo padrão (MODE_STANDARD) é uma chamada telefónica para o qual o utilizador responde ao premir a tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
