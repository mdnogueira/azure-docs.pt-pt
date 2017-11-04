---
title: "As aplicações no mercado de gerido do Azure | Microsoft Docs"
description: "Descreve Azure as aplicações que estão disponíveis através do Marketplace gerido."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/20/2017
ms.author: gauravbh
ms.openlocfilehash: b4fda06f85c7dab52ff38558b0d928193e0694f6
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Aplicações geridas do Azure no Marketplace

Os fornecedores podem utilizar o Azure geridos aplicações para oferecer as soluções para todos os clientes do Azure Marketplace. Os fornecedores podem incluir fornecedores de serviços geridos (MSPs), os fabricantes independentes de software (ISV) e integradores de sistema (SIs). Aplicações geridas reduzem a manutenção e a sobrecarga de manutenção para os clientes. Os fornecedores propor infraestrutura e de software através do marketplace. Pode anexar os serviços e operacional suporte para aplicações geridas. Para obter mais informações, consulte [descrição geral de aplicações gerido](overview.md).

Este artigo explica como pode publicar uma aplicação no Marketplace e que fiquem amplamente disponíveis aos clientes.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Pré-requisitos para publicar uma aplicação gerida

Para concluir este artigo, já tem de ter o ficheiro. zip para a sua definição de aplicações geridas. Para obter mais informações, consulte [Criar aplicação do catálogo de serviço](publish-service-catalog-app.md).

Além disso, existem vários pré-requisitos de negócio. São:

* Da sua empresa ou a sua subsidiária tem de estar localizada num país/região onde vendas são suportadas no marketplace.
* O produto têm de estar licenciado de uma forma que é compatível com modelos de faturação suportados no marketplace.
* Disponibilizar suporte técnico para clientes de forma comercialmente razoável. O suporte pode ser livre, paga, ou através da Comunidade suportar.
* Licença do software e as dependências de software de terceiros.
* Forneça o conteúdo que cumpra os critérios para a sua oferta seja apresentado no Marketplace e no portal do Azure.
* Aceite os termos do contrato do publicador e políticas de participação do Azure Marketplace.
* Concorda em estar em conformidade com os termos de utilização, declaração de privacidade da Microsoft e contrato de programa de certificados do Microsoft Azure.

## <a name="set-up-your-account-for-publishing-portal"></a>Configurar a conta para portal de publicação

O portal de publicação é utilizado para publicar e gerir a sua offer(s). Para publicar uma aplicação de marketplace, tem de ter um Developer Microsoft aprovados para o Azure Marketplace. Se ainda não registou para uma conta aprovada, consulte [criar uma conta Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

Se tiver um aprovados **Microsoft Developer Center** conta, mas não tiver utilizado anteriormente a [Portal de publicação do Azure](https://cloudpartner.azure.com/), tem de se registar para o portal de publicação.

1. Abra uma nova Chrome Incognito ou Internet Explorer InPrivate navegação sessão para se certificar de que tem não sessão iniciada para uma conta pessoal.
2. Aceda a [https://cloudpartner.azure.com/](https://cloudpartner.azure.com/).
3. Se for um novo utilizador e o início de sessão para a publicação portal pela primeira vez, em seguida, deve iniciar sessão com o mesmo ID de correio eletrónico como a sua conta do Centro de programadores. Agora, conta de centro de programadores e conta de publicação de portal estão ligados.

Posteriormente, pode adicionar outros membros da empresa como um [coadministrador](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md#4-steps-to-add-a-co-admin-in-the-publishing-portal) no portal de publicação. Se é adicionado como coadministrador no portal de publicação, pode iniciar sessão com a sua conta de co-admin.

> [!TIP]
> As políticas de participação são descritas no [Web site Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="create-a-new-azure-application-offer"></a>Criar uma nova oferta de aplicação do Azure

Após cumprir os pré-requisitos, está pronto para criar a sua oferta da aplicação gerida.

### <a name="set-up-an-offer"></a>Configurar uma oferta

A oferta para uma aplicação gerida corresponde a uma classe de produto de um fabricante da oferta. Se tiver um novo tipo de aplicação que pretende tornar disponível no mercado, pode configurá-lo como uma nova oferta. Uma oferta é uma coleção de SKUs. Cada oferta aparece como o seu próprio entidade no marketplace.

1. Iniciar sessão para o [portal de parceiros de nuvem](https://cloudpartner.azure.com/).

1. No painel de navegação à esquerda, selecione **+ nova oferta** > **aplicações do Azure**.

   ![Nova oferta](./media/publish-marketplace-app/newOffer.png)

1. No **Editor** vista, consulte os formulários necessários. Cada formulário é descrito mais à frente neste artigo.

   ![Definições de oferta](./media/publish-marketplace-app/newOffer_OfferSettings.png)

## <a name="offer-settings-form"></a>Formulário de definições da oferta

Os campos para o **oferecem definições** formulário são:

* **Oferecer ID**: este identificador exclusivo identifica a oferta dentro de um perfil do publicador. Este ID é visível no produto URLs, modelos do Resource Manager, e relatórios de faturação. Só podem ser composta de carateres alfanuméricos minúsculos ou traços (-). O ID não pode terminar com um travessão. De limitada a um máximo de 50 carateres. Depois de uma oferta fica em direto, este campo está bloqueado.
* **ID de publicador**: Utilize esta lista pendente para escolher o perfil de publicador que pretende publicar esta oferta em. Depois de uma oferta fica em direto, este campo está bloqueado.
* **Nome**: este nome a apresentar para a sua oferta é apresentada no Marketplace e através do portal. Pode ter um máximo de 50 carateres. Incluem um nome de marca reconhecível para o produto. Não inclua o nome da sua empresa aqui, a menos que seja como for marketed. Se estiver a marketing esta oferta no seu próprio site, certifique-se de que o nome exatamente como é apresentado no seu Web site.

Quando terminar, selecione **guardar** para guardar o seu progresso.

## <a name="skus-form"></a>SKUs de formulário

O passo seguinte consiste em Adicionar SKUs para a sua oferta.

Um SKU é a unidade purchasable mais pequeno de uma oferta. Pode utilizar um SKU dentro da mesma classe de produto (oferta) para diferenciar entre:

* Funcionalidades diferentes que são suportadas
* Indica se a oferta é gerida ou não gerida
* Modelos de faturação que são suportados

Um SKU aparece sob a oferta de principal no marketplace. É apresentado como o seu próprio entidade purchasable no portal do Azure.

1. Selecione **SKUs** > **SKU novo**.

   ![Selecione o novo SKU](./media/publish-marketplace-app/newOffer_skus.png)

1. Introduza um **ID do SKU**. Um ID de SKU é um identificador exclusivo para o SKU dentro de uma oferta. Este ID é visível no produto URLs, modelos do Resource Manager, e relatórios de faturação. Só podem ser composta de carateres alfanuméricos minúsculos ou traços (-). O ID não pode terminar com um travessão e de limitada a um máximo de 50 carateres. Depois de uma oferta fica em direto, este campo está bloqueado. Pode ter vários SKUs dentro de uma oferta. É necessário um SKU de cada imagem que pretende publicar.

1. Preencha o **detalhes de SKU** secção no seguinte formato:

   ![Forneça o SKU de novo](./media/publish-marketplace-app/sku-settings.png)

   Preencha os campos seguintes:

   * **Título**: introduza um título para este SKU. Este título é apresentado na Galeria para este item.
   * **Resumo**: introduza um breve resumo para esta SKU. Este texto é apresentado sob o título.
   * **Descrição**: introduza uma descrição detalhada sobre o SKU.
   * **Tipo de SKU**: os valores permitidos são *aplicações geridas* e *modelos de solução*. Neste caso, selecione *aplicações geridas*.
   * **Disponibilidade do país/região**: selecione países/regiões onde a aplicação gerida está disponível.

      ![Selecione países](./media/publish-marketplace-app/select-country.png)

   * **Preços**: forneça um preço para a gestão da aplicação. Selecione os países disponíveis antes de definir o preço.

1. Adicione um novo pacote. Preencha o **detalhes do pacote** secção no seguinte formato:

   ![Pacote](./media/publish-marketplace-app/new-package.png)

   Preencha os campos seguintes:

   * **Versão atual**: introduza uma versão para o pacote que carrega. Deverá estar no formato `{number}.{number}.{number}{number}`.
   * **Selecione um ficheiro de pacote**: Este pacote contém o ficheiro comprimido para um pacote. zip. Para obter mais informações, consulte [Criar aplicação do catálogo de serviço](publish-service-catalog-app.md).
   * **PrincipalId**: Esta propriedade é o identificador do Azure Active Directory (Azure AD) de um utilizador, o grupo de utilizadores ou a aplicação que é concedida acesso aos recursos na subscrição do cliente. A definição de função descreve as permissões.
   * **Definição de função**: Esta propriedade é uma lista de todos os as controlo de acesso baseado em funções (RBAC) funções incorporadas fornecidas pelo Azure AD. Pode selecionar a função que é mais adequada utilizar para gerir os recursos em nome do cliente.

Pode adicionar vários autorizações. Recomendamos que crie um grupo de utilizadores do AD e especificar o respetivo ID em **PrincipalId**. Desta forma, pode adicionar mais utilizadores para o grupo de utilizadores sem a necessidade de atualizar o SKU.

Para obter mais informações sobre o RBAC, consulte [começar a utilizar o RBAC no portal do Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulário do Marketplace

O formulário de Marketplace pede-lhe campos que apareçam no [Azure Marketplace](https://azuremarketplace.microsoft.com) e o [portal do Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>IDs de subscrição de pré-visualização

Introduza uma lista de IDs, que pode aceder à oferta depois da sua publicação de subscrição do Azure. Pode utilizar estas subscrições listados em branco para testar a oferta previewed antes de torná-lo em direto. Pode compilar uma lista de permissão de até 100 subscrições no portal de parceiros.

### <a name="suggested-categories"></a>Categorias sugeridas

Selecione até cinco categorias da lista que oferta pode ser melhor associada. Estas categorias são utilizadas para mapear a oferta para as categorias de produtos que estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com) e [portal do Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

O resumo da sua aplicação gerida apresenta os seguintes campos:

![Resumo do Marketplace](./media/publish-marketplace-app/publishvm10.png)

O **descrição geral** separador para a sua aplicação gerida apresenta os seguintes campos:

![Descrição geral do Marketplace](./media/publish-marketplace-app/publishvm11.png)

O **planos + preços** separador para a sua aplicação gerida apresenta os seguintes campos:

![Planos de Marketplace](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Portal do Azure

O resumo da sua aplicação gerida apresenta os seguintes campos:

![Resumo do portal](./media/publish-marketplace-app/publishvm12.png)

A descrição geral da sua aplicação gerida apresenta os seguintes campos:

![Descrição geral do portal](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Diretrizes de logótipo

Siga estas diretrizes para qualquer logótipo que carregar no portal de parceiros de Cloud:

*   A estrutura do Azure tem uma paleta de cores simples. Limite o número de principal e secundário cores no seu logótipo.
*   As cores de tema do portal são brancos e marcar a negrito. Não utilize estes cores como a cor de fundo para o logótipo. Utilize uma cor que faz com que o seu logótipo prominent no portal. Recomendamos simples de principal de cores. *Se utilizar um fundo transparente, certifique-se de que o logótipo e o texto não são brancos, negra ou azul.*
*   Não utilize um fundo gradação no logótipo.
*   Não coloque o texto no logótipo, nem mesmo a empresa ou o nome de marca. O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações.
*   Certifique-se que o logótipo não se encontra stretched (ampliada).

#### <a name="hero-logo"></a>Logótipo de heroína

O logótipo heroína é opcional. O fabricante pode optar por não carregar um logótipo de heroína. Depois do ícone de heroína é carregado, não pode ser eliminada. Nessa altura, o parceiro tem de seguir as diretrizes de Marketplace para ícones heroína.

Siga estas diretrizes para o ícone de logótipo heroína:

*   O nome de apresentação do publicador, o título do plano e a oferta longa resumo são apresentados em branco. Por conseguinte, não utilize uma cor leve para a imagem de fundo do ícone heroína. Não é permitido um fundo branco, preto ou transparente para ícones heroína.
*   Depois da oferta é listada, elementos são incorporados programaticamente dentro o logótipo heroína. Os elementos incorporados incluem o nome de apresentação do publicador, o título do plano, a oferta longa resumo e o **criar** botão. Por conseguinte, não introduza quaisquer texto ao estruturar o logótipo heroína. Deixe o espaço em branco à direita porque o texto é através de programação desse espaço. O espaço em branco para o texto deve ser 415 x 100 pixéis à direita. É de deslocamento em 370 pixéis da esquerda.

    ![Exemplo de logótipo heroína](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Formulário de suporte

Preencha o **suporta** contacta de formulário com o suporte da sua empresa. Estas informações podem ser engenharia contactos e contactos de suporte ao cliente.

## <a name="publish-an-offer"></a>Publicar uma oferta

Depois de preencher todas as secções, selecione **publicar** para iniciar o processo que disponibiliza a oferta para clientes.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral de aplicações gerido](overview.md).
* Para obter informações sobre como publicar uma aplicação do catálogo de serviço geridas, consulte [criar e publicar uma aplicação do catálogo de serviço geridas](publish-service-catalog-app.md).
