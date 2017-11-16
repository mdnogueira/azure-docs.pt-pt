---
title: 'Personalizar: O Azure AD SSPR | Microsoft Docs'
description: "Opções de personalização do Azure AD Self-repor a palavra-passe do serviço"
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
ms.openlocfilehash: f2b172208185e343c9c10d55036c20d60346778c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>Personalizar funcionalidades do Azure AD para a reposição da palavra-passe Self-Service

Os profissionais de TI que querem para implementar a reposição de palavra-passe self-service podem personalizar a experiência para corresponder aos seus utilizadores.

## <a name="customize-the-contact-your-administrator-link"></a>Personalizar o contacto a ligação de administrador

Mesmo que não está ativado SSPR utilizadores ainda um "Contacte o administrador" ligar sobre a palavra-passe portal de reposição.  Ao clicar nesta ligação, os administradores de solicitar assistência na alteração da palavra-passe do utilizador de mensagens de correio eletrónico ou envia os seus utilizadores para um URL que especificar. Recomendamos que defina esta para algo como um endereço de e-mail ou Web site que os utilizadores são utilizados para a utilizar para o suporte.

![Contacte][Contact]

Este e-mail é enviado para os seguintes destinatários pela seguinte ordem:

1. Se o **administrador de palavras-passe** função é atribuída, os administradores de com esta função são notificados
2. Se não administradores de palavras-passe forem atribuídos, em seguida, os administradores de com o **administrador utilizador** função são notificados
3. Se nenhuma das funções anteriores foram atribuídas, em seguida, **administradores globais** são notificados

Em todos os casos, um máximo de 100 destinatários são notificados.

Para obter mais informações sobre o administrador de diferentes funções e atribuí-las, consulte o documento [atribuir funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

### <a name="disable-contact-your-administrator-emails"></a>Desativar contacte aos e-mails de administrador

Se a sua organização pretende que os administradores notificados sobre palavras-passe repor pedidos, a seguinte configuração pode ser ativada

* Ative self-service reposição palavra-passe para todos os utilizadores finais. Esta opção está sob **reposição de palavra-passe > propriedades**.
    * Se não desejar que os utilizadores reponham as suas próprias palavras-passe, pode definir o âmbito de acesso a um grupo vazio **não recomendamos esta opção**.
* Personalizar a hiperligação de suporte técnico para fornecer um URL de web ou mailto: endereço que os utilizadores podem utilizar para obter assistência. Esta opção está sob **reposição de palavra-passe > personalização > correio eletrónico de suporte técnico personalizado ou URL**.

## <a name="customize-adfs-sign-in-page-for-sspr"></a>Personalizar a página de início de sessão de ADFS para SSPR

Os administradores do AD FS pode adicionar uma ligação à respetiva página de início de sessão utilizando as orientações encontrada no artigo [descrição da página de início de sessão de adicionar](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Utilizando o comando que se segue no seu servidor do ADFS adiciona uma ligação para a página de início de sessão do AD FS, permitindo que os utilizadores introduzam a palavra-passe self-service fluxo de trabalho de reposição diretamente.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>Personalizar o início de sessão e acesso painel aspeto e funcionalidade

Quando os utilizadores acedem a página de início de sessão, pode personalizar o logótipo que é apresentada juntamente com a imagem da página de início de sessão para se ajustar a sua imagem corporativa da empresa.

Estes gráficos são mostrados nas seguintes circunstâncias:

* Depois de um utilizador escreve o respetivo nome de utilizador
* Utilizador acede ao url personalizado
    * Reposição de página, tal como "https://login.microsoftonline.com/?whr=contoso.com" transferindo "whr" parâmetro para a palavra-passe
    * Através da transmissão de "nomedeutilizador" parâmetro para a palavra-passe de reposição de página, tal como "https://login.microsoftonline.com/?username=admin@contoso.com"

### <a name="graphics-details"></a>Detalhes de gráficos

As seguintes definições permitem-lhe alterar as visual características da página de início de sessão e pode ser encontradas na **do Azure Active Directory**, **da empresa de imagem corporativa**, **Editar imagem corporativa**

* Imagem da página de início de sessão deve ser um pixels de ficheiro são 1420 x 1200 PNG ou JPG e não com mais de 500KB. Recomendamos que seja cerca de 200 KB para obter os melhores resultados.
* Cor de fundo da página de início de sessão é utilizada quando em ligações de latência alta e tem de estar no formato hexadecimal RGB.
* Imagem de faixa deve ser um pixels de ficheiro 60 x 280 PNG ou JPG e não superior a 10 KB.
* Logótipo quadrado (tema normal e um escuro) PNG ou JPG 240 x 240 (redimensionável) não superior a 10 KB.

### <a name="sign-in-text-options"></a>Opções de início de sessão de texto

As seguintes definições permitem-lhe adicionar texto de início de sessão relevantes para a sua organização. Estas definições podem ser encontradas em **do Azure Active Directory**, **da empresa de imagem corporativa**, **Editar imagem corporativa**

* **Sugestão de nome de utilizador** substitui o texto de exemplo do someone@example.com com algo mais adequado para os seus utilizadores, recomendamos que para ficar predefinido ao suportar utilizadores internos e externos
* **Texto da página de início de sessão** um máximo de 256 carateres de comprimento. Este texto é apresentado em qualquer lugar o início de sessão de utilizadores online e a experiência de associação do Azure AD no Windows 10. Utilize este texto de termos de utilização, instruções e sugestões para os seus utilizadores. **Todos podem ver a página início de sessão para que não fornecem informações confidenciais aqui.**

### <a name="keep-me-signed-in-disabled"></a>Manter a minha sessão iniciada desativado

A opção "Manter-me assinado desativado" permite aos utilizadores permanecer com sessão iniciada quando fechar e reabrir a respetiva janela do browser. Esta opção não afeta as durações de sessão. Esta definição encontra-se em **do Azure Active Directory > da empresa de imagem corporativa > Editar imagem corporativa**.

Algumas funcionalidades do SharePoint Online e Office 2010 tem uma dependência sobre os utilizadores a capacidade para esta caixa de verificação. Se ocultar esta opção, os utilizadores podem aceder adicionais e inesperados início de sessão avisos.

### <a name="directory-name"></a>Nome do diretório

Pode alterar o atributo de nome em **do Azure Active Directory > propriedades** para mostrar um nome de organização amigável visto nas comunicações do portais e automatizadas. Esta opção é mais visível no formato de correio eletrónico automatizado em formulários que se seguem

* Nome amigável no e-mail "Microsoft em nome de demonstração CONTOSO"
* Linha de assunto do e-mail "CONTOSO demonstração conta e-mail código de verificação"

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](active-directory-passwords-best-practices.md)
* [Reponha ou altere a palavra-passe](active-directory-passwords-update-your-own-password.md).
* [Registe-se na reposição personalizada de palavras-passe](active-directory-passwords-reset-register.md).
* [Tem alguma pergunta sobre Licenciamento?](active-directory-passwords-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](active-directory-passwords-data.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política da SSPR?](active-directory-passwords-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](active-directory-passwords-writeback.md)
* [Como posso comunicar a atividade da SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](active-directory-passwords-how-it-works.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Contacte o administrador para obter ajuda a repor o seu exemplo de e-mail palavras-passe"