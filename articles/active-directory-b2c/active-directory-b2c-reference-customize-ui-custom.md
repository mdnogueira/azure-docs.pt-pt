---
title: "Do Azure Active Directory B2C: Referência: personalizar a IU da journey utilizador com as políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 68f40aa638a687398512278a0b77d1ba392859cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>Personalize a IU da journey utilizador com as políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Este artigo é uma descrição de como ativar o com as políticas de B2C personalizada, utilizando a estrutura de experiência de identidade e de como funciona a personalização de IU Avançada


Uma experiência de utilizador totalmente integrada é a chave para qualquer solução de empresa-consumidor. Através da experiência de utilizador totalmente integrada, estamos significa uma experiência, no dispositivo ou browser, onde journey de um utilizador através do nosso serviço não pode ser distinguida do que o serviço de cliente que estão a utilizar.

## <a name="understand-the-cors-way-for-ui-customization"></a>Compreender a forma CORS de personalização de IU

O Azure AD B2C permite-lhe personalizar o aspeto e funcionalidade de experiência de utilizador (UX) nas várias páginas que podem ser servidos e apresentados pelo Azure AD B2C através de políticas personalizadas do seu potencialmente.

Para essa finalidade, o Azure AD B2C executa o código no browser do cliente e utiliza a abordagem moderna e standard [de partilha de recursos de várias origens (CORS)](http://www.w3.org/TR/cors/) carregar conteúdo personalizado a partir de um URL específico que especificar numa política personalizada para que apontem para os modelos de HTML5/CSS. CORS é um mecanismo que permite recursos restritos, como tipos de letra, numa página web para pedidos a partir de outro domínio fora do domínio a partir do qual o recurso foi originado.

Em comparação com a forma tradicional antiga, onde as páginas de modelo forem propriedade pela solução de onde fornecido limitado de texto e imagens, em que foi oferecido controlo limitado de esquema e o funcionamento em mais do que dificuldades para alcançar um totalmente integrada experiência, a forma CORS suporta HTML5 e CSS e à esquerda permitem-lhe:

- O conteúdo de anfitriões e a solução injects os controlos com o script do lado do cliente.
- Ter controlo total sobre cada pixel de esquema e a funcionalidade.

Pode fornecer tantos páginas conteúdas como pretender através de composição ficheiros HTML5/CSS, conforme apropriado.

> [!NOTE]
> Por motivos de segurança, a utilização de JavaScript está atualmente bloqueada para personalização. Para desbloquear o JavaScript, é necessária a utilização de um nome de domínio personalizado para o seu inquilino do Azure AD B2C.

Cada um dos seus modelos HTML5/CSS, deve fornecer um *âncora* elemento, que corresponde à necessários `<div id=”api”>` elemento no HTML ou a página conteúda como ilustrar hereafter. O Azure AD B2C requer que todas as páginas de conteúdo têm este div. específico

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Conteúdo relacionado com AD B2C do Azure para a página irá ser injetado neste div, enquanto o resto da página é mesmo seu controlo. No código JavaScript do Azure AD B2C solicita o conteúdo e injects nosso HTML para este elemento div específico. O Azure AD B2C injects os seguintes controlos conforme adequado: atributo coleção controlos, controlos (atualmente baseado em telefone) do multi-factor, controlos de início de sessão e controlo de selecionador de conta. O Azure AD B2C garante que todos os controlos são HTML5 acessível e não compatíveis, os controlos podem ser totalmente escovados e de que uma versão de controlo não será regress.

O conteúdo intercalado, eventualmente, é apresentado como o documento dinâmico para o consumidor.

Para garantir do acima funciona conforme esperado, tem de:

- Certifique-se de que o conteúdo é HTML5 acessível e não compatíveis
- Certifique-se de que o servidor de conteúdo está ativado para CORS.
- Servir conteúdo através de HTTPS.
- Utilize URLS absolutos como https://yourdomain/content para todas as ligações e conteúdo do CSS.

> [!TIP]
> Para verificar se o site que está a alojar o conteúdo tem CORS ativada e testar pedidos CORS, pode utilizar o http://test-cors.org/ site. Graças deste site, pode simplesmente enviar o pedido CORS para um servidor remoto (para testar se for suportada CORS), ou enviar o pedido CORS para um servidor de teste (para explorar determinadas funcionalidades de CORS).

> [!TIP]
> O site http://enable-cors.org/ também constitui um mais do que recursos úteis no CORS.

Graças a esta abordagem baseada em CORS, os utilizadores finais terá experiências consistentes entre a sua aplicação e as páginas servidas pelo Azure AD B2C.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Como pré-requisito, tem de criar uma conta de armazenamento. Precisa de uma subscrição do Azure para criar uma conta do Blob Storage do Azure. Pode inscrever numa avaliação gratuita no [Web site Azure](https://azure.microsoft.com/en-us/pricing/free-trial/).

1. Abra uma sessão de navegação e navegue para o [portal do Azure](https://portal.azure.com).
2. Inicie sessão com as suas credenciais administrativas.
3. Clique em **novo** > **dados + armazenamento** > **conta de armazenamento**.  A **criar conta de armazenamento** painel abre-se.
4. No **nome**, forneça um nome para a conta de armazenamento, por exemplo, *contoso369b2c*. Este valor será mais tarde referido como *storageAccountName*.
5. Escolha as seleções adequadas para o escalão de preço, o grupo de recursos e a subscrição. Certifique-se de que tem o **afixar ao Startboard** opção selecionada. Clique em **Criar**.
6. Volte ao Startboard e clique na conta de armazenamento que acabou de criar.
7. No **serviços** secção, clique em **Blobs**. A **painel de serviço Blob** abre-se.
8. Clique em **+ contentor**.
9. No **nome**, forneça um nome para o contentor, por exemplo, *b2c*. Este valor será mais tarde referido como *containerName*.
9. Selecione **Blob** como o **aceder tipo**. Clique em **Criar**.
10. O contentor que criou serão apresentados na lista no **painel de serviço Blob**.
11. Fechar o **Blobs** painel.
12. No **painel de conta de armazenamento**, clique em de **chave** ícone. Um **painel chaves de acesso** abre-se.  
13. Anote o valor de **chave1**. Este valor será mais tarde referido como *chave1*.

## <a name="downloading-the-helper-tool"></a>Transferir a ferramenta de programa auxiliar

1.  Transferir a ferramenta de programa auxiliar de [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Guardar o *B2C AzureBlobStorage-cliente master.zip* ficheiro no seu computador local.
3.  Extrair os conteúdos do ficheiro B2C AzureBlobStorage-cliente master.zip no disco local, por exemplo sob o **pacote de personalização de IU** pasta. Esta ação irá criar um *B2C-AzureBlobStorage-cliente-master* pasta por baixo.
4.  Abra a pasta e extrair os conteúdos do ficheiro de arquivo *B2CAzureStorageClient.zip* dentro da mesma.

## <a name="upload-the-ui-customization-pack-sample-files"></a>Carregar os ficheiros de exemplo de pacote de personalização de IU

1.  Utilizando o Explorador do Windows, navegue para a pasta *B2C-AzureBlobStorage-cliente-master* localizada sob o *pacote de personalização de IU* pasta que criou na secção anterior.
2.  Execute o *B2CAzureStorageClient.exe* ficheiro. Este programa simplesmente carregar todos os ficheiros no diretório que especificar para a sua conta do storage e ativar o acesso CORS para esses ficheiros.
3.  Quando lhe for pedido, especifique: um.  O nome da sua conta de armazenamento, *storageAccountName*, por exemplo *contoso369b2c*.
    b.  A chave de acesso primária do seu armazenamento de Blobs do azure, *chave1*, por exemplo *contoso369b2c*.
    c.  O nome do contentor de armazenamento de BLOBs de armazenamento, *containerName*, por exemplo *b2c*.
    d.  O caminho do *Starter pacote* exemplo ficheiros, por exemplo *... \B2CTemplates\wingtiptoys*.

Se seguiu os passos acima, os ficheiros de HTML5 e CSS do *pacote de personalização de IU* para a empresa fictícia com **wingtiptoys** agora irá apontar para a sua conta de armazenamento.  Pode verificar que o conteúdo foi carregado corretamente ao abrir o painel de contentor relacionados no portal do Azure. Em alternativa, pode verificar que o conteúdo foi carregado corretamente acedendo à página num browser. Para obter mais informações, consulte [Azure Active Directory B2C: uma ferramenta de programa auxiliar utilizada para demonstrar a funcionalidade de personalização de interface (IU) de utilizador de página](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Certifique-se de que a conta de armazenamento tem CORS ativada

CORS (transversal à partilha de recursos) tem de estar ativado no seu ponto final do B2C do Azure AD Premium ao carregar o conteúdo. Isto acontece porque o conteúdo estiver alojado num domínio diferente do que o domínio do Azure AD B2C Premium será que serve a página do.

Para verificar que o armazenamento que está a alojar o conteúdo tem CORS ativada, pode continue com os seguintes passos:

1. Abra uma sessão de navegação e navegue para a página *unified.html* utilizando o URL completo da respetiva localização na sua conta de armazenamento, `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Por exemplo, https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navegue para http://test-cors.org. Este site permite-lhe verificar que a página que está a utilizar tem CORS ativada.  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. No **URL remoto**, introduza o URL completo para o conteúdo do unified.html e, em **enviar pedido**.
4. Certifique-se de que o resultado no **resultados** secção contém *Estado XHR: 200*. Isto indica que a CORS está ativada.
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
A conta de armazenamento agora deve conter um contentor do blob denominado *b2c* na nossa ilustração que contém os seguintes modelos de wingtiptoys do *Starter pacote*.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

A tabela seguinte descreve a finalidade das páginas HTML5 acima.

| Modelo de HTML5 | Descrição |
|----------------|-------------|
| *phonefactor.HTML* | Nesta página pode ser utilizada como um modelo para uma página de autenticação multifator. |
| *ResetPassword.HTML* | Nesta página pode ser utilizada como um modelo para um esqueceu a página de palavra-passe. |
| *selfasserted.HTML* | Nesta página pode ser utilizada como um modelo para uma página de inscrição de conta de redes sociais, uma página de inscrição de conta local ou uma página de início de sessão da conta local. |
| *Unified.HTML* | Nesta página pode ser utilizada como um modelo para uma página de inscrição ou início de sessão unificada. |
| *updateprofile.HTML* | Nesta página pode ser utilizada como um modelo para uma página de atualização de perfil. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Adicionar uma ligação para os modelos de HTML5/CSS da sua viagem de utilizador

Pode adicionar uma ligação para os modelos de HTML5/CSS da sua viagem de utilizador ao editar diretamente uma política personalizada.

Os modelos de HTML5/CSS personalizados para utilizar da sua viagem de utilizador tem de ser especificado numa lista de definições de conteúdo que podem ser utilizados nos percursos de utilizador. Para essa finalidade, opcional  *<ContentDefinitions>*  elemento XML tem de ser declarado sob o  *<BuildingBlocks>*  secção Compilation do ficheiro XML de política personalizada.

A tabela seguinte descreve o conjunto de ids de definição de conteúdo reconhecido pelo motor de experiência de identidade do Azure AD B2C e o tipo de páginas que está relacionada com aos mesmos.

| Id de definição de conteúdo | Descrição |
|-----------------------|-------------|
| *API.Error* | **Página de erro**. Esta página é apresentada quando é encontrado uma excepção ou um erro. |
| *API.idpselections* | **Página de seleção de fornecedor de identidade**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante o início de sessão. Estes são o fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais como o Facebook e Google + ou contas locais (com base no nome de utilizador ou endereço de e-mail). |
| *API.idpselections.Signup* | **Seleção de fornecedor de identidade para inscrição**. Esta página contém uma lista de fornecedores de identidade que o utilizador pode escolher durante a inscrição. Estes são o fornecedores de identidade empresarial, os fornecedores de identidade de redes sociais como o Facebook e Google + ou contas locais (com base no nome de utilizador ou endereço de e-mail). |
| *API.localaccountpasswordreset* | **Se esqueceu a página de palavra-passe**. Esta página contém um formulário que o utilizador tem de preencher para iniciar a respetiva reposição de palavra-passe.  |
| *API.localaccountsignin* | **Página de início de sessão da conta local**. Esta página contém um formulário de início de sessão que o utilizador tem de preencher quando iniciar sessão com uma conta local, com base no endereço de e-mail ou um nome de utilizador. O formulário pode conter uma caixa de entrada de texto e a caixa de entrada de palavra-passe. |
| *API.localaccountsignup* | **Página de inscrição de conta local**. Esta página contém um formulário de inscrição que o utilizador tem de preencher quando inscrever-se de uma conta local é baseada num endereço de e-mail ou um nome de utilizador. O formulário pode conter controlos de entrada diferentes, tais como a caixa de entrada de texto, caixa de entrada de palavra-passe, botão de opção, as caixas de lista pendente de selecção única e selecionar vários caixas de verificação. |
| *API.phonefactor* | **Página de autenticação multifator**. Nesta página, os utilizadores podem verificar os respetivos números de telefone (utilizando o Editor de texto ou de voz) durante a inscrição ou início de sessão. |
| *API.selfasserted* | **Página de inscrição de redes sociais conta**. Esta página contém um formulário de inscrição que o utilizador tem de preencher quando inscrever-se com uma conta existente de um fornecedor de identidade de redes sociais, como o Facebook ou Google +. Esta página é semelhante para o social acima campos de entrada da página de inscrição com a exceção a palavra-passe de conta. |
| *API.selfasserted.profileupdate* | **Página de atualização de perfil**. Esta página contém um formulário que o utilizador pode utilizar para atualizar o respetivo perfil. Esta página é semelhante para o social acima campos de entrada da página de inscrição com a exceção a palavra-passe de conta. |
| *API.signuporsignin* | **Página de inscrição ou início de sessão unificada**.  Esta página processa tanto inscrição & início de sessão de utilizadores, que podem utilizar fornecedores de identidade empresarial, fornecedores de identidade de redes sociais, como o Facebook Google + ou para contas locais.

## <a name="next-steps"></a>Passos seguintes
[Referência: Compreender as políticas personalizadas como trabalhar com a arquitetura de experiência de identidade no B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)
