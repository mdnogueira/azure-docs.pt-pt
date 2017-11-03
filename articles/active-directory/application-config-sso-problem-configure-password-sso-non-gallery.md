---
title: "Problema de configuração de palavra-passe-início de sessão único para uma aplicação não Galeria | Microsoft Docs"
description: "Compreender a letra de pessoas problemas comuns quando configurar a palavra-passe-início de sessão único para aplicações não Galeria personalizadas que não estão listados na Galeria de aplicações do Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9c76b6f3495e2dd759a156fcef97b57aece8d632
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Configurar a palavra-passe-início de sessão único para uma aplicação não galeria do problema

Este artigo ajuda-o a compreender a letra de pessoas problemas comuns quando configurar **palavra-passe-início de sessão único** com uma aplicação não galeria.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Como capturar campos de início de sessão para uma aplicação

Captura de início de sessão no campo só é suportada para ativada de HTML páginas de início de sessão e é **não suportada para as páginas de início de sessão não padrão**, como aqueles que utiliza Flash ou outras tecnologias não ativado HTML.

Existem duas formas pode capturar o início de sessão campos para as suas aplicações personalizadas:

-   Captura de campo de início de sessão automático

-   Captura de campo de início de sessão manual

**Captura de campo de início de sessão automático** funciona bem com a maioria das ativada de HTML início de sessão páginas, caso utilizem **conhecidos DIV IDs para o nome de utilizador e palavra-passe de entrada** campo. É a forma como isto funciona por scraping HTML na página para localizar os IDs de DIV que correspondem a determinados critérios e, em seguida, guardar esses metadados para esta aplicação, pelo que pode reproduzir palavras-passe ao mesmo mais tarde.

**Captura de campo de início de sessão manual** pode ser utilizado no caso de que a aplicação **fornecedor não etiqueta** campos de entrada utilizados para iniciar sessão. Captura de campo de início de sessão manual, também pode ser utilizada no caso quando o **fornecedor composições vários campos** que não pode ser automaticamente detetados. Azure AD pode armazenar dados para tantos campos que estão na página de início de sessão, desde que diga-nos onde os campos estão na página.

Em geral, **se captura do campo de início de sessão automático não funcionar, sugerimos sempre a opção manual a tentar.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Como capturar automaticamente os campos de início de sessão para uma aplicação

Para configurar **baseada em palavra-passe Single Sign-on** para uma aplicação utilizando **captura do campo de início de sessão automático**, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. **Certifique-se de que os campos de início de sessão está visível no URL que fornecer**.

10. Clique no botão **Guardar**.

11. Depois, fazê-lo, iremos irá scrape automaticamente esse URL para um nome de utilizador e palavra-passe caixa de entrada e permite a utilização do Azure AD de forma segura transmitir palavras-passe para essa aplicação utilizando a extensão de browser do painel de acesso.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Como capturar manualmente os campos de início de sessão para uma aplicação

Para capturar manualmente os campos de início de sessão, primeiro tem de ter a extensão de Browser do painel de acesso instalada e **não estar em execução no modo privado, inPrivate ou incognito.** Para instalar a extensão de browser, siga os passos a [como instalar a extensão de Browser do painel de acesso](#i-cannot-manually-detect-sign-in-fields-for-my-application) secção.

Para configurar **baseada em palavra-passe Single Sign-on** para uma aplicação utilizando **captura do campo de início de sessão manual**, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione o modo **baseada em palavra-passe de início de sessão.**

9.  Introduza o **URL de início de sessão**. Este é o URL onde os utilizadores introduzem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. **Certifique-se de que os campos de início de sessão está visível no URL que fornecer**.

10. Clique no botão **Guardar**.

11. Depois, fazê-lo, iremos irá scrape automaticamente esse URL para um nome de utilizador e palavra-passe caixa de entrada e permite a utilização do Azure AD de forma segura transmitir palavras-passe para essa aplicação utilizando a extensão de browser do painel de acesso. No caso isto falhar, pode **alterar o modo de início de sessão para utilizar a captura de campo de início de sessão manual** ao continuar para o passo 12.

12. Clique em **configurar &lt;appname&gt; as definições de início de sessão único de palavra-passe**.

13. Selecione o **detetar manualmente os campos de início de sessão** opção de configuração.

14. Clique em **OK**.

15. Clique em **Guardar**.

16. Siga as instruções do ecrã on para utilizar o painel de acesso.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Vejo um erro "Não foi possível encontrar quaisquer campos de início de sessão, esse URL"

Ver este erro quando a deteção automática dos campos de início de sessão falha. Para resolver este problema, tente deteção manual campo de início de sessão, seguindo os passos a [como capturar manualmente os campos de início de sessão para uma aplicação](#how-to-manually-capture-sign-in-fields-for-an-application) secção.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Vejo uma "não é possível guardar a configuração do início de sessão" erro

Em certos casos raros, pode efetuar a atualizar a configuração de início de sessão único. Para resolver isto tente guardar a configuração única de início de sessão novamente.

Se isto continua a falharem de forma consistente, abra um incidente de suporte e forneça as informações recolhidas [como ver os detalhes de uma notificação de portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) secções.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Posso manualmente não consegue detetar sessão nos campos para a minha aplicação

Alguns dos comportamentos que poderá ver quando a deteção manual não está a funcionar incluem:

-   O processo de captura manual apareceu funcionar, mas os campos capturados não foram corretos

-   Os campos de direito não obter realçados quando executar o processo de captura

-   O processo de captura demora-me a página de início de sessão da aplicação conforme esperado, mas não acontece nada

-   Captura manual é apresentada a funcionar, mas o SSO não ocorre quando os meus utilizadores navegarem para a aplicação a partir do painel de acesso.

Verifique o seguinte se tiver algum destes problemas:

-   Certifique-se de que tem a versão mais recente da extensão de browser do painel de acesso **instalado** e **ativada** seguindo os passos a [como instalar a extensão de Browser do painel de acesso](#how-to-install-the-access-panel-browser-extension) secção.

-   Certifique-se de que não são repetir o processo de captura ao seu browser em **modo privado, inPrivate ou incognito**. A extensão do painel de acesso não é suportada nestes modos.

-   Certifique-se de que os utilizadores não estão a tentar iniciar sessão para a aplicação do painel de acesso no **modo privado, inPrivate ou incognito**. A extensão do painel de acesso não é suportada nestes modos.

-   Repita o processo de captura manual novamente, garantindo que os marcadores de vermelhos são os campos corretos.

-   Se o processo de captura manual parece fique suspensa ou o página de início de sessão não faz nada (caso 3 acima), repita o processo de captura manual. But, desta vez depois de concluir o processo, prima a **F12** botão para abrir a consola de programador do seu browser. Uma vez, abra o **consola** e tipo **window.location= "&lt;introduzir o início de sessão no url especificado quando configurar a aplicação&gt;"** e, em seguida, prima **Enter**. Este impor uma página redirecionar que termina o processo de captura e armazenar os campos que tenham sido capturados.

Se nenhuma destas abordagens resolver o problema, podemos ajudar. Abrir um incidente de suporte com os detalhes do que tentou, bem como as informações recolhidas [como ver os detalhes de uma notificação de portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) secções (se aplicável).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão de Browser do painel de acesso

Para instalar a extensão de Browser do painel de acesso, siga os passos abaixo:

1.  Abra o [painel de acesso](https://myapps.microsoft.com) dos browsers suportados e início de sessão como um **utilizador** no seu Azure AD.

2.  Clique num **aplicação de palavra-passe SSO** no painel de acesso.

3.  Na linha de pedir instalar o software, selecione **instalar agora**.

4.  Com base no seu browser, direcionado para a hiperligação de transferência. **Adicionar** a extensão para o seu browser.

5.  Se o browser pede-lhe, selecione como **ativar** ou **permitir** a extensão.

6.  Uma vez instalado, **reiniciar** a sessão do browser.

7.  Inicie sessão no painel de acesso e veja se pode **iniciar** as aplicações de SSO de palavra-passe.

Também pode transferir a extensão para Chrome e Firefox a partir de hiperligações diretas abaixo:

-   [Extensão de painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão de painel de acesso de Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal

Pode ver os detalhes de qualquer notificação do portal, seguindo os passos abaixo:

1.  Clique em de **notificações** ícone (de sino) no canto superior direito do Portal do Azure

2.  Selecione qualquer notificação num **erro** Estado (as com um vermelho (!) junto ao-las).

  >! Tenha em atenção] não é possível clique as notificações de um **Successful** ou **em curso** estado.
  >
  >

3.  Este abra o **detalhes de notificação** painel.

4.  Utilize estas informações por si para compreender a obter mais detalhes sobre o problema.

5.  Se ainda precisar de ajuda, também pode partilhar estas informações com um engenheiro de suporte técnico ou o grupo do produto para obter ajuda com o seu problema.

6.  Clique no **cópia** **ícone** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo do produto ou de suporte.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda com o envio de detalhes de notificação para um engenheiro de suporte

É muito importante que partilha **todos os detalhes apresentados abaixo** com um engenheiro de suporte se precisar de ajuda, para que o podem ajudar a rapidamente. Pode fazê-lo facilmente por **tirar uma captura de ecrã,** ou clicando a **ícone de erro de cópia**, encontrado para a direita do **copiar erro** caixa de texto.

## <a name="notification-details-explained"></a>Detalhes de notificação explicados

O abaixo explica mais que cada da notificação significa que os itens e fornece exemplos de cada um deles.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

-   **Título** – o título descritivo da notificação

    -   Exemplo – **as definições de proxy de aplicações**

-   **Descrição** – a descrição, o que ocorreu em resultado da operação

    -   Exemplo – **interno url introduzido já está a ser utilizado por outra aplicação**

-   **Id de notificação** – o id exclusivo da notificação

    -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Id do pedido de cliente** – o id do pedido específico efetuado pelo browser

    -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Hora UTC de carimbo de data /** – timestamp durante os quais a notificação ocorreu, em UTC

    -   Exemplo – **2017-03-23T19:50:43.7583681Z**

-   **Id de transação interno** – o ID interno podemos utilizar ao procurar o erro do nosso sistemas

    -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – o utilizador que executou a operação

    -   Exemplo –**tperkins@f128.info**

-   **Id de inquilino** – o ID exclusivo do inquilino que o utilizador que executou a operação foi membro de

    -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Id de objeto de utilizador** – o ID exclusivo do utilizador que executou a operação

    -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhada

-   **Nome a apresentar** – **(pode estar vazio)** mais detalhado nome a apresentar para o erro

    -   Exemplo * – **as definições de proxy de aplicações**

-   **Estado** – o estado específico da notificação

    -   Exemplo * – **falhou**

-   **Id de objeto** – **(pode estar vazio)** o ID de objeto relativamente ao qual foi efetuada a operação

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – a descrição, o que ocorreu em resultado da operação de detalhado

    -   Exemplo – **url interno 'http://bing.com/' é inválido porque já se encontra em utilização**

-   **Copiar erro** – clique no **ícone copiar** à direita do **copiar erro** caixa de texto para copiar todos os detalhes de notificação para partilhar com um engenheiro de grupo de suporte ou produto

    -   Exemplo –```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)

