---
title: "Personalizar uma IU ao utilizar políticas personalizadas - Azure AD B2C | Microsoft Docs"
description: "Saiba mais sobre como personalizar uma interface de utilizador (IU) ao utilizar políticas personalizadas no Azure AD B2C."
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeedakhter-msft
ms.openlocfilehash: d5a3c0a323b31696d39e3d2b36317dec3a2337d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>O Azure Active Directory B2C: Configurar a personalização de IU numa política personalizada

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Depois de concluir este artigo, terá uma política personalizada de inscrição e o início de sessão com a marca e do aspeto. Com o Azure Active Directory B2C (Azure AD B2C), lhe controlar quase cheios do conteúdo HTML e CSS que é apresentado aos utilizadores. Quando utilizar uma política personalizada, configure a personalização de IU no ficheiro XML em vez de utilizar os controlos no portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, concluir [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Deve ter uma política personalizada do trabalho para inscrição e inicie sessão com as contas locais.

## <a name="page-ui-customization"></a>Personalização de página IU

Ao utilizar a funcionalidade de personalização de IU da página, pode personalizar o aspeto e funcionalidade de qualquer política personalizada. Também pode manter a marca e consistência visual entre a sua aplicação e o Azure AD B2C.

Eis como funciona: Azure AD B2C executa o código no browser do cliente e utiliza uma abordagem moderna chamada [de partilha de recursos de várias origens (CORS)](http://www.w3.org/TR/cors/). Em primeiro lugar, especifique um URL na política personalizada com conteúdos HTML personalizados. O Azure AD B2C intercala elementos de IU com o conteúdo HTML que é carregado a partir do URL e, em seguida, apresenta a página para o cliente.

## <a name="create-your-html5-content"></a>Criar a sua HTML5 conteúdo

Crie HTML conteúdo com o nome do seu produto marca no título.

1. Copie o seguinte fragmento HTML. É bem formado HTML5 com um elemento vazio denominado  *\<div id = "api"\>\</div\>*  localizados no  *\<corpo\>*  etiquetas. Este elemento indica onde é possível inserir conteúdo do Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >Por motivos de segurança, a utilização de JavaScript está atualmente bloqueada para personalização.

2. Cole o fragmento copiado num editor de texto e, em seguida, guarde o ficheiro como *ui.html personalizar*.

## <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure

>[!NOTE]
> Neste artigo, utilizamos o armazenamento de Blobs do Azure para alojar o nosso conteúdo. Pode optar por receber o conteúdo num servidor web, mas tem [ativar a CORS no seu servidor web](https://enable-cors.org/server.html).

Para alojar este conteúdo HTML no Blob storage, efetue o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No **Hub** menu, selecione **novo** > **armazenamento** > **conta de armazenamento**.
3. Introduza um único **nome** para a sua conta de armazenamento.
4. **Modelo de implementação** pode permanecer **Resource Manager**.
5. Alteração **tipo de conta** para **armazenamento de BLOBs**.
6. **Desempenho** pode permanecer **padrão**.
7. **Replicação** pode permanecer **RA-GRS**.
8. **Camada de acesso** pode permanecer **frequente**.
9. **Encriptação do serviço de armazenamento** pode permanecer **desativado**.
10. Selecione um **subscrição** para a sua conta de armazenamento.
11. Criar um **grupo de recursos** ou selecione um existente.
12. Selecione o **localização geográfica** para a sua conta de armazenamento.
13. Clique em **Criar** para criar a conta do Storage.  
    Depois de concluída a implementação, o **conta de armazenamento** painel abre-se automaticamente.

## <a name="create-a-container"></a>Criar um contentor

Para criar um contentor público no Blob storage, efetue o seguinte:

1. Clique em de **descrição geral** separador.
2. Clique em **contentor**.
3. Para **nome**, tipo **$root**.
4. Definir **aceder tipo** para **Blob**.
5. Clique em **$root** para abrir o contentor de novo.
6. Clique em **Carregar**.
7. Clique no ícone de pasta junto a **selecione um ficheiro**.
8. Aceda a **ui.html personalizar**, que criou anteriormente no [personalização de página IU](#the-page-ui-customization-feature) secção.
9. Clique em **Carregar**.
10. Selecione o blob de personalizar ui.html que carregou.
11. Junto a **URL**, clique em **cópia**.
12. Num browser, cole o URL copiado e, avance para o site. Se o site está inacessível, certifique-se o tipo de acesso do contentor é definido como **blob**.

## <a name="configure-cors"></a>Configurar a CORS

Configure o armazenamento de BLOBs para a partilha de recursos de várias origens efetuando o seguinte procedimento:

>[!NOTE]
>Pretende experimentar a funcionalidade da IU a personalização utilizando o nosso exemplo HTML e conteúdo do CSS? Fornecemos [uma ferramenta de programa auxiliar simples](active-directory-b2c-reference-ui-customization-helper-tool.md) que carrega e configura o nosso conteúdo de exemplo na sua conta de armazenamento de Blobs. Se utilizar a ferramenta, avançar diretamente para [modificar a política de inscrição ou início de sessão personalizada](#modify-your-sign-up-or-sign-in-custom-policy).

1. No **armazenamento** painel, em **definições**, abra **CORS**.
2. Clique em **Adicionar**.
3. Para **permitido origens**, escreva um asterisco (\*).
4. No **verbos permitidos** na lista pendente, selecione **obter** e **opções**.
5. Para **permitido cabeçalhos**, escreva um asterisco (\*).
6. Para **expostos cabeçalhos**, escreva um asterisco (\*).
7. Para **idade máxima (segundos)**, tipo **200**.
8. Clique em **Adicionar**.

## <a name="test-cors"></a>Teste CORS

Confirme que está pronto efetuando o seguinte procedimento:

1. Aceda ao [teste cors.org](http://test-cors.org/) Web site e, em seguida, cole o URL no **URL remoto** caixa.
2. Clique em **enviar pedido**.  
    Se receber um erro, certifique-se de que o [as definições CORS](#configure-cors) estão corretos. Poderá também terá de limpar a cache do browser ou abra uma sessão de navegação privada ao premir Ctrl + Shift + P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modificar a política personalizada de se inscrever ou iniciar sessão

O nível superior em  *\<TrustFrameworkPolicy\>*  etiqueta, deve encontrar  *\<BuildingBlocks\>*  tag. Dentro do  *\<BuildingBlocks\>*  etiquetas, adicione um  *\<ContentDefinitions\>*  tag copiando o exemplo seguinte. Substitua *your_storage_account* com o nome da conta de armazenamento.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Carregar a sua política personalizada atualizada

1. No [portal do Azure](https://portal.azure.com), [mudar para o contexto do seu inquilino do Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)e, em seguida, abra o **do Azure AD B2C** painel.
2. Clique em **todas as políticas**.
3. Clique em **carregar política**.
4. Carregar `SignUpOrSignin.xml` com o  *\<ContentDefinitions\>*  etiquetas que adicionou anteriormente.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testar a política personalizada utilizando **executar agora**

1. No **do Azure AD B2C** painel, aceda a **todas as políticas**.
2. Selecione a política personalizada que é carregado e clique em de **executar agora** botão.
3. Poderá inscrever-se utilizando um endereço de e-mail.

## <a name="reference"></a>Referência

Pode encontrar modelos de exemplo de personalização de IU aqui:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

A pasta de sample_templates/wingtip contém os seguintes ficheiros HTML:

| Modelo de HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.HTML* | Utilize este ficheiro como um modelo para uma página de autenticação multifator. |
| *ResetPassword.HTML* | Utilizar este ficheiro como um modelo para um esqueceu a página de palavra-passe. |
| *selfasserted.HTML* | Utilize este ficheiro como um modelo para uma página de inscrição de conta de redes sociais, uma página de inscrição de conta local ou uma página de início de sessão da conta local. |
| *Unified.HTML* | Utilize este ficheiro como um modelo para uma página de inscrição ou início de sessão unificada. |
| *updateprofile.HTML* | Utilize este ficheiro como um modelo para uma página de atualização de perfil. |

No [modificar a secção de política personalizada de se inscrever ou iniciar sessão](#modify-your-sign-up-or-sign-in-custom-policy), configurada a definição de conteúdo para `api.idpselections`. O conjunto completo de conteúdo são os IDs de definição que são reconhecidos pelo framework de experiência de identidade do Azure AD B2C e as respetivas descrições na seguinte tabela:

| ID de definição de conteúdo | Descrição | 
|-----------------------|-------------|
| *API.Error* | **Página de erro**. Esta página é apresentada quando é encontrado uma excepção ou um erro. |
| *API.idpselections* | **Página de seleção de fornecedor de identidade**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante o início de sessão. Estas opções são o fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais como o Facebook e Google + ou contas locais. |
| *API.idpselections.Signup* | **Seleção de fornecedor de identidade para inscrição**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante a inscrição. Estas opções são o fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais como o Facebook e Google + ou contas locais. |
| *API.localaccountpasswordreset* | **Se esqueceu a página de palavra-passe**. Esta página contém um formulário que o utilizador deve realizar para iniciar uma reposição de palavra-passe.  |
| *API.localaccountsignin* | **Página de início de sessão da conta local**. Esta página contém um formulário de início de sessão para iniciar sessão com uma conta local, com base no endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *API.localaccountsignup* | **Página de inscrição de conta local**. Esta página contém um formulário de inscrição para inscrever-se de uma conta local é baseada num endereço de e-mail ou um nome de utilizador. O formulário pode conter vários controlos de entrada, tais como uma caixa de entrada de texto, uma caixa de entrada de palavra-passe, um botão de opção, as caixas de lista pendente de selecção única e selecionar vários caixas de verificação. |
| *API.phonefactor* | **Página de autenticação multifator**. Nesta página, os utilizadores podem verificar os respetivos números de telefone (utilizando o texto ou de voz) durante a inscrição ou início de sessão. |
| *API.selfasserted* | **Página de inscrição de redes sociais conta**. Esta página contém um formulário de inscrição que os utilizadores terão de concluir quando inscrever utilizando uma conta existente de um fornecedor de identidade de redes sociais, como o Facebook ou Google +. Esta página é semelhante à anterior conta sociais página de inscrição, exceto para os campos de entrada de palavra-passe. |
| *API.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que os utilizadores podem utilizar para atualizar o respetivo perfil. Esta página é semelhante para a página de inscrição de conta de redes sociais, exceto para os campos de entrada de palavra-passe. |
| *API.signuporsignin* | **Página de inscrição ou início de sessão unificada**. Esta página processa tanto a inscrição e o início de sessão de utilizadores, que podem utilizar fornecedores de identidade empresarial, fornecedores de identidade de redes sociais, como o Facebook Google + ou para contas locais.  |

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais sobre os elementos de IU que podem ser personalizadas, consulte [guia de referência para personalização de IU para políticas incorporadas](active-directory-b2c-reference-ui-customization.md).
