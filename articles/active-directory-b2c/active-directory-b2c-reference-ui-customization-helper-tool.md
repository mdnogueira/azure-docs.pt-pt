---
title: "Do Azure Active Directory B2C: Ferramenta de programa auxiliar de personalização de página IU | Microsoft Docs"
description: "Uma ferramenta de programa auxiliar utilizada para demonstrar a funcionalidade da página IU personalização no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
ms.openlocfilehash: e0c2d827553567ddbc7d006192dc35574e66f1cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>O Azure Active Directory B2C: Uma ferramenta auxiliar utilizada para demonstrar a funcionalidade de personalização de interface (IU) de utilizador de página
Este artigo é um complemento para a [principal artigo de personalização de IU](active-directory-b2c-reference-ui-customization.md) no Azure Active Directory (Azure AD) B2C. Os passos seguintes descrevem como para a funcionalidade da página IU personalização utilizando o conteúdo HTML e CSS de exemplo que fornecemos.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obter um inquilino do Azure AD B2C
Antes de pode personalizar qualquer coisa, será necessário [obter um inquilino do Azure AD B2C](active-directory-b2c-get-started.md) se ainda não tiver um.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Criar uma política de inscrição ou início de sessão
O conteúdo de exemplo que fornecemos pode ser utilizado para customze duas páginas de uma [política de inscrição ou início de sessão](active-directory-b2c-reference-policies.md): o [unificada página de início de sessão](active-directory-b2c-reference-ui-customization.md) e o [página Self-permitido atributos](active-directory-b2c-reference-ui-customization.md). Quando [criar a política de inscrição ou início de sessão](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), adicione a conta Local (endereço de e-mail), Facebook, Google e Microsoft como **fornecedores de identidade**. Estes são os IDPs apenas que o nosso conteúdo HTML de exemplo irá aceitar.  Também pode adicionar um subconjunto destas IDPs se desejar.

## <a name="register-an-application"></a>Registar uma aplicação
Terá [registar uma aplicação](active-directory-b2c-app-registration.md) no inquilino do B2C que pode ser utilizado para executar a sua política. Depois de registar a sua aplicação, tem algumas opções que pode utilizar para executar, na verdade, a política de inscrição:

* Criar um Azure AD B2C aplicações de início rápido listadas na secção "Introdução" [inscrever-se e iniciar sessão em consumidores nas suas aplicações](active-directory-b2c-overview.md#get-started).
* Utilize o pré-criadas [do Azure AD B2C Playground](https://aadb2cplayground.azurewebsites.net) aplicação. Se optar por utilizar o playground, tem de registar uma aplicação no seu inquilino do B2C utilizando o **URI de redirecionamento** `https://aadb2cplayground.azurewebsites.net/`.
* Utilize o **executar agora** botão na sua política do [portal do Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalizar a política
Para personalizar o aspeto e funcionalidade da sua política, tem primeiro de criar ficheiros HTML e CSS, utilizando as convenções de específicas do Azure AD B2C. Em seguida, pode carregar o conteúdo estático para uma localização publicamente disponível para que o Azure AD B2C consegue aceder-lhe. Isto pode ser o próprio servidor web dedicado, Blob Storage do Azure, rede de entrega de conteúdos do Azure ou quaisquer outra estático que aloja o recurso de fornecedor. Os únicos requisitos são se o seu conteúdo está disponível através de HTTPS e pode ser acedido através de CORS. Assim que tiver exposta o conteúdo estático na web, pode editar a política para apontar para esta localização e apresentar esse conteúdo aos seus clientes. O [principal artigo de personalização de IU](active-directory-b2c-reference-ui-customization.md) descreve em detalhe como funciona a funcionalidade de personalização do Azure AD B2C.

Para efeitos deste tutorial, iremos tiver já criado algum conteúdo de exemplo e está lojado-lo no Blob Storage do Azure. O conteúdo de exemplo é uma personalização muito básica no tema da nossa empresa fictícias, "No diretório Wingtip Toys". Para experimentar na sua própria política, siga estes passos:

1. Inicie sessão no seu inquilino no [portal do Azure](https://portal.azure.com/) e navegue para o painel de funcionalidades do B2C.
2. Clique em **políticas de inscrição ou início de sessão** e, em seguida, clique em sua política (por exemplo, "b2c\_1\_sessão\_cópias de segurança\_sessão\_no").
3. Clique em **personalização de página IU** e, em seguida, **unificado a página de inscrição ou início de sessão**.
4. Ativar/desativar a **página personalizada utilize** mudar para **Sim**. No **página personalizada URI** campo, introduza `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Clique em **OK**.
5. Clique em **página de inscrição de conta Local**. Ativar/desativar a **modelo personalizado utilize** mudar para **Sim**. No **página personalizada URI** campo, introduza `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
6. Repita o passo mesmo para o **página de inscrição de redes sociais conta**.
   Clique em **OK** duas vezes para fechar os painéis de personalização de IU.
7. Clique em **Guardar**.

Agora pode experimentar a política personalizada. Pode utilizar a sua própria aplicação ou o Azure AD B2C playground se quiser, mas pode também simplesmente clicar o **executar agora** comando no painel da política. Selecione a aplicação na caixa pendente e escolha o URI de redirecionamento adequado. Clique em de **executar agora** botão. Um novo separador do browser será aberto e pode percorrer a experiência de utilizador de inscrever-se para a sua aplicação com o conteúdo novo assegurados!

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Carregar o conteúdo de exemplo para o Blob Storage do Azure
Se gostaria de utilizar o Blob Storage do Azure para alojar o conteúdo da página, pode criar a sua própria conta de armazenamento e utilizar o nosso ferramenta auxiliar de B2C para carregar os ficheiros.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **+ novo** > **dados + armazenamento** > **conta de armazenamento**. Precisa de uma subscrição do Azure para criar uma conta do Blob Storage do Azure. Pode inscrever numa avaliação gratuita no [Web site Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Forneça um **nome** para o armazenamento de conta (por exemplo, "contoso") e escolha as seleções adequadas para **escalão de preço**, **grupo de recursos** e  **Subscrição**. Certifique-se de que tem o **afixar ao Startboard** opção selecionada. Clique em **Criar**.
4. Volte ao Startboard e clique na conta de armazenamento que acabou de criar.
5. No **resumo** secção, clique em **contentores**e, em seguida, clique em **+ adicionar**.
6. Forneça um **nome** para o contentor (por exemplo, "b2c") e selecione **Blob** como o **aceder tipo**. Clique em **OK**.
7. O contentor que criou serão apresentados na lista no **Blobs** painel. Anote o URL do contentor; Por exemplo, deve ser semelhante `https://contoso.blob.core.windows.net/b2c`. Fechar o **Blobs** painel.
8. No painel de conta de armazenamento, clique em **chaves** e anote os valores de **nome da conta de armazenamento** e **chave de acesso primária** campos.

> [!NOTE]
> **Chave de acesso primária** é uma credencial de segurança importantes.
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>Transferir os ficheiros de programa auxiliar ferramenta e exemplos
Pode transferir o [Blob Storage do Azure ajuda ferramenta e exemplos ficheiros como um ficheiro. zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) ou cloná-la a partir do GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositório inclui um `sample_templates\wingtip` diretório, que contém o exemplo HTML, CSS e imagens. Para estes modelos fazer referência a sua própria conta do Blob Storage do Azure, terá de editar os ficheiros HTML. Abra `unified.html` e `selfasserted.html` e substituir quaisquer instâncias de `https://localhost` com o URL do seu próprio contentor que escreveu para baixo nos passos anteriores. Tem de utilizar o caminho absoluto dos ficheiros HTML porque neste caso, o HTML será servido pelo Azure AD, sob o domínio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Carregar os ficheiros de exemplo
No mesmo repositório, deszipe `B2CAzureStorageClient.zip` e execute o `B2CAzureStorageClient.exe` dentro do ficheiro. Este programa simplesmente carregar todos os ficheiros no diretório que especificar para a sua conta do storage e ativar o acesso CORS para esses ficheiros. Se seguiu os passos acima, os ficheiros HTML e CSS agora irão apontar para a sua conta de armazenamento. Tenha em atenção que o nome da sua conta de armazenamento é a parte que precede `blob.core.windows.net`; por exemplo, `contoso`. Pode verificar que o conteúdo foi carregado corretamente ao tentar aceder `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` num browser. Também utilizar [http://test-cors.org/](http://test-cors.org/) para se certificar de que o conteúdo está agora CORS ativada. (Procure "Estado XHR: 200" nos resultados.)

### <a name="customize-your-policy-again"></a>Personalizar a política novamente
Agora que carregar o conteúdo de exemplo para a sua própria conta de armazenamento, tem de Editar política de inscrição referenciá-la. Repita os passos do ["Personalizar a política"](#customize-your-policy) secção acima, desta vez utilizando o URL da sua própria conta de armazenamento. Por exemplo, a localização do seu `unified.html` ficheiro seria `<url-of-your-container>/wingtip/unified.html`.

Agora, pode utilizar o **executar agora** botão ou a sua própria aplicação para executar a política novamente. O resultado deverá ter um aspeto quase exatamente iguais – é utilizado o mesmo exemplo HTML e CSS em ambos os casos. No entanto, as políticas estão agora a referenciar a sua própria instância do Blob Storage do Azure e está livres editar e carregar os ficheiros novamente como. Para obter mais informações sobre a personalização de HTML e CSS, consulte o [principal artigo de personalização de IU](active-directory-b2c-reference-ui-customization.md).

