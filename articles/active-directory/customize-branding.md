---
title: "Personalizar a página de início de sessão no Azure Active Directory | Microsoft Docs"
description: "Saiba como adicionar uma imagem corporativa para a página de início de sessão do Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 41f4ceea4842303760c5b156e90bd9e0746a7825
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Guia de introdução: Adicionar imagem corporativa à sua página de início de sessão no Azure AD
Para evitar confusões, muitas empresas pretendem aplicar um aspeto e funcionalidade consistentes em todos os Web sites e serviços que gerem. Azure Active Directory (Azure AD) fornece esta capacidade ao permitir-lhe personalizar o aspeto da página de início de sessão com o logótipo da empresa e esquemas de cores personalizados. A página de início de sessão é a página que aparece quando inicia sessão no Office 365 ou outras aplicações baseadas na web que estão a utilizar o Azure AD como o fornecedor de identidade. Interagir com esta página para introduzir as suas credenciais.

> [!NOTE]
> * Imagem corporativa da empresa está disponível apenas se tiver efetuado a atualização para a edição Premium ou Basic do Azure AD ou tem uma licença do Office 365. Para saber se uma funcionalidade é suportada pelo tipo de licença, verifique o [página de informações de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * As edições Premium e Basic do Azure Active Directory estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições Basic e Premium do Azure Active Directory não são atualmente suportadas pelo serviço Microsoft Azure operado pela 21Vianet na China. Para mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Personalizar a página de início de sessão

<!--You can customize the following elements on the sign-in page: <attach image>-->

Empresa personalizações de imagem corporativa apresentado na página de início de sessão do Azure AD quando os utilizadores acedem um URL de inquilino específico como [ *https://outlook.com/contoso.com*](https://outlook.com/contoso.com).

Quando os utilizadores visitarem um URL genérico como www.office.com, a página de início de sessão não contém corporativa personalizações porque o sistema não souber quem é o utilizador. Imagem corporativa da empresa irá mostrar depois dos utilizadores, introduza o seu ID de utilizador ou selecione um mosaico de utilizador.

> [!NOTE]
> * O nome de domínio tem de aparecer como "Ativo" no **domínios** parte do portal do Azure em que tiver configurado uma imagem corporativa. Para obter mais informações, consulte [adicionar um nome de domínio personalizado](add-custom-domain.md).
> * A imagem corporativa na página de início de sessão não passa para a página de início de sessão de contas Microsoft. Se os seus funcionários e convidados de negócio inicia sessão com uma conta Microsoft pessoal, a respetiva página de início de sessão não reflete a imagem corporativa da sua organização.


### <a name="banner-logo"></a>Logótipo de faixa 

Descrição | Restrições | Recomendações
------- | ------- | ----------
O logótipo de faixa é apresentado o início de sessão e as páginas do painel de acesso.<br>Na página de início de sessão, mostra assim que a organização do utilizador é determinada, normalmente, após o nome de utilizador é introduzido.  | Transparente JPG ou PNG<br>A altura máxima: 36 px<br>A largura máxima: 245 px | Utilize aqui o logótipo da sua organização.<br>Utilize uma imagem transparente. Não parta do princípio que a imagem de fundo será branca.<br>Não adicione preenchimento à volta o logótipo na imagem ou o logótipo procurará disproportionately pequeno.

### <a name="username-hint"></a>Sugestão de nome de utilizador   
Descrição | Restrições | Recomendações
------- | ------- | ----------
Isto customizes o texto de sugestão no campo do nome de utilizador. | Texto Unicode até 64 carateres<br>Apenas texto simples | Recomendamos que não definir esta se espera que os utilizadores convidados fora da sua organização para iniciar sessão na sua aplicação.
            
### <a name="sign-in-page-text"></a>Texto da página de início de sessão   
Descrição | Restrições | Recomendações
------- | ------- | ----------
Esta é apresentada na parte inferior do formulário de início de sessão e pode ser utilizada para comunicar informações adicionais, tais como o número de telefone para o suporte técnico ou uma instrução legal. | Texto Unicode até 256 carateres<br>Apenas texto simples (sem ligações ou tags de HTML)   

### <a name="sign-in-page-image"></a>Imagem da página de início de sessão  
Descrição | Restrições | Recomendações
------- | ------- | ----------
Isto é apresentado em segundo plano da página de início de sessão, está ancorado o centro do espaço de podem ser visualizado e será dimensionar e recortar para preencher a janela do browser.    <br>Em ecrãs estreitos, como telemóveis, esta imagem não é apresentada.<br>Uma máscara preta com opacidade 0.55 será aplicada ao longo desta imagem pelo nosso código quando a página é carregada. | JPG ou PNG<br>As dimensões de imagem: px de 1920 x 1080<br>Tamanho de ficheiro: &lt; 300 KB | <br>Utilizar imagens onde não é um foco requerente forte. O formulário de início de sessão opaco é apresentado ao longo do centro desta imagem e pode abranger qualquer parte da imagem, consoante o tamanho da janela do browser.<br>Mantenha o tamanho do ficheiro tão reduzida quanto possível garantir que os tempos de carregamento rápida. 

### <a name="background-color"></a>Cor de fundo
Descrição | Restrições | Recomendações
------- | ------- | ----------
Esta cor é utilizado em vez da imagem de fundo em ligações de largura de banda baixa. | Cor RGB como hexadecimal (exemplo: #FFFFFF | Sugerimos que utilizar a cor primária do logótipo de faixa ou a cor da organização.

### <a name="show-option-to-remain-signed-in"></a>Mostrar opção permanecer com sessão iniciada
Descrição | Restrições | Recomendações
------- | ------- | ----------
Início de sessão de AD do Azure dá-o utilizador a opção de permanecer com sessão iniciada quando fechar e reabrir o seu browser. Utilize esta opção para ocultar essa opção.<br>Defina esta opção para "Não" para ocultar esta opção dos seus utilizadores. | &nbsp; | Isto não afeta a duração de sessão.<br>Algumas funcionalidades do SharePoint Online e Office 2010 dependem dos utilizadores a capacidade de escolher permanecer com sessão iniciada. Se definir esta opção para ocultas, os utilizadores poderão ver avisos adicionais e inesperados para o início de sessão.

> [!NOTE]
> Todos os elementos são opcionais. Por exemplo, se especificar um logótipo de faixa com nenhuma imagem de fundo, a página de início de sessão irá apresentar o logótipo e a imagem de fundo para o site de destino (por exemplo, o Office 365).

## <a name="add-company-branding-to-your-directory"></a>Adicionar a imagem corporativa para o seu diretório

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta que seja um administrador global do diretório.
2. Selecione **mais serviços**, introduza **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

   ![Gestão de utilizadores de abertura](./media/customize-branding/user-management.png)
3. No **utilizadores e grupos** painel, selecione **da empresa de imagem corporativa**.
4. No **utilizadores e grupos - empresa imagem corporativa** painel, selecione o **editar** comando.

    ![Editar a imagem corporativa personalizado](./media/customize-branding/edit-branding.png)
5. Modifique os elementos que pretende personalizar. Todos os elementos são opcionais.
6. Clique em **Guardar**.

Pode demorar até uma hora para todas as alterações efetuadas para a página de início de sessão de imagem corporativa sejam apresentados.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Adicionar a imagem corporativa específicas do idioma ao seu diretório

1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
2. Selecione **utilizadores e grupos** na caixa de texto e, em seguida, selecione **Enter**.

   ![Gestão de utilizadores de abertura](./media/customize-branding/user-management.png)
3. No **utilizadores e grupos** painel, selecione **da empresa de imagem corporativa**.
4. No **utilizadores e grupos - empresa imagem corporativa** painel, selecione o **Adicionar idioma** comando.

    ![Adicionar elementos de imagem corporativa específicas do idioma](./media/customize-branding/add-language.png)
5. Modifique os elementos que pretende personalizar. Todos os elementos são opcionais.
6. Clique em **Guardar**.

Pode demorar até uma hora para todas as alterações efetuadas para a página de início de sessão de imagem corporativa sejam apresentados.

## <a name="next-steps"></a>Passos seguintes
Este guia de introdução, aprendeu como adicionar imagem corporativa para o diretório do Azure AD. 

Pode utilizar a hiperligação seguinte para configurar a sua imagem corporativa no Azure AD a partir do portal do Azure.

> [!div class="nextstepaction"]
> [Configurar o branding da empresa](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 