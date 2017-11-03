---
title: Criar e registar a conta de publicador | Microsoft Docs
description: "Instruções para criar uma conta Microsoft Developer, pelo que, após a aprovação, pode propor vários oferecem tipos no Azure Marketplace."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-microsoft-developer-account"></a>Criar uma conta Microsoft Developer
Este artigo orienta-o através da criação de contas necessárias e o processo de registo para se tornar um Developer Microsoft aprovados para o Azure Marketplace.

## <a name="1-create-a-microsoft-account"></a>1. Criar conta Microsoft
Para iniciar o processo de publicação, terá de criar uma conta Microsoft. Esta conta será utilizada para registar para ambos os **Microsoft Developer Center** e **Portal de publicação do Azure**. Deve ter apenas uma conta Microsoft para as ofertas do Azure Marketplace. Não deve ser específico para serviços ou ofertas.

O endereço de que forma o nome de utilizador deve estar no seu domínio e controladas pela sua equipa de TI. Todas as atividades relacionadas com publicação devem ser feitas esta conta.

> [!WARNING]
> Palavras como **"Azure"** e **"Microsoft"** não são suportadas para o registo da conta Microsoft. Evite utilizar estas palavras para concluir a criação da conta e o processo de registo.
>
>

### <a name="guidelines-for-company-accounts"></a>Diretrizes para contas de acesso da empresa
Ao criar uma conta da empresa, siga estas diretrizes se mais do que uma pessoa precisar de aceder a conta ao iniciar sessão com a conta Microsoft que abrir a conta.

> [!Important]
> Importante para permitir que vários utilizadores a aceder à sua conta do Dev Center, recomendamos a utilização do Azure Active Directory para atribuir funções a utilizadores individuais, quem podem aceder a conta ao iniciar sessão com as respetivas indivíduo credenciais do Azure AD. Para obter mais informações, consulte [gerir os utilizadores da conta](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users).

* Criar a sua conta Microsoft com um endereço de e-mail que pertence ao domínio da sua empresa, mas não até um indivíduo único — por exemplo, windowsapps@fabrikam.com.
* Limitar o acesso a esta conta Microsoft para o menor número possível de programadores.
* Configurar uma lista de distribuição de e-mail empresarial que inclui todos os utilizadores que necessitam de aceder à conta de programador e adicionar este endereço de e-mail às suas informações de segurança. Isto permite que todos os funcionários na lista para receber os códigos de segurança quando necessário e gerir informações de segurança da sua conta Microsoft. Se configurar uma lista de distribuição não for viável, o proprietário da conta de e-mail individuais terá de estar disponível para aceder e partilhar o código de segurança quando lhe for pedido (por exemplo, quando novas informações de segurança é adicionada à conta ou quando este tem de ser acedido a partir de um dispositivo novo).
* Adicione um número de telefone da empresa que não necessita de uma extensão e está acessível para os membros da equipa de chave.
* Ter em geral, os programadores utilizar dispositivos fidedignos para iniciar sessão conta de programador da sua empresa. Todos os membros da equipa de chave devem ter acesso a estes dispositivos fidedignos. Isto irá reduzir a necessidade de códigos de segurança para serem enviados ao aceder a conta.
* Se precisar de permitir o acesso à conta de um computador não fidedigno, limite o que o acesso a um máximo de cinco programadores. Idealmente, estes programadores devem aceder à conta de máquinas que partilhar o mesmo geográfica e a localização de rede.
* Frequentemente, reveja as informações de segurança da sua empresa em [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) para se certificar de que está tudo atual.

A conta de programador deve ser acedida principalmente a partir de PCs fidedignos. Isto é crucial, porque não há um limite ao número de códigos gerados por conta, por semana. Também permite que a experiência de início de sessão mais integrada.

Para obter mais informações sobre as diretrizes de conta de programador adicionais e segurança, clique em [aqui](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Instruções
1. Abra uma nova Chrome Incognito ou Internet Explorer InPrivate navegação sessão para se certificar de que tem não sessão iniciada numa conta existente.
2. Registar a mensagem de e-mail (pelas diretrizes acima por exemplo, windowsapp@fabrikam.com) como uma conta Microsoft, utilizando a hiperligação [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx). Siga as instruções abaixo.

   1. Durante a registar a sua conta como uma conta Microsoft, tem de fornecer um número de telefone válido para o sistema para lhe enviar um código de verificação de conta como uma mensagem de texto ou chamada automática.
   2. Durante a registar a sua conta como uma conta Microsoft, tem de indicar um id de correio eletrónico válido para receber uma mensagem de e-mail automatizada para verificação da conta.
3. Certifique-se o endereço de e-mail enviado para o ld.
4. Agora, está pronto para utilizar a nova conta Microsoft no Microsoft Developer Center.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Registar a sua conta no Microsoft Developer Center
A Microsoft Developer Center é utilizado para registar as informações da empresa uma vez. O registrant tem de ser um representante válido da empresa e tem de fornecer as suas informações pessoais como uma forma de validar a sua identidade. A pessoa que está a registar tem de utilizar uma conta Microsoft que é partilhada da empresa, **e a mesma conta deve ser utilizada no Portal de publicação do Azure.** Deve verificar para se certificar de que a sua empresa ainda não tiver uma conta Microsoft Developer Center antes de tentar criar um. Durante o processo, iremos recolher informações de endereço de empresa, informações de conta do banco e impostos informações. Normalmente, estas são obtidas por meio de contactos financeiros ou profissionais.

> [!IMPORTANT]
> Tem de concluir os seguintes componentes de perfil de programador para percorre as fases de criação de oferta e a implementação.
>
>

| Perfil de programador | Para iniciar o rascunho | Transição | Publicar livre e modelo de solução | Publicar comerciais |
| --- | --- | --- | --- | --- |
| Registo da empresa |Tem de ter |Tem de ter |Tem de ter |Tem de ter |
| ID de perfil dedução dos impostos |Opcional |Opcional |Opcional |Tem de ter |
| Conta de Bank |Opcional |Opcional |Opcional |Tem de ter |

> [!NOTE]
> Traga a sua própria licença (BYOL) só é suportada para máquinas virtuais e é considerado um **livre** oferta.
>
>

### <a name="register-your-company-account"></a>Registar a sua conta de empresa
1. Abra uma nova Internet Explorer InPrivate ou Incognito Chrome navegação sessão para se certificar de que tem não sessão iniciada para uma conta pessoal.
2. Aceda a [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) para registar-se como um vendedor no Dev Center. Leia a seguinte nota importante antes de continuar.

   > [!IMPORTANT]
   > Certifique-se de que o id ou a distribuição de lista de e-mail (uma lista de distribuição é recomendada para remover a dependência indivíduos) que irá utilizar para registar no Dev Center no primeiro registado como uma conta Microsoft. Se não estiver, em seguida, registe-se utilizar esta [ligação](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Além disso, **qualquer id sob o domínio de empresa do Microsoft de e-mail ou seja, @microsoft.com não é possível utilizar** para o registo de Dev Center.
   >
   >

    ![desenho][img-signin]
3. Conclua o assistente "Ajuda-na proteger a sua conta", que irá verificar a sua identidade através do telefone número ou endereço de e-mail.

    ![desenho][img-verify]
4. Na secção "Informações de conta de registo", selecione o **país/região da conta** no menu de lista pendente.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Vende de" países:** para propor os serviços no Azure Marketplace, entidade registada tem de ser um dos aprovados "vende-de" países/regiões acima. Esta restrição é por razões de dividendos e taxation. Iremos ativamente procura para expandir esta lista de países num futuro próximo, por isso, esteja atento e não. Para obter mais informações, consulte o [políticas de participação de Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Selecione o "tipo de conta" como **empresa** e, em seguida, clique em de **seguinte** botão.

   > [!IMPORTANT]
   > Para compreender melhor os tipos de conta e que é melhor para escolher, visualizar página [taxas, tipos e localizações de conta](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Introduza o **nome a apresentar do publicador**, normalmente, o nome da sua empresa.

   > [!TIP]
   > O nome de apresentação do publicador introduzido no Dev Center não é apresentado no Azure Marketplace, assim que a sua oferta fica listada. Mas, este tem de ser preenchido para concluir o processo de registo.
   >
   >
7. Introduza o **informação de contacto** para a verificação de conta.

   > [!IMPORTANT]
   > Tem de fornecer informações de contacto exata porque será utilizado no nosso processo de verificação para a sua empresa seja aprovada no Centro de programadores.
   >
   >
8. Introduza as informações de contactos para o **aprovador da empresa**. O aprovador da empresa é a pessoa que pode verificar se está autorizado para criar uma conta no Dev Center em nome da sua organização. Clique em **seguinte** mover para o **"Secção de pagamento"** quando tiver terminado.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Introduza as suas informações de pagamento pagar para a sua conta. Se tiver um código de promoção que abrange o custo de registo, pode introduzir que aqui. Caso contrário, fornece aos informações de cartão de crédito (ou PayPal no mercados suportados). Quando tiver terminado, clique em **seguinte** mover para o **"Ecrã de revisão"**.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Reveja as suas informações de conta e confirme que tudo está correto. Em seguida, leia e aceite os termos e condições do [contrato do Microsoft Azure Marketplace publicador](http://go.microsoft.com/fwlink/?LinkID=699560). Selecione a caixa para indicar que tenha lido e aceite estes termos.
11. Clique em **concluir** para confirmar o seu registo. Enviaremos uma mensagem de confirmação para o seu endereço de correio eletrónico.
12. Se estiver a planear publicar ofertas apenas livres, clique em **aceda ao Portal do Azure Marketplace publicação** e poderá avançar para a secção 3 deste documento, [registar a sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal).

Se estiver a planear a publicação comercial oferece (por exemplo, ofertas de Máquina Virtual com o modelo de faturação por hora), clique em **atualizar as suas informações de conta** onde, tem de preencher a dedução dos impostos e informações na sua conta do Centro de programadores.

Se preferir Atualize as informações dedução dos impostos e banco mais tarde, em seguida, pode mover para a secção seguinte, ou seja, a secção 3 deste documento, [registar a sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal)e ficar mais tarde, utilizando ligações no Portal de publicação do Azure.

> [!IMPORTANT]
> Em caso de ofertas de comerciais, não será capaz de push as ofertas para produção sem concluir as informações de dedução dos impostos e banco conta.
>
>

Se preferir Atualize as informações dedução dos impostos e banco mais tarde, pode aceder à secção 3, [registar a sua conta no portal de publicação](#3-register-your-account-in-the-publishing-portal)e ficar mais tarde, utilizando ligações no Portal de publicação do Azure.

### <a name="add-tax-and-banking-information"></a>Adicionar dedução dos impostos e informações de bancos
 Se pretender publicar comerciais ofertas de compra, também terá de adicionar dividendos dedução dos impostos informações e submetê-las para a validação no Centro de programadores. Se irá publicar ofertas apenas livres (ou BYOL oferece), em seguida, não terá de adicionar estas informações. Pode adicioná-lo mais tarde, mas demora algum tempo para validar as informações de dedução dos impostos. Se souber que irá oferecer comerciais ofertas de compra, recomendamos que o adicione logo que possível.

**Informações de Bank**

1. Iniciar sessão para o [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) com a sua conta Microsoft.
2. Clique em **conta dividendos** no menu à esquerda, em **escolher método de pagamento** clique **banco conta** ou **PayPal**.

   > [!IMPORTANT]
   > Se tiver comerciais ofertas aos clientes comprar no Marketplace, esta é a conta em que irá receber dividendos para esses compras.
   >
   >
3. Introduza as informações de pagamento e clique em **guardar** quando estiver satisfeito.

   > [!IMPORTANT]
   > Se precisar de atualizar ou alterar a sua conta de dividendos, siga os mesmos passos acima, substituindo as informações atuais com as informações de novo. Alterar a sua conta de dividendos pode atrasar os pagamentos pelo máximo de um ciclo de pagamento. Este atraso ocorre porque é necessário verificar a alteração da conta, tal como foi feito ao configurar a conta de dividendos primeiro. Pode ainda irá obter paga para a quantidade total após a sua conta foi verificada; qualquer pagamentos devido para o pagamento atual ciclo será adicionado ao seguinte.
   >
   >
4. Clique em **Seguinte**.

**Informações de dedução dos impostos**

1. Iniciar sessão para o [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) com a sua conta Microsoft (se necessário).
2. Clique em **dedução dos impostos perfil** no menu esquerdo.
3. No **configurar do formulário dedução dos impostos** página, selecione o país ou região onde tenha residency permanente e, em seguida, selecione o país ou região onde espera que aceita primária. Clique em **Seguinte**.
4. Introduza os detalhes da sua dedução dos impostos e, em seguida, clique em **seguinte**.

> [!WARNING]
> Não será capaz de push para produção a comercial oferece sem concluir a dedução dos impostos e conta banco as informações na sua conta Microsoft Developer Center.
>
>

Se tiver problemas com o registo do Centro de programadores, inicie um pedido de suporte, tal como indicado abaixo

1. Aceda ao link do suporte [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. Em **contacte-na** secção, clique no botão **submeter um incidente** (conforme ilustrado na captura de ecrã abaixo)

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Escolha "Ajudar com Dev Center" como **tipo de problema** e "publicar e gerir aplicações" como **categoria**. Depois disso clique no botão "Iniciar e-mail".

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. Será fornecido com uma página de início de sessão. Utilize qualquer Microsoft conta de início de sessão. Se não tiver uma conta Microsoft, em seguida, crie um usando isto [ligação](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Preencha os detalhes do problema e subit a permissão ao clicar no **submeter** botão.

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. Registar a sua conta no portal de publicação
O [publicação portal](http://publish.windowsazure.com) é utilizado para publicar e gerir a sua offer(s).

1. Abra uma nova Chrome Incognito ou Internet Explorer InPrivate navegação sessão para se certificar de que tem não sessão iniciada para uma conta pessoal.
2. Aceda a [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Se for um novo utilizador e o início de sessão para a publicação portal pela primeira vez, em seguida, deve iniciar sessão com o mesmo id de correio eletrónico na qual a sua conta do Dev Center está registada. Desta forma sua conta do Dev Center e a conta do portal de publicação serão ligado entre si. Posteriormente, pode adicionar os outros membros da empresa, que estiver a trabalhar na aplicação, como um coadministrador na publicação portal seguindo os passos abaixo.

Se é adicionado como coadministrador na publicação portal, em seguida, pode iniciar sessão com a sua conta de co-admin.

> [!TIP]
> As políticas de participação são descritas no [Web site Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Passos para adicionar um coadministrador na publicação portal
**Partindo do princípio que são o administrador,** indicados abaixo estão os passos para adicionar um co-administrador.

> [!NOTE]
> **Para os novos utilizadores** antes de adicionar um coadministrador na publicação portal, certifique-se de que criou, pelo menos, uma aplicação a publicação do portal. Isto é necessário como o **PUBLICADORES** separador são apresentados apenas depois de criar pelo menos uma aplicação na publicação portal.
>
>

1. Certifique-se de que o id de correio eletrónico coadministrador um account(MSA) da Microsoft. Se não estiver, registe-o como uma MSA com este [ligação](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Certifique-se de que existe pelo menos uma aplicação com a conta de administrador antes de tentar adicionar um co-administrador.
3. Depois de terminar os passos acima, início de sessão para a publicação portal com o id de correio eletrónico de coadministrador e, em seguida, a fim de sessão.
4. Agora início de sessão para a publicação portal com o id de correio eletrónico de admin.
5. Navegue para publicadores -> selecione a sua conta -> administradores -> Adicionar coadministrador (captura de ecrã indicada abaixo)

   ![desenho](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. Passos para eliminar um coadministrador na publicação portal
**Partindo do princípio que são o administrador,** indicados abaixo estão os passos para eliminar um co-administrador.

1. Início de sessão para a publicação portal com o id de correio eletrónico de admin.
2. Navegue para **publicadores** -> selecione a sua conta -> **administradores** -> **Coadministradores**.
3. Clique em de **X** junto coadministrador que pretende eliminar tot (captura de ecrã indicada abaixo).

    ![desenho](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>Passos seguintes
Agora que a conta é criada e registada, certifique-se de satisfazer ou corresponder a todos os pré-requisitos não técnico para publicar a sua oferta revendo [pré-requisitos não técnicos](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
