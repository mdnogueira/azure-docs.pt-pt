---
title: "Traduzir ligações e URLs do Proxy de aplicação do Azure AD | Microsoft Docs"
description: "Abrange as noções básicas sobre conectores de Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 57218346d236b376d2227e0ffaea6c6dd5ebe855
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecionar codificado ligações para aplicações publicadas com o Proxy de aplicações do Azure AD

Proxy de aplicações do Azure AD disponibiliza as suas aplicações no local para os utilizadores que são remotos, ou os seus próprios dispositivos. No entanto, algumas aplicações foram desenvolvidas com ligações locais incorporadas no HTML. Estas ligações não funcionam corretamente quando a aplicação é utilizada remotamente. Quando tiver várias aplicações no local a apontar para si, os utilizadores esperam que as ligações para manter a funcionar quando não estiverem no escritório. 

É a melhor forma para se certificar de que as ligações funcionam da mesma dentro e fora da rede empresarial para configurar os URLs externos das suas aplicações para ser o mesmo que os URLs internos. Utilize [domínios personalizados](active-directory-application-proxy-custom-domains.md) para configurar os URLs externos para ter o seu nome de domínio empresarial em vez do domínio de proxy de aplicações predefinido.

Se não conseguir utilizar domínios personalizados no seu inquilino, a funcionalidade de tradução de ligação de Proxy de aplicações mantém as ligações, independentemente de onde os utilizadores estão a funcionar. Quando tiver aplicações que vá diretamente a pontos finais internos ou portas, pode mapear estes URLs internos para os URLs de Proxy de aplicações externas publicada. Quando a tradução de ligação está ativada e pesquisas de Proxy de aplicações através de HTML, CSS e selecionadas etiquetas de JavaScript para ligações internas publicadas. Em seguida, o serviço de Proxy de aplicações converte-los para que os utilizadores obtêm uma experiência ininterrupta.

>[!NOTE]
>A funcionalidade de tradução de ligação é para os inquilinos que, para que pelo motivo, não é possível utilizar domínios personalizados com os mesmos URLs internos e externos para as suas aplicações. Antes de ativar esta funcionalidade, consulte o artigo se [domínios personalizados no Proxy de aplicações do Azure AD](active-directory-application-proxy-custom-domains.md) pode resolver o problema.
>
>Ou, se a aplicação tem de configurar com ligação tradução é SharePoint, consulte [configurar mapeamentos de acesso alternativo para SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) para outra abordagem para ligações de mapeamento.

## <a name="how-link-translation-works"></a>Como funciona a tradução de ligação

Após a autenticação, quando o servidor proxy transmite os dados da aplicação para o utilizador, o Proxy de aplicações analisa a aplicação para ligações de codificado e substitui-los com os seus respetivos publicados URLs externos.

Proxy de aplicações parte do princípio de que as aplicações são com codificação UTF-8. Se não for esse o caso, especifique o tipo de codificação no cabeçalho de resposta de http, tal como `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>As ligações são afetadas?

A funcionalidade de tradução de ligação procura apenas as ligações que estão em tags de código no corpo de uma aplicação. Proxy de aplicações tem uma funcionalidade à parte para traduzir cookies ou URLs nos cabeçalhos. 

Existem dois tipos comuns de ligações internos em aplicações no local:

- **Ligações internas relativas** que ponto para um recurso partilhado numa estrutura de ficheiros local como `/claims/claims.html`. Estas ligações funcionam automaticamente nas aplicações que são publicadas através do Proxy de aplicações e continuam a trabalhar com ou sem tradução de ligação. 
- **Ligações internas codificado** para outras aplicações no local como `http://expenses` ou publicado ficheiros como `http://expenses/logo.jpg`. A funcionalidade de tradução de ligação funciona em ligações interno codificado e altera-los para que apontem para os URLs externos que os utilizadores remotos têm de passar por.

### <a name="how-do-apps-link-to-each-other"></a>Como ligação a aplicações entre si?

Tradução de ligação está ativada para cada aplicação, para que tenham o controlo sobre a experiência de utilizador ao nível por aplicação. Ativar a tradução de ligação para uma aplicação quando pretender que as ligações *de* essa aplicação para ser convertido, não ligar *para* essa aplicação. 

Por exemplo, suponha que tem três aplicações publicadas através do Proxy de aplicações que todos os ligação entre si: benefícios, as despesas e levar. Há uma quarta aplicação, comentários, que não estão publicado através do Proxy de aplicações.

Quando ativar a tradução de ligação para a aplicação de benefícios, as ligações para as despesas e levar são redirecionadas para os URL externos para essas aplicações, mas a ligação para comentários não é redirecionada porque não há nenhum URL externo. As ligações de despesas e levar para vantagens não funcionam porque a tradução de ligação não foi ativada para as duas aplicações.

![Ligações de benefícios para outras aplicações quando a tradução de ligação está ativada](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>As ligações não são convertidas?

Para melhorar o desempenho e segurança, não são convertidas algumas hiperligações:

- Ligações não dentro de etiquetas de código. 
- Ligações não no HTML, CSS ou JavaScript. 
- Ligações internas abertas a partir de outros programas. Não possível converter ligações enviados através de e-mail ou mensagem instantânea, ou parte de outros documentos. Os utilizadores precisam de saber para ir para o URL externo.

Se precisar de suportar um dos dois destes cenários, utilize os mesmos URLs internos e externos em vez de tradução de ligação.  

## <a name="enable-link-translation"></a>Ativar a tradução de ligação

Introdução à tradução de ligação é tão fácil como clicar num botão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Aceda a **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** > selecione a aplicação que pretende gerir > **proxy de aplicações**.
3. Ativar **traduzir URLs no corpo de aplicação** para **Sim**.

   ![Selecione Sim para tradução de URLs no corpo de aplicação](./media/application-proxy-link-translation/select_yes.png).
4. Selecione **guardar** para aplicar as suas alterações.

Agora, quando os utilizadores acederem a esta aplicação, o proxy irá analisar automaticamente para URL interno que tiverem sido publicada através do Proxy de aplicações no seu inquilino.

## <a name="send-feedback"></a>Enviar comentários

Queremos a sua ajudá-lo para tornar esta funcionalidade funciona para todas as suas aplicações. Estamos a pesquisar mais de 30 etiquetas no HTML e CSS e estiver a considerar que o JavaScript casos para suportar. Se tiver um exemplo de ligações geradas que não estão a ser traduzidos, envie um fragmento de código para [comentários de Proxy de aplicação](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passos seguintes
[Utilizar domínios personalizados com o Proxy de aplicações do Azure AD](active-directory-application-proxy-custom-domains.md) para ter o mesmo URL interno e externo

[Configurar os mapeamentos de acesso alternativo para SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
