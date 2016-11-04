---
title: Obter instruções detalhadas sobre a utilização da pré-visualização de colaboração B2B do Azure Active Directory | Microsoft Docs
description: A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais
services: active-directory
documentationcenter: ''
author: viv-liu
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali

---
# Pré-visualização da colaboração B2B do Azure AD: instruções detalhadas
Estas instruções descrevem como utilizar a colaboração B2B do Azure AD. Como administrador de TI da Contoso, queremos partilhar aplicações com funcionários de três empresas associadas. Nenhuma das empresas associadas têm de ter o Azure AD.

* Alice da Simple Partner Org
* Bernardo, da Medium Partner Org, precisa de aceder a um conjunto de aplicações 
* Camila, da Complex Partner Org, precisa de aceder a um conjunto de aplicações e de associação a grupos na Contoso

Após o envio dos convites aos utilizadores parceiros, podemos configurá-los no Azure AD para garantir o acesso a aplicações e a associação a grupos através do Portal do Azure. Vamos começar por adicionar a Alice.

## Adicionar a Alice ao diretório da Contoso
1. Crie um ficheiro .csv com os cabeçalhos conforme mostrado, povoando apenas o endereço de **E-mail**, **DisplayName** e **InviteContactUsUrl** de Alice. O **DisplayName** é o nome apresentado no convite, bem como o nome que aparece no diretório do Azure AD da Contoso. O **InviteContactUsUrl** é uma forma de a Alice contactar a Contoso. No exemplo que se segue, InviteContactUsUrl especifica o perfil do LinkedIn da Contoso. É importante escrever as etiquetas da primeira linha do ficheiro .csv exatamente conforme especificado na [referência de formatos de ficheiro CSV](active-directory-b2b-references-csv-file-format.md).  
   ![Exemplo de ficheiro CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)
2. No Portal do Azure, adicione um utilizador ao diretório da Contoso (Active Directory > Contoso > Utilizadores > Adicionar Utilizador). Na lista pendente “Tipo de Utilizador”, selecione “Utilizadores em empresas associadas”. Carregue o ficheiro .csv. Certifique-se de que o ficheiro .csv está fechado antes de carregar.  
   ![Carregamento do ficheiro CSV para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)
3. A Alice é agora representada como um utilizador externo no diretório do Azure AD da Contoso.  
   ![Alice listada no Azure AD](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)
4. Alice recebe o seguinte e-mail.  
   ![E-mail de convite de Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)
5. A Alice clica na ligação e é-lhe pedido para aceitar o convite e iniciar sessão com as credenciais de trabalho dela. Se a Alice não estiver no diretório do Azure AD, ser-lhe-á pedido para se inscrever.  
   ![Inscrever-se após o convite para Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)
6. Alice é redirecionada para o Painel de Acesso da Aplicação, vazia até lhe ser concedido o acesso às aplicações.  
   ![Painel de Acesso de Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Este procedimento é a forma mais simples de colaboração B2B. Como utilizador no diretório do Azure AD da Contoso, poderá ser concedido a Alice acesso às aplicações e aos grupos através do Portal do Azure. Agora, vamos adicionar Bernardo, que tem de aceder a aplicações da Moodle e da Salesforce.

## Adicionar Bernardo ao diretório da Contoso e conceder acesso às aplicações
1. Utilize o Windows PowerShell com o Módulo do Azure AD instalado para localizar os IDs da aplicação da Moodle e da Salesforce. Os IDs podem ser obtidos utilizando o cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Isto apresenta uma lista de todas as aplicações disponíveis na Contoso e os respetivos AppPrincialIds.  
   ![Obter IDs para o Bernardo](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)
2. Crie um ficheiro .csv com o e-mail e DisplayName do Bernardo, **InviteAppID**, **InviteAppResources** e InviteContactUsUrl. Povoe **InviteAppResources** com os AppPrincipalIds da Moodle e da Salesforce localizados no PowerShell, separados por um espaço. Povoe **InviteAppId** com o mesmo AppPrincipalId da Moodle para criar marca no e-mail e assinar as páginas com o logótipo da Moodle.  
   ![Exemplo de ficheiro CSV para Bernardo](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)
3. Carregue o ficheiro .csv através do Portal do Azure, tal como foi feito para Alice. Bernardo é agora um utilizador externo no diretório do Azure AD da Contoso.
4. Bernardo recebe o seguinte e-mail.  
   ![E-mail de convite de Bernardo](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)
5. Bernardo clica na ligação e é-lhe pedido para aceitar o convite. Depois de ter iniciado sessão, é direcionado para o Painel de Acesso e pode já utilizar a Moodle e a Salesforce.  
   ![Painel de Acesso para Bernardo](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

De seguida, vamos adicionar Camila, que tem de aceder a aplicações e precisa de associação a grupos no diretório da Contoso.

## Adicionar Camila ao diretório da Contoso, concedendo acesso às aplicações e atribuindo associação a grupos
1. Utilize o Windows PowerShell com o Módulo do Azure AD instalado para localizar os IDs da aplicação e os IDs de grupo dentro da Contoso.
   
   * Obtenha o AppPrincipalId utilizando o cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, tal como procedeu para Bernardo
   * Obtenha o ObjectId de grupos utilizando o cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Será apresentada uma lista de todos os grupos na Contoso e os respetivos ObjectIds. Os IDs de Grupo também podem ser obtidos como o ID de Objeto no separador Propriedades do grupo no Portal do Azure.  
     ![Obter IDs e grupos para Camila](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)
2. Crie o ficheiro .csv, povoando o e-mail, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources** e InviteContactUsUrl de Camila. **InviteGroupResources** é povoado com os ObjectIds dos grupos MyGroup1 e Externals, separados por um espaço.  
   ![Exemplo de ficheiro CSV para Camila](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)
3. Carregue o ficheiro. csv através do Portal do Azure.
4. Camila é um utilizador no diretório da Contoso e também é membro dos grupos MyGroup1 e Externals, conforme visto no Portal do Azure.  
   ![Camila encontra-se listada num grupo no Azure AD](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)
5. Camila recebe um e-mail com uma ligação para aceitar o convite. Depois de iniciar sessão, é redirecionada para o Painel de Acesso da Aplicação para poder aceder à Moodle e à Salesforce.  

Não há mais passos a executar para adicionar utilizadores a partir de empresas associadas na colaboração B2B do Azure AD. Estas instruções mostraram como adicionar os utilizadores Alice, Bernardo e Camila ao diretório da Contoso utilizando três ficheiros .csv separados. É possível facilitar este processo condensando os ficheiros .csv separados num único ficheiro.  
![Exemplo de ficheiro CSV para Alice, Bernardo e Camila](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Artigos relacionados
Consulte os nossos outros artigos sobre a colaboração B2B do Azure AD:

* [O que é a colaboração B2B do Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como funciona](active-directory-b2b-how-it-works.md)
* [Referência de formatos de ficheiro CSV](active-directory-b2b-references-csv-file-format.md)
* [Formato de token de utilizador externo](active-directory-b2b-references-external-user-token-format.md)
* [Alterações de atributos de objetos do utilizador externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limitações atuais da pré-visualização](active-directory-b2b-current-preview-limitations.md)
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)

<!--HONumber=ago16_HO5-->


