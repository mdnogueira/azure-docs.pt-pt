---
title: "Adicionar a imagem corporativa às páginas de início de sessão e do Painel de Acesso no Azure Active Directory"
description: "Saiba como adicionar uma imagem corporativa à página de início de sessão do Azure e à página de painel de acesso"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f74621b4-4ef0-4899-8c0e-0c20347a8c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/07/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 6d4fbfe97288fcb76628b45649b8b678152198a9
ms.lasthandoff: 04/07/2017


---
# <a name="add-company-branding-to-sign-in-and-access-panel-pages"></a>Adicionar a imagem corporativa às páginas de início de sessão e do Painel de Acesso
Muitas organizações preferem aplicar um aspeto e funcionalidade consistente em todos os Web sites e serviços que gerem. O Azure Active Directory proporciona esta capacidade ao permitir que os profissionais de TI personalizem o aspeto das seguintes páginas Web com logótipos e imagens das empresas:

* **Página de início de sessão** - esta é a página que aparece quando os seus funcionários e convidados empresariais iniciam sessão no Office 365 ou noutras aplicações que utilizam o Azure AD.
* **Página Painel de Acesso** - o Painel de Acesso é um portal baseado na Web que lhe permite ver e iniciar as aplicações baseadas na cloud a que o seu administrador do Azure AD lhe concedeu acesso. O Painel de Acesso está disponível em [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tópico explica como pode personalizar a página de início de sessão e a página de painel de acesso.

> [!NOTE]
> * A imagem corporativa só estará disponível se tiver atualizado para a edição Premium ou Básica do Azure Active Directory ou se tiver uma licença do Office 365. Para obter mais informações, veja Edições do Azure Active Directory.
> 
> * As edições Premium e Basic do Azure Active Directory estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições Basic e Premium do Azure Active Directory não são atualmente suportadas pelo serviço Microsoft Azure operado pela 21Vianet na China. Para obter mais informações, contacte-nos através do Fórum do Azure Active Directory.


## <a name="customizing-the-sign-in-page"></a>Personalizar a página de início de sessão
Geralmente, os utilizadores interagem com a página de início de sessão do Azure AD quando tentam aceder a aplicações e serviços cloud que a sua organização subscreveu.

Se tiver efetuado alterações à imagem corporativa na sua página de início de sessão, pode demorar até uma hora para que estas apareçam aos utilizadores finais.

Os elementos da imagem corporativa das empresas aparecem na página de início de sessão do Azure AD quando os utilizadores acedem a um URL de inquilino específico, como https://outlook.com/contoso.com.

Quando os utilizadores visitam um serviço num URL genérico, como www.office.com, a página de início de sessão ainda não contém qualquer informação de imagem corporativa, porque o sistema não sabe quem são esses utilizadores. No entanto, a imagem aparece imediatamente depois de introduzirem o ID de utilizador ou de selecionarem um mosaico de utilizador.

> [!NOTE]
> * O nome de domínio tem de aparecer como “Ativo” na secção **Domínios** > **do Diretório** > **do Active Directory** do Portal Clássico do Azure onde tiver configurado uma imagem corporativa.
> * A imagem corporativa na página de início de sessão não passa para a página de início de sessão de contas Microsoft. Se os seus funcionários ou convidados empresariais iniciarem sessão com uma conta Microsoft, a página de início de sessão não refletirá a imagem corporativa da sua organização.
>

As capturas de ecrã seguintes explicam como são personalizadas as páginas de início de sessão.

### <a name="scenario-1-contoso-employee-goes-to-a-generic-app-url-for-example-wwwofficecom"></a>Cenário 1: um funcionário da Contoso vai ao URL genérico de uma aplicação (por exemplo, www.office.com)

Neste exemplo, um utilizador da Contoso utiliza um URL genérico para iniciar sessão numa aplicação móvel ou numa aplicação Web. A ilustração à esquerda representará sempre a aplicação e o painel de interação, à direita, é atualizado para mostrar os elementos da imagem corporativa da Contoso, sempre que for adequado.

![Página de início de sessão do Office 365 antes e depois da personalização][1]

### <a name="scenario-2-contoso-employee-goes-to-contoso-app-thats-restricted-to-internal-users"></a>Cenário 2: o funcionário da Contoso aceda a uma aplicação da Contoso que está limitada a utilizadores internos

Neste exemplo, um utilizador Contoso está a iniciar sessão numa aplicação interna com um URL específico da empresa. A ilustração à esquerda representa a marca da empresa (Contoso). O painel de interação à direita está bloqueado para a Contoso e ajuda os funcionários ao longo do início de sessão.

![página de início de sessão da aplicação restrita][2]

### <a name="scenario-3-contoso-employee-goes-to-a-contoso-app-thats-open-to-external-users"></a>Cenário 3: um funcionário da Contoso acede a uma aplicação da Contoso que está aberta a utilizadores externos

Neste exemplo, os utilizadores estão a iniciar sessão numa aplicação LoB da Contoso, mas podem ser ou não funcionários da empresa. A ilustração à esquerda representa o proprietário do recurso (Contoso), tal e qual o caso \#2, acima. No entanto, desta vez, o painel de interação do lado direito não está bloqueado para a Contoso, para passar a mensagem de que o início de sessão dos utilizadores externos é bem-vindo.

![iniciar sessão para abrir o acesso][3]

### <a name="scenario-4-fabrikam-business-guest-goes-to-contoso-app-thats-open-to-external-users"></a>Cenário de 4: o convidado empresarial Fabrikam acede à aplicação da Contoso que está aberta a utilizadores externos

Neste exemplo, um utilizador Contoso está a iniciar sessão numa aplicação interna com um URL específico da empresa. A ilustração à esquerda representa a marca da empresa (Contoso). O painel de interação à direita está bloqueado para a Contoso e ajuda os funcionários ao longo do início de sessão.

![iniciar sessão como utilizador externo][4]


## <a name="what-elements-on-the-page-can-i-customize"></a>Que elementos na página posso personalizar?

Pode personalizar os seguintes elementos na página de início de sessão:

![][5]

| Elemento da página | Localização na página |
|:--- | --- |
| Logótipo de Faixa | Mostrado na parte superior direita da página. Substitui o logótipo da aplicação assim que a organização do utilizador for determinada (normalmente, depois de introduzido o nome de utilizador). |
| Ilustração de fundo | Mostrada como imagem de transbordo completo no lado esquerdo da página de início de sessão. Substitui a ilustração da aplicação, para cenários de início de sessão em inquilinos (quando os utilizadores acedem a uma aplicação publicada pela sua própria organização ou por uma organização da qual sejam convidado empresarial).<br>Em ligações de largura de banda baixa, a ilustração de fundo é substituída por uma cor de fundo. Em ecrãs estreitos, como os dos telemóveis, não é apresentada.<br>A ilustração de fundo será recortada se os utilizadores redimensionarem o browser. Ao desenhar a ilustração, mantenha os elementos visuais-chave no canto superior esquerdo, para que não sejam recortados. | 
| Caixa de verificação Manter a sessão iniciada | Apresentada abaixo da caixa de texto **Palavra-passe**. |
| Texto da página de início de sessão | Texto automático a ser apresentado acima do rodapé da página Pode ser útil para transmitir informações úteis aos seus utilizadores, como o número de telefone do suporte técnico ou uma declaração legal. |

> [!NOTE]
> Todos os elementos são opcionais. Por exemplo, se especificar um Logótipo de Faixa, mas não uma Ilustração de Fundo, a página de início de sessão mostra o seu logótipo e a ilustração para o site de destino (por exemplo, a imagem de autoestrada da Califórnia do Office 365).
>

Na sua página de início de sessão, a caixa de verificação **Manter a sessão iniciada** permite que os utilizadores permaneçam com a sessão iniciada depois de fecharem e reabrirem o browser e não afeta a duração da sessão.

A apresentação ou não da caixa de verificação depende da definição de **Ocultar KMSI (Keep Me Signed In)**.

![Definição Ocultar KMSI][6]

Para ocultar a caixa de verificação, configure esta definição como **Oculta**.

> [!NOTE]
> Algumas funcionalidades do SharePoint Online e do Office 2010 dependem da capacidade de os utilizadores poderem selecionar esta caixa de verificação. Se configurar esta definição como oculta, os seus utilizadores poderão ver avisos de início de sessão adicionais e inesperados.
>
>

Também pode localizar todos os elementos nesta página. Depois de configurar um conjunto “predefinido” de elementos de personalização, pode configurar mais versões para diferentes regiões. Também pode misturar e combinar os vários elementos. Pode, por exemplo:

* Criar uma ilustração “predefinida” que funciona para todas as culturas e criar, depois, versões específicas em inglês e francês. Quando definir os seus browsers para um destes dois idiomas, aparece a imagem específica, ao passo que a ilustração predefinida é apresentada para todos os outros idiomas.
* Configurar logótipos diferentes para a sua organização (por exemplo, versões em japonês ou hebraico).

## <a name="access-panel-page-customization"></a>Personalização de página do painel de acesso
A página do Painel de Acesso é essencialmente uma página de portal para um acesso rápido às aplicações da nuvem a que o seu administrador lhe concedeu acesso. Nesta página, as suas aplicações são apresentados como mosaicos clicáveis de aplicações.

A seguinte captura de ecrã mostra um exemplo de uma página do painel de acesso após a personalização.

![][8]

## <a name="configure-your-directory-with-company-branding"></a>Configurar o diretório da sua empresa com a imagem corporativa
Pode configurar um conjunto predefinido de elementos personalizáveis por diretório no Portal Clássico do Azure. Uma vez guardadas as predefinições, um administrador pode adicionar versões localizadas de cada elemento, para idiomas / regiões diferentes. Todos os elementos personalizáveis são opcionais.

Por exemplo, se configurar um Logótipo de Faixa predefinido, mas não uma Ilustração Grande, a página de início de sessão apresenta o seu logótipo no canto superior direito. É no entanto apresentada a ilustração predefinida do site.

Imagine a seguinte configuração:

* Um Logótipo de Faixa e Texto da Página de Início de Sessão predefinidos em inglês
* Um Texto da Página de Início de Sessão específico para o alemão

Se a sua preferência de idioma é alemão, verá o Logótipo de Faixa predefinido, mas o texto em alemão.

Embora possa tecnicamente configurar um conjunto diferente para cada idioma suportado pelo Azure AD, recomendamos que mantenha o número de variações baixo, por motivos de manutenção e desempenho.
 
**Para adicionar a imagem corporativa da sua empresa ao seu diretório, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
5. Modifique os elementos que pretende personalizar. Todos os campos são opcionais.
6. Clique em **Guardar**.

Pode demorar até uma hora para que apareça a nova alteração que efetuou na imagem corporativa da página de início de sessão.

**Para adicionar uma imagem corporativa específica do idioma, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
fs3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
5. Clique em **Adicionar imagem corporativa para um idioma específico**.
6. Selecione o idioma para que pretende personalizar o logótipo e clique em **Seguinte**.
7. Edite apenas os elementos para que pretende configurar substituições específicas do idioma. Todos os campos são opcionais. Se um campo for deixado em branco, é apresentado o valor predefinido personalizado (ou a predefinição da Microsoft, se não tiver configurado um personalizado predefinido).
8. Clique em **Guardar**.

**Para remover a imagem corporativa da sua empresa do seu diretório, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
5. Na página Personalizar uma Imagem Corporativa, selecione **Editar Definições da Imagem Corporativa Existente** e vá para a página seguinte.
6. Dependendo dos elementos que pretende remover, efetue um ou mais dos seguintes procedimentos:

    a. Em **Logótipo de Faixa**, selecione **Remover o logótipo carregado**.

    b. Em **Logótipo de Mosaico**, selecione **Remover o logótipo carregado**.

    c. Remova o texto de todas as caixas de texto.

    d. Clique em **Seguinte**.

    e. Remova o texto de todas as caixas de texto.
7. Clique em **Guardar** para remover os elementos.
8. Se necessário, clique novamente em **Personalizar uma Imagem Corporativa** e repita estes passos para todas as imagens corporativas específicas do idioma que têm de ser removidas.
    Todas as definições de imagem corporativa foram removidas quando clicar em **Personalizar uma Imagem Corporativa** e vir o formulário **Personalizar Imagem Corporativa Predefinida** sem definições existentes configuradas.


## <a name="customizable-elements"></a>Elementos personalizáveis
Os logótipos empresariais são utilizados para as páginas de início de sessão e do Painel de Acesso, enquanto outros elementos só são utilizados na página de início de sessão. A tabela seguinte fornece detalhes para os diferentes elementos personalizáveis.

| Nome | Descrição | Restrições | Recomendações |
| --- | --- | --- | --- |
| Logótipo de faixa |O logótipo de faixa é apresentado na página de início de sessão e no Painel de Acesso. |<p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 KB</p> |<p>Utilize o logótipo completo da sua organização (incluindo pictograma e logótipo)</p><p>Mantenha uma altura inferior a 30 pixeis para evitar a introdução de barras de deslocamento em dispositivos móveis</p><p>Mantenha-a inferior a 4 KB</p><p>Utilize um PNG transparente (não parta do princípio de que a página de início de sessão tem sempre um fundo branco)</p> |
| Logótipo de mosaico | Não utilizado atualmente |<p>JPG ou PNG</p><p>120 x 120 pixéis</p><p>10 KB</p> |<p>Mantenha-o simples (sem texto pequeno), uma vez que esta imagem poderá ser redimensionada para 50% |
| </p> | | | |
| Etiqueta de nome de utilizador de início de sessão | Não utilizado atualmente |<p>Texto Unicode, até 50 carateres</p><p>Apenas texto simples (sem ligações ou tags de HTML)</p> |<p>Mantenha-o curto e simples</p><p>Peça aos seus utilizadores como se costumam referir à conta profissional ou escolar que lhes fornece.</p> |
| Texto automático de página de início de sessão |Este texto automático aparece por baixo do formulário da página de início de sessão e pode ser utilizado para comunicar instruções adicionais ou onde obter ajuda e suporte. |<p>Texto Unicode, até 256 carateres</p><p>Apenas texto simples (sem ligações ou tags de HTML)</p> |Mantenha-o abaixo dos 250 carateres (cerca de 3 linhas de texto) |
| Ilustração de fundo da página de início de sessão | Imagem grande que é apresentada no lado esquerdo da página de início de sessão (no lado direito, para idiomas da direita para a esquerda) quando os utilizadores acedem a URLs de inquilinos específicos. |<p>JPG ou PNG</p><p>1420 x 1200</p><p>500 KB</p> |<p>1420 x 1200 pixéis</p><p>Importante: mantê-la o mais pequena possível, idealmente abaixo de 200 KB. Se esta imagem for demasiado grande, o desempenho da página de Início de Sessão é afetado se a imagem não estiver em cache</p><p>Esta imagem será quase sempre recortada, para suportar diferentes proporções de ecrã. Mantenha os elementos visuais principais no canto superior esquerdo.</p> |
| Cor de fundo da página de início de sessão | Em ligações de largura de banda baixa, esta cor sólida é utilizada em vez da ilustração de fundo. | Tem de ser uma cor RGB numa forma hexadecimal (exemplo: \#FFFFFF) | Sugerimos que escolha a cor primária do logótipo de faixa. |

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
* [Ver os relatórios de acesso e utilização](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/signin-page_before-customization.png
[2]: ./media/active-directory-add-company-branding/signin-page-restricted-app.png
[3]: ./media/active-directory-add-company-branding/signin-page-open-access.png
[4]: ./media/active-directory-add-company-branding/signin-page-external-guest.png
[5]: ./media/active-directory-add-company-branding/which-elements-can-i-customize.png
[6]: ./media/active-directory-add-company-branding/hide-kmsi.png
[8]: ./media/active-directory-add-company-branding/APBranding.png
[9]: ./media/active-directory-add-company-branding/hidekmsi.png

