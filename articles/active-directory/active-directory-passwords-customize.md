---
title: 'Personalizar: O Azure AD SSPR | Microsoft Docs'
description: "Reposição de palavra-passe self-service do Azure AD, as opções de personalização"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar a funcionalidade do Azure AD para a reposição de palavra-passe self-service

Profissionais de TI que pretendem implementar a reposição de palavra-passe self-service (SSPR) no Azure Active directory (Azure AD), podem personalizar a experiência para corresponder as respetivas necessidades dos utilizadores.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar a hiperligação "Contacte o administrador"

Mesmo se SSPR não estiver ativada, os utilizadores ainda tem uma ligação de "Contacte o administrador" na palavra-passe portal de reposição. Se um utilizador seleccionar esta ligação,-lo a:
   * Os administradores de mensagens de correio eletrónico e pede-lhe para obter ajuda na alteração da palavra-passe do utilizador. 
   * Envia os seus utilizadores para um URL que especificar para obter assistência. 

Recomendamos que defina esta contacte para algo como um endereço de e-mail ou Web site que já utilizam os seus utilizadores para questões de suporte.

![Contacte][Contact]

O correio de eletrónico de contacto é enviado para os seguintes destinatários pela seguinte ordem:

1. Se o **administrador de palavras-passe** função é atribuída, os administradores de com esta função são notificados.
2. Se não administradores de palavras-passe forem atribuídos, em seguida, os administradores de com o **administrador utilizador** função são notificados.
3. Se nenhuma das funções anteriores estão atribuídas, em seguida, a **administradores globais** são notificados.

Em todos os casos, um máximo de 100 destinatários são notificados.

Para saber mais sobre as funções de administrador diferentes e como atribui-las, consulte o artigo [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Desativar e-mails "Contacte o administrador"

Se a sua organização pretender notificar os administradores sobre palavras-passe repor pedidos, pode ativar a seguinte configuração:

* Ative self-service reposição palavra-passe para todos os utilizadores finais. Esta opção está sob **reposição de palavra-passe** > **propriedades**.
  
  Se não quiser que os utilizadores reponham as suas próprias palavras-passe, pode definir o âmbito de acesso a um grupo vazio. *Não recomendamos esta opção.*
* Personalizar a hiperligação de suporte técnico para fornecer um URL de web ou mailto: endereço que os utilizadores podem utilizar para obter assistência. Esta opção está sob **reposição de palavra-passe** > **personalização** > **correio eletrónico de suporte técnico personalizado ou URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personalizar a página de início de sessão do AD FS para SSPR

Administradores de serviços de Federação do Directory (AD FS) ativos podem adicionar uma ligação à sua página de início de sessão com as orientações encontrada no [descrição da página de início de sessão de adicionar](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) artigo.

Para adicionar uma ligação para a página de início de sessão do AD FS, utilize o seguinte comando no servidor do AD FS. Os utilizadores podem utilizar esta página para introduzir o fluxo de trabalho SSPR.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personalizar o início de sessão acesso e de página painel aspeto e funcionalidade

Pode personalizar a página de início de sessão. Pode adicionar um logótipo que é apresentada juntamente com a imagem que se adequa à sua imagem corporativa da empresa.

Os gráficos que escolher são mostrados nas seguintes circunstâncias:

* Depois de um utilizador introduz o nome de utilizador
* Se o utilizador acede o URL personalizado:
    * Transferindo o *whr* página, tal como "https://login.microsoftonline.com/?whr=contoso.com" Repor o parâmetro para a palavra-passe
    * Transferindo o *username* parâmetro para a palavra-passe de reposição de página, tal como "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Detalhes de gráficos

Utilize as seguintes definições para alterar as características visual da página de início de sessão. Aceda a **do Azure Active Directory** > **da empresa de imagem corporativa** > **Editar imagem corporativa**:

* A imagem da página de início de sessão deve ser um ficheiro. png ou. jpg, 1420 x 1200 pixéis e não superior a 500 KB. Para obter os melhores resultados, recomendamos que mantenha-cerca de 200 KB.
* A cor de fundo da página de início de sessão é utilizada em ligações de latência alta e tem de estar no formato hexadecimal RGB.
* A imagem de faixa deve ser um ficheiro. png ou. jpg, 60 x 280 pixels e ser não superior a 10 KB.
* O logótipo quadrado (tema normal e um escuro) deve ser um ficheiro. png ou. jpg, 240 x 240 pixels (redimensionáveis) e não superior a 10 KB.

### <a name="sign-in-text-options"></a>Opções de início de sessão de texto

Utilize as seguintes definições para adicionar o texto para a página de início de sessão é relevante para a sua organização. Aceda a **do Azure Active Directory** > **da empresa de imagem corporativa** > **Editar imagem corporativa**:

* **Sugestão de nome de utilizador**: substitui o texto de exemplo do  *someone@example.com*  com algo mais adequado para os seus utilizadores. Recomendamos que deixe a sugestão de predefinição quando suportam a utilizadores internos e externos.
* **Texto da página de início de sessão**: pode ter um máximo de 256 carateres de comprimento. Este texto é apresentado em qualquer local que aos utilizadores iniciarem sessão online e de que a experiência de associação de área de trabalho do Azure AD no Windows 10. Utilize este texto para os termos de utilização, instruções e sugestões para os seus utilizadores. 

   >[!IMPORTANT]
   >Todos podem ver a página de início de sessão, pelo que não forneça quaisquer informações sensíveis aqui.
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>A definição "Keep-me sessão desativada"

Com o **manter a minha sessão iniciada desativado** opção, os utilizadores podem permanecer com sessão iniciada quando fechar e reabrir a respetiva janela do browser. Esta opção não afeta a duração da sessão. Aceda a **do Azure Active Directory** > **da empresa de imagem corporativa** > **Editar imagem corporativa**.

Algumas funcionalidades do SharePoint Online e Office 2010 tem uma dependência na capacidade dos próprios utilizadores para selecionar esta caixa de verificação. Se ocultar esta opção, os utilizadores podem obter adicionais e inesperados início de sessão avisos.

### <a name="directory-name"></a>Nome do diretório

Pode alterar o atributo de nome de diretório em **do Azure Active Directory** > **propriedades**. Pode mostrar um nome de organização amigável que é utilizado no portal e o comunicações automatizada. Esta opção é a mais visível no correio eletrónico automatizado em formulários que se seguem:

* O nome amigável do e-mail, por exemplo "Microsoft em nome de demonstração CONTOSO"
* A linha de assunto do e-mail, por exemplo "CONTOSO demonstração conta e-mail código de verificação"

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
* [Tem uma pergunta licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Contacte o administrador para obter ajuda a repor o seu exemplo de e-mail palavras-passe"
