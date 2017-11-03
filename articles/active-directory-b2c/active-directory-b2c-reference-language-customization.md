---
title: "O Azure Active Directory B2C: Personalização de idioma a utilizar | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>O Azure Active Directory B2C: Personalização de idioma a utilizar

>[!NOTE] 
>Esta funcionalidade está em pré-visualização pública.  É recomendado que utilize um inquilino de teste ao utilizar esta funcionalidade.  Não planeamos em quaisquer alterações de quebra a partir da pré-visualização para a versão de disponibilidade geral, mas iremos reservar o direito de efetuar essas alterações para melhorar a funcionalidade.  Depois de ter tido oportunidade de experimentar a funcionalidade, forneça comentários sobre as suas experiências e como podemos pode torná-lo melhor.  Pode fornecer comentários através do portal do Azure com a ferramenta de rostos em smiley no canto superior direito.   Se não houver um requisito comercial para ir em direto de utilizar esta funcionalidade durante a fase de pré-visualização, informe-nos cenários e podem fornecer as orientações adequada e obter assistência.  Pode contactar-nos [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).

Personalização de idioma permite-lhe alterar da sua viagem de utilizador para um idioma diferente de acordo com as suas necessidades de cliente.  Fornecemos traduções para 36 idiomas (consulte [informações adicionais](#additional-information)).  Mesmo se a sua experiência só é fornecida para um único idioma, pode personalizar qualquer texto nas páginas de acordo com as suas necessidades.  

## <a name="how-does-language-customization-work"></a>Como funciona a personalização de idioma?
Personalização de idioma permite-lhe selecionar os idiomas da sua viagem de utilizador está disponível.  Depois da funcionalidade está ativada, pode fornecer o parâmetro de cadeia de consulta, ui_locales, da sua aplicação.  Quando chamar para o Azure AD B2C, iremos traduzir a página para a região que indicou.  Utilizando o tipo de configuração dá-lhe controlo total sobre os idiomas da sua viagem de utilizador e ignora as definições de idioma do browser do cliente. Em alternativa, pode não necessitar desse nível de controlo sobre os idiomas, consulte do seu cliente.  Se não fornecer um parâmetro de ui_locales, experiência do cliente é ditada pelo definições do seu browser.  Pode continuar a controlar os idiomas da sua viagem de utilizador é convertida em adicionando-o como um idioma suportado.  Se o browser de um cliente está definida para mostrar um idioma que não pretende suportar, o idioma selecionado por predefinição no culturas suportadas é apresentado em vez disso.

1. **IU regiões especificado idioma** -depois de ativar a personalização de idioma, da sua viagem de utilizador é convertida para o idioma especificado aqui
2. **Browser pedida idioma** -não se foi especificado nenhum regiões IU, traduz-se o browser pedida idioma, **se foi incluído nos idiomas suportados**
3. **Linguagem predefinida de política** -se o browser não especifica um idioma ou especifica um que não é suportada, mesmo traduz-se o idioma predefinido de política

>[!NOTE]
>Se estiver a utilizar os atributos de utilizador personalizado, terá de fornecer as suas próprias traduções. Consulte '[personalizar as cadeias](#customize-your-strings)' para obter mais detalhes.
>

## <a name="support-uilocales-requested-languages"></a>Pedido de suporte ui_locales idiomas 
Ao ativar 'Personalização de idioma' numa política, agora pode controlar o idioma de journey o utilizador adicionando o parâmetro ui_locales.
1. [Siga estes passos para navegar para o painel de funcionalidades do B2C no portal do Azure.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navegue para uma política que pretende ativar para traduções.
3. Clique em **personalização de idioma**.
4. Leia o aviso cuidadosamente.  Uma vez ativada, não é possível desativar 'Personalização de idioma'.
5. Ative a funcionalidade e clique em **OK**.
6. Guardar a política no canto superior esquerdo do seu **Editar política** painel.

## <a name="select-which-languages-your-user-journey-supports"></a>Selecione os idiomas da sua viagem de utilizador suporta 
Crie uma lista de idiomas permitidos da sua viagem de utilizador ser convertido no quando o parâmetro ui_locales não foi fornecido.
1. Certifique-se a política de 'Personalização de idioma' ativada a partir de instruções anteriores.
2. Do seu **Editar política** painel, selecione **personalização de idioma**.
3. É direcionado para o **idiomas suportados** painel.  Aqui, pode selecionar **Adicionar idioma**.
4. Selecione os idiomas que pretende que seja suportado.  

>[!NOTE]
>Se não for fornecido um parâmetro de ui_locales, em seguida, a página é convertida em idioma do browser do cliente apenas se estiver nesta lista
>

5. Clique em **Ok** na parte inferior
6. Fechar o **personalização de idioma** painel e **guardar** a política.

## <a name="customize-your-strings"></a>Personalizar a sua cadeias
'Personalização de idioma' permite-lhe personalizar a qualquer cadeia da sua viagem de utilizador.
1. Certifique-se a política de 'Personalização de idioma' ativada a partir das instruções anteriores.
2. Do seu **Editar política** painel, selecione **personalização de idioma**.
3. No menu de navegação esquerdo, selecione **transferir conteúdo**.
4. Selecione a página que pretende editar.
5. Na lista pendente, selecione o idioma que pretende editar para.
6. Clique em **Transferir**. 

Estes passos dão-lhe um ficheiro JSON que pode utilizar para iniciar as cadeias de edição.

### <a name="changing-any-string-on-the-page"></a>Alterar qualquer cadeia na página
1. Abra o ficheiro JSON que transferiu do instruções anteriores num editor de JSON.
2. Encontrar o elemento que pretende alterar.  Pode encontrar o `StringId` da cadeia de procura ou procure o `Value` que pretende alterar.
3. Atualização do `Value` atributo com o que pretende apresentar.
4. Guarde o ficheiro e carregar as suas alterações.

### <a name="changing-extension-attributes"></a>Atributos de extensão de alteração
Se pretender alterar a cadeia para um atributo de utilizador personalizada ou pretender adicionar um para o JSON, é o seguinte formato:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Se precisar de uma cadeia de substituição, certifique-se definir o `Override` valor `true`.  Se o valor não é alterado, a entrada é ignorada. 
>

Substitua `<ExtensionAttribute>` com o nome do seu atributo de utilizador personalizadas.  
Substitua `<ExtensionAttributeValue>` com a nova cadeia a apresentar.

### <a name="using-localizedcollections"></a>Utilizar LocalizedCollections
Se pretender fornecer uma lista de conjunto de valores de respostas, terá de criar um `LocalizedCollections`.  A `LocalizedCollections` é uma matriz de `Name` e `Value` pares.  O `Name` é o que é apresentado e o `Value` é o que é devolvido na afirmação.  Para adicionar um `LocalizedCollections`, tem o seguinte formato:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": false,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```
>[!IMPORTANT]
>Se precisar de uma cadeia de substituição, certifique-se definir o `Override` valor `true`.  Se o valor não é alterado, a entrada é ignorada. 
>

* `ElementId`é o utilizador de atributos que este `LocalizedCollections` for uma resposta a
* `Name`é apresentado o valor para o utilizador
* `Value`é o que é devolvido na afirmação quando esta opção está selecionada

### <a name="upload-your-changes"></a>Carregar as suas alterações
1. Depois de concluir as alterações para o ficheiro JSON, navegue para o inquilino do B2C.
2. Do seu **Editar política** painel, selecione **personalização de idioma**.
3. No menu de navegação esquerdo, selecione **carregar conteúdo**.
4. Selecione a página que pretende carregar as suas alterações para.
5. Se pretender carregar um idioma que forneceu anteriormente um JSON para, terá de eliminar a entrada anterior.  Pode eliminá-lo ao clicar no `...` à direita desse idioma e selecione **eliminar**.
6. Clique em **adicionar** no canto superior esquerdo.
7. Selecione o idioma do ficheiro JSON.
8. Clique no botão de pasta à direita e navegue para o ficheiro JSON.
9. Clique em de **carregar** botão na parte inferior do painel.
10. Volte ao seu **Editar política** painel e clique em **guardar**.

## <a name="additional-information"></a>Informações adicionais
### <a name="recommendations-for-language-customization"></a>Recomendações para personalização do idioma
Recomendamos que apenas colocando no entradas aos seus recursos do idioma cadeias que pretende substituir explicitamente.  Iremos impor um limite de tamanho para o ficheiro que é compilado fora de todos os seus traduções de JSON.  Se os ficheiros de obter demasiado grandes, afeta o desempenho da sua viagem de utilizador.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>As etiquetas de personalização de página IU são removidas quando a personalização do idioma estiver ativada
Quando ativar a personalização do idioma, as edições que efectuou anterior para as etiquetas a utilizar a personalização de página IU são removidas, exceto para os atributos de utilizador personalizadas.  Esta alteração é feita para evitar conflitos na qual pode editar as cadeias.  Pode continuar a alterar as etiquetas e noutras cadeias através do carregamento de recursos do idioma 'Personalização de idioma'.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>A Microsoft está empenhada para fornecer as traduções mais atualizadas à sua para utilização
Iremos continuamente melhorar traduções e mantê-las em conformidade para si.  Iremos identificar erros e de alterações na terminologia global e disponibilizar as atualizações de que irão funcionar de forma totalmente integrada da sua viagem de utilizador.
### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas de direita para a esquerda
Não são suportadas para idiomas de direita para a esquerda, se necessitar desta funcionalidade, votar para esta funcionalidade no [Azure comentários](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Redes sociais traduções de fornecedor de identidade
Podemos fornecer o parâmetro OIDC ui_locales para inícios de sessão de redes sociais, mas não são cumpridas por alguns fornecedores de identidade de redes sociais, incluindo Facebook e Google. 
### <a name="browser-behavior"></a>Comportamento de browser
Chrome Firefox ambos de pedidos de e para o idioma do conjunto e se se tratar de um idioma suportado, é apresentada antes da predefinição.  Atualmente, o limite não pedir um idioma e vai diretamente para o idioma predefinido.
### <a name="known-issues"></a>Problemas conhecidos
* Carregar os recursos de idioma para a página MFA numa política de editar o perfil não está atualmente disponível.
* `LocalizedCollections`não são gerados para os valores quando for necessária pelo tipo de resposta
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>E se quiser um idioma que não é suportado?
Iremos estiver a planear para fornecer uma extensão desta funcionalidade permite-lhe carregar um recurso JSON para 'idiomas personalizados'.  A funcionalidade permite-lhe especificar o código de idioma e o nome para nenhum idioma e fornecer *todos os* traduções para esse idioma.  Se precisar desta funcionalidade, envie-do seu cenário em [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Que idiomas são suportados?

| Idioma              | Código de idioma |
|-----------------------|---------------|
| Bangla                | Bn            |
| Checo                 | cs            |
| Dinamarquês                | da            |
| Alemão                | de            |
| Grego                 | el            |
| Português               | en            |
| Espanhol               | es            |
| Finlandês               | fi            |
| Francês                | fr            |
| Gujarati              | Gu            |
| Hindi                 | Olá            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonês              | ja            |
| Kannada               | kN            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Marathi               | MR            |
| Malaio                 | MS            |
| Norueguês Bokmal      | nb            |
| Neerlandês                 | nl            |
| Punjabi               | Pa            |
| Polaco                | pl            |
| Português - Brasil   | pt-br         |
| Português - Portugal | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Eslovaco                | SK            |
| Sueco               | sv            |
| Tamil                 | dados            |
| Telugu                | te            |
| Tailandês                  | ésimo            |
| Turco               | TR            |
| Chinês - simplificada  | zh-hans       |
| Chinês - tradicional | zh-hant       |
