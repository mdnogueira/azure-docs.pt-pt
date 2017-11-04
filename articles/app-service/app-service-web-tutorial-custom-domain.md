---
title: Mapear um nome DNS personalizado existente para Web Apps do Azure | Microsoft Docs
description: "Saiba como adicionar um personalizado DNS nome de domínio existente (domínio intuitivos) para uma aplicação web, o back-end da aplicação móvel ou a aplicação API no App Service do Azure."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6d7c99b1b02a0450cae406e2bc70a7e5563e2ac2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapear um nome DNS personalizado existente para Web Apps do Azure

[As Aplicações Web do Azure](app-service-web-overview.md) fornecem um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como mapear um nome DNS personalizado existente para Web Apps do Azure.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio (por exemplo, `www.contoso.com`) através da utilização de um registo CNAME
> * Mapear um domínio de raiz (por exemplo, `contoso.com`) através da utilização de um registo
> * Mapear um domínio de caráter universal (por exemplo, `*.contoso.com`) através da utilização de um registo CNAME
> * Mapeamento de domínio com scripts de automatizar

Pode utilizar tanto um **registo CNAME** ou um **um registo** para mapear um nome DNS personalizado no App Service. 

> [!NOTE]
> Recomendamos que utilize um CNAME para todos os nomes DNS personalizados, exceto um domínio de raiz (por exemplo, `contoso.com`).

Para migrar um site em direto e o respetivo nome de domínio DNS para o App Service, consulte [migrar um nome DNS do Active Directory para o App Service do Azure](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Criar uma aplicação do app Service](/azure/app-service/), ou utilizar uma aplicação que criou para outro tutorial.
* Adquira um nome de domínio e certifique-se de que tem acesso ao registo DNS para o seu fornecedor de domínio (por exemplo, a GoDaddy).

  Por exemplo, para adicionar entradas DNS para `contoso.com` e `www.contoso.com`, tem de ser capaz de configurar as definições de DNS para o `contoso.com` domínio de raiz.

  > [!NOTE]
  > Se não tiver um domínio existente, nome, considere [comprar um domínio através do portal do Azure](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação web, a aplicação web [plano do App Service](https://azure.microsoft.com/pricing/details/app-service/) tem de ser uma camada paga (**partilhados**, **básico**, **padrão**, ou **Premium**). Neste passo, certifique-se de que a aplicação de serviço de aplicações é suportado no escalão de preço.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegue para a aplicação no portal do Azure

No menu à esquerda, selecione **serviços aplicacionais**e, em seguida, selecione o nome da aplicação.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do serviço de aplicações.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Verifique o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para o **definições** secção e selecione **aumentar verticalmente (plano do App Service)**.

![Menu de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Camada da aplicação de actual é realçada por um limite azul. Certifique-se de que a aplicação não está no **livres** camada. DNS personalizado não é suportada a **livres** camada. 

![Verifique o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano de serviço de aplicações não está **livres**, feche o **escolher o escalão de preço** página e avançar para o [mapear um registo CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano de serviço de aplicações

Selecione qualquer uma das camadas não livre (**partilhados**, **básico**, **padrão**, ou **Premium**). 

Clique em **Selecionar**.

![Verifique o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando for apresentada a seguinte notificação, a operação de dimensionamento está concluída.

![Confirmação de operação de escala](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mapear um registo CNAME

O exemplo tutorial, adicione um registo CNAME para o `www` subdomínio (por exemplo, `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registo CNAME para mapear um subdomínio para o nome de anfitrião da aplicação predefinida (`<app_name>.azurewebsites.net`, onde `<app_name>` é o nome da sua aplicação).

Para o `www.contoso.com` exemplo de domínio, adicione um registo CNAME, que mapeia o nome `www` para `<app_name>.azurewebsites.net`.

Depois de adicionar o CNAME, a página de registos DNS aspeto semelhante ao seguinte exemplo:

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Ativar o mapeamento de registo CNAME no Azure

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

No **domínios personalizados** página da aplicação e adicione o nome DNS personalizado completamente qualificado (`www.contoso.com`) à lista.

Selecione o  **+**  ícone junto a **adicionar hostname**.

![Adicione o nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva o nome de domínio completamente qualificado que adicionou um registo CNAME, tais como `www.contoso.com`. 

Selecione **validar**.

O **adicionar hostname** botão está ativado. 

Certifique-se de que **tipo de registo de nome de anfitrião** está definido como **CNAME (www.example.com ou qualquer subdomínio)**.

Selecione **adicionar hostname**.

![Adicione o nome DNS para a aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Poderá demorar algum tempo para o novo nome de anfitrião para serem refletidas na aplicação do **domínios personalizados** página. Tente atualizar o browser para atualizar os dados.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Se em falta um passo ou enganou algures anteriormente, verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mapear um registo

O exemplo tutorial, adicione um registo a para o domínio de raiz (por exemplo, `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Copie o endereço IP da aplicação

Para mapear um registo, terá de endereço IP externo da aplicação. Pode encontrar este endereço IP da aplicação **domínios personalizados** página no portal do Azure.

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

No **domínios personalizados** página, copie o endereço IP da aplicação.

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Criar o registo

Para mapear um registo para uma aplicação, serviço de aplicações requer **dois** registos DNS:

- Um **A** registo para mapear para o endereço IP da aplicação.
- A **TXT** registo para mapear para o nome de anfitrião da aplicação predefinido `<app_name>.azurewebsites.net`. Serviço de aplicações utiliza este registo apenas no momento da configuração, para verificar que possui o domínio personalizado. Depois do domínio personalizado é validado e configurado no App Service, pode eliminar este registo TXT. 

Para o `contoso.com` exemplo de domínio, criar os registos A e TXT, de acordo com a tabela seguinte (`@` normalmente representa o domínio de raiz). 

| Tipo de registo | Anfitrião | Valor |
| - | - | - |
| A | `@` | Endereço IP de [copie o endereço IP da aplicação](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

Quando os registos são adicionados, a página de registos DNS aspeto semelhante ao seguinte exemplo:

![Página de registos DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Ativar o mapeamento de um registo na aplicação

A aplicação de regresso **domínios personalizados** página no portal do Azure, adicione o nome DNS personalizado completamente qualificado (por exemplo, `contoso.com`) à lista.

Selecione o  **+**  ícone junto a **adicionar hostname**.

![Adicione o nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Escreva o nome de domínio completamente qualificado que configurou o registo, tais como `contoso.com`.

Selecione **validar**.

O **adicionar hostname** botão está ativado. 

Certifique-se de que **tipo de registo de nome de anfitrião** está definido como **um registo (example.com)**.

Selecione **adicionar hostname**.

![Adicione o nome DNS para a aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Poderá demorar algum tempo para o novo nome de anfitrião para serem refletidas na aplicação do **domínios personalizados** página. Tente atualizar o browser para atualizar os dados.

![Um registo adicionado](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Se em falta um passo ou enganou algures anteriormente, verá um erro de verificação na parte inferior da página.

![Erro de verificação](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mapear um domínio de caráter universal

No exemplo tutorial, mapear uma [nome DNS com carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (por exemplo, `*.contoso.com`) para a aplicação de serviço de aplicações através da adição de um registo CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registo CNAME para mapear um nome de caráter universal para o nome de anfitrião da aplicação predefinida (`<app_name>.azurewebsites.net`).

Para o `*.contoso.com` exemplo de domínio, o registo CNAME irá mapear o nome `*` para `<app_name>.azurewebsites.net`.

Quando é adicionado o CNAME, a página de registos DNS aspeto semelhante ao seguinte exemplo:

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Ativar o mapeamento de registo CNAME na aplicação

Agora pode adicionar qualquer subdomínio que corresponda ao nome de caráter universal para a aplicação (por exemplo, `sub1.contoso.com` e `sub2.contoso.com` corresponder `*.contoso.com`). 

No painel de navegação esquerdo da página da aplicação no portal do Azure, selecione **domínios personalizados**. 

![Menu de domínio personalizado](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Selecione o  **+**  ícone junto a **adicionar hostname**.

![Adicione o nome de anfitrião](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Escreva um nome de domínio completamente qualificado que corresponde ao domínio de caráter universal (por exemplo, `sub1.contoso.com`) e, em seguida, selecione **validar**.

O **adicionar hostname** botão está ativado. 

Certifique-se de que **tipo de registo de nome de anfitrião** está definido como **registo CNAME (www.example.com ou qualquer subdomínio)**.

Selecione **adicionar hostname**.

![Adicione o nome DNS para a aplicação](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Poderá demorar algum tempo para o novo nome de anfitrião para serem refletidas na aplicação do **domínios personalizados** página. Tente atualizar o browser para atualizar os dados.

Selecione o  **+**  ícone novo para adicionar outro nome de anfitrião, que corresponde ao domínio de caráter universal. Por exemplo, adicionar `sub2.contoso.com`.

![Registo CNAME adicionado](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Teste no browser

Navegue para os nomes DNS que configurou anteriormente (por exemplo, `contoso.com`, `www.contoso.com`, `sub1.contoso.com`, e `sub2.contoso.com`).

![Navegação do portal para aplicações do Azure](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a gestão de domínios personalizados com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>CLI do Azure 

O comando seguinte adiciona um nome DNS personalizado configurado para uma aplicação de serviço de aplicações. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Para obter mais informações, consulte [mapear um domínio personalizado para uma aplicação web](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

O comando seguinte adiciona um nome DNS personalizado configurado para uma aplicação de serviço de aplicações. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Para obter mais informações, consulte [atribuir um domínio personalizado para uma aplicação web](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Mapear um subdomínio através da utilização de um registo CNAME
> * Mapear um domínio de raiz utilizando um registo
> * Mapear um domínio de caráter universal, utilizando um registo CNAME
> * Mapeamento de domínio com scripts de automatizar

Avançar para o próximo tutorial para saber como vincular um certificado SSL personalizado para uma aplicação web.

> [!div class="nextstepaction"]
> [Vincular um certificado SSL personalizado existente para Web Apps do Azure](app-service-web-tutorial-custom-ssl.md)
