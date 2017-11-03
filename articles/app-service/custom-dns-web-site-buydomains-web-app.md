---
title: "Compre um nome de domínio personalizado para Web Apps do Azure"
description: "Saiba como comprar um nome de domínio personalizado com uma aplicação web no App Service do Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 3cb22b935624041ab51e64028a1b668fd694f9b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Compre um nome de domínio personalizado para Web Apps do Azure

Domínios de serviço de aplicações (pré-visualização) são domínios de nível superior que são geridos diretamente no Azure. Tornar mais fácil gerir os domínios personalizados para [Web Apps do Azure](app-service-web-overview.md). Este tutorial mostra como comprar um domínio do serviço de aplicações e atribuir nomes DNS para Web Apps do Azure.

Este artigo é para o App Service do Azure (Web Apps, API Apps, Mobile Apps, as Logic Apps). Para a VM do Azure ou o armazenamento do Azure, consulte [domínio atribuir do serviço de aplicações para a VM do Azure ou o armazenamento do Azure](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Para serviços em nuvem, consulte [configurar um nome de domínio personalizado para um serviço em nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Criar uma aplicação do app Service](/azure/app-service/), ou utilizar uma aplicação que criou para outro tutorial.

## <a name="prepare-the-app"></a>Preparar a aplicação

Para utilizar domínios personalizados do Web Apps do Azure, a aplicação web [plano do App Service](https://azure.microsoft.com/pricing/details/app-service/) tem de ser uma camada paga (**partilhados**, **básico**, **padrão**, ou **Premium**). Neste passo, certifique-se de que a aplicação web é suportado no escalão de preço.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegue para a aplicação no portal do Azure

No menu à esquerda, selecione **serviços aplicacionais**e, em seguida, selecione o nome da aplicação.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do serviço de aplicações.  

### <a name="check-the-pricing-tier"></a>Verifique o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para o **definições** secção e selecione **aumentar verticalmente (plano do App Service)**.

![Menu de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Camada da aplicação de actual é realçada por um limite azul. Certifique-se de que a aplicação não está no **livres** camada. DNS personalizado não é suportada a **livres** camada. 

![Verifique o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano de serviço de aplicações não está **livres**, feche o **escolher o escalão de preço** página e avançar para o [comprar o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano de serviço de aplicações

Selecione qualquer uma das camadas não livre (**partilhados**, **básico**, **padrão**, ou **Premium**). 

Clique em **Selecionar**.

![Verifique o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando for apresentada a seguinte notificação, a operação de dimensionamento está concluída.

![Confirmação de operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra o [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.

### <a name="launch-buy-domains"></a>Iniciar comprar domínios
No **Web Apps** separador, clique no nome da sua aplicação web, selecione **definições**e, em seguida, selecione **domínios personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

No **domínios personalizados** página, clique em **comprar domínios**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

### <a name="configure-the-domain-purchase"></a>Configurar a compra de domínio

No **domínio da aplicação de serviço** na página de **Procurar domínio** caixa, escreva o nome de domínio que pretende comprar e escreva `Enter`. Os domínios disponíveis sugeridos são mostrados imediatamente por baixo da caixa de texto. Selecione um ou mais domínios que pretende comprar. 
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

Clique em de **informações de contacto** e preencher o formulário de informações de contacto do domínio. Quando terminar, clique em **OK** para regressar à página de domínio da aplicação de serviço.
   
É importante que preencha todos os campos obrigatórios com a maior quantidade exatidão quanto possível. Dados incorretos informações de contacto podem resultar numa falha ao adquirir os domínios. 

Em seguida, selecione as opções pretendidas para o seu domínio. Consulte a tabela seguinte para explicações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|A renovação automática | **Ativar** | Renova o domínio da aplicação de serviço automaticamente todos os anos. O cartão de crédito é cobrado o mesmo preço de compra no momento da renovação. |
|Proteção de privacidade | Ativar | Optar por "A proteção de privacidade", que está incluída no preço de compra _gratuitamente_ (à exceção dos domínios de nível superior cujo registo não suportam a proteção de privacidade, tais como _. co.in_, _. co.uk_, e assim sucessivamente). |
| Atribuir nomes de anfitrião predefinido | **www** e**@** | Selecione os enlaces do nome de anfitrião desejado, se assim o desejar. Quando a operação de compra de domínio estiver concluída, a aplicação web pode ser acedida em nomes de anfitrião selecionado. Se a aplicação web está protegido por [Traffic Manager do Azure](https://azure.microsoft.com/services/traffic-manager/), não vir a opção de atribuir o domínio de raiz (@), devido Gestor de tráfego não suporte registos. Pode efetuar alterações às atribuições de nome de anfitrião depois de concluir a compra de domínio. |

### <a name="accept-terms-and-purchase"></a>Aceitar os termos e compra

Clique em **termos legais** para rever os termos de licenciamento e os encargos, em seguida, clique em **comprar**.

> [!NOTE]
> Domínios de serviço de aplicações utilizam DNS do Azure para alojar os domínios. Além a taxa de registo do domínio, são aplicáveis encargos de utilização de DNS do Azure. Para informações, consulte [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

Volta a **domínio da aplicação de serviço** página, clique em **OK**. Enquanto a operação está em curso, consulte as notificações que se seguem:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testar os nomes de anfitriões

Se tiver atribuído hostnames predefinido para a sua aplicação web, também ver uma notificação de sucesso para cada nome de anfitrião selecionado. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Consulte também os nomes de anfitriões selecionado no **domínios personalizados** na página de **Hostnames** secção. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Para testar os nomes de anfitriões, navegue para os nomes de anfitriões listadas no browser. O exemplo na captura de ecrã anterior, tente navegar para _kontoso.net_ e _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Atribuir nomes de anfitrião para a aplicação web

Se optar por não atribuir um ou mais nomes de anfitrião de predefinido para a sua aplicação web durante o processo de compra, ou se terá de atribuir um nome de anfitrião não listada, pode atribuir um nome de anfitrião em qualquer altura.

Também pode atribuir os nomes de anfitrião de domínio da aplicação de serviço a qualquer outra aplicação web. Os passos dependem se o domínio da aplicação de serviço e a aplicação web de pertencer à mesma subscrição.

- Uma subscrição diferente: mapear personalizados registos DNS do domínio de serviço de aplicações para a aplicação web como um domínio externamente adquirida. Para obter informações sobre como adicionar nomes DNS personalizados a um domínio do serviço de aplicações, consulte [gerir registos DNS personalizados](#custom). Para mapear um domínio externo adquirido para uma aplicação web, consulte o artigo [mapear um nome DNS personalizado existente para Web Apps do Azure](app-service-web-tutorial-custom-domain.md). 
- Subscrição do mesma: Utilize os seguintes passos.

### <a name="launch-add-hostname"></a>Inicie a adicionar o nome de anfitrião
No **serviços aplicacionais** página, selecione o nome da sua aplicação web que pretende atribuir nomes de anfitrião para selecionar **definições**e, em seguida, selecione **domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que o seu domínio adquirido está listado no **domínios do serviço de aplicações** secção, mas não selecioná-lo. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os domínios do serviço de aplicações na mesma subscrição são apresentados na aplicação web **domínios personalizados** página. Se o seu domínio for na subscrição da aplicação web, mas não é possível vê-lo na aplicação da web **domínios personalizados** página, tente voltar a abrir o **domínios personalizados** página ou Atualize a página Web. Além disso, verifique na campainha de notificação na parte superior do portal do Azure para as falhas de progresso ou criação.
>
>

Selecione **adicionar hostname**.

### <a name="configure-hostname"></a>Configurar o nome de anfitrião
No **adicionar hostname** caixa de diálogo, escreva o nome de domínio completamente qualificado do seu domínio da aplicação de serviço ou de qualquer subdomínio. Por exemplo:

- kontoso.NET
- www.kontoso.NET
- ABC.kontoso.NET

Quando terminar, selecione **validar**. O tipo de registo de nome de anfitrião é selecionado automaticamente para si.

Selecione **adicionar hostname**.

Quando a operação foi concluída, verá uma notificação de sucesso para o nome do anfitrião atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Feche Adicionar nome de anfitrião
No **adicionar hostname** página, atribua outro nome de anfitrião para a sua aplicação web, conforme o desejado. Quando terminar, feche o **adicionar hostname** página.

Deverá ver o hostname(s) atribuída recentemente na sua aplicação **domínios personalizados** página.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testar os nomes de anfitriões

Navegue para os nomes de anfitriões listadas no browser. O exemplo na captura de ecrã anterior, tente navegar para _abc.kontoso.net_.

<a name="custom" />

## <a name="manage-custom-dns-records"></a>Gerir registos DNS personalizados

No Azure, os registos DNS para um domínio da aplicação de serviço são geridos através de [DNS do Azure](https://azure.microsoft.com/services/dns/). Pode adicionar, remover e atualizar os registos DNS, tal como para um domínio externamente adquirido.

### <a name="open-app-service-domain"></a>Domínio de abrir o serviço de aplicações

No portal do Azure, no menu à esquerda, selecione **mais serviços** > **domínios do serviço de aplicações**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para gerir. 

### <a name="access-dns-zone"></a>Zona DNS de acesso

No menu à esquerda do domínio, selecione **zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Esta ação abre o [zona DNS](../dns/dns-zones-records.md) página do seu domínio do serviço de aplicações no DNS do Azure. Para obter informações sobre como editar registos DNS, consulte [como gerir zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar a compra (eliminar o domínio)

Depois de comprar o domínio da aplicação de serviço, terá de cinco dias para cancelar a compra para obter um reembolso total. Depois de cinco dias, pode eliminar o domínio da aplicação de serviço, mas não consegue receber um reembolso.

### <a name="open-app-service-domain"></a>Domínio de abrir o serviço de aplicações

No portal do Azure, no menu à esquerda, selecione **mais serviços** > **domínios do serviço de aplicações**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a pretende cancelar ou eliminar. 

### <a name="delete-hostname-bindings"></a>Eliminar enlaces do nome de anfitrião

No menu à esquerda do domínio, selecione **enlaces do nome de anfitrião**. Os enlaces do nome de anfitrião de todos os serviços do Azure estão listados aqui.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Não é possível eliminar o domínio da aplicação de serviço até todos os enlaces do nome de anfitrião são eliminados.

Eliminar cada enlace de nome de anfitrião selecionando **...**   >  **Eliminar**. Depois de serem eliminados todos os enlaces, selecione **guardar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou eliminar

No menu à esquerda do domínio, selecione **descrição geral**. 

Se não tiver decorrido o período de cancelamento no domínio adquirido, selecione **cancelar a compra**. Caso contrário, verá um **eliminar** botão em vez disso. Para eliminar o domínio sem um reembolso, selecione **eliminar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Selecione **OK** para confirmar a operação. Se não quiser continuar, clique em qualquer lugar fora da caixa de diálogo de confirmação.

Depois de concluída a operação, o domínio é libertado da sua subscrição e disponíveis para qualquer pessoa adquirir o novamente. 
