---
title: "Configurar um nome de domínio personalizado nos serviços em nuvem | Microsoft Docs"
description: "Saiba como para expor a aplicação do Azure ou a dados para a internet num domínio personalizado ao configurar as definições de DNS.  Estes exemplos utilizam o portal do Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 5783a246-a151-4fb1-b488-441bfb29ee44
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 139ec6578dc9e76039c5fb13e7a7741aa8ba4e0d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurar um nome de domínio personalizado para um serviço em nuvem do Azure
Quando cria um serviço em nuvem, Azure atribui-o para um subdomínio **cloudapp.net**. Por exemplo, se o seu serviço em nuvem com o nome "contoso", os utilizadores irão conseguir aceder à aplicação num URL como http://contoso.cloudapp.net. Azure também atribui um endereço IP virtual.

No entanto, pode também expor a aplicação no seu próprio nome de domínio, tal como **contoso.com**. Este artigo explica como reserva ou configurar um nome de domínio personalizado para funções da web de serviço em nuvem.

Já compreender quais são CNAME e registos? [Ir passado a explicação](#add-a-cname-record-for-your-custom-domain).

> [!NOTE]
> Os procedimentos nesta tarefa se aplicam ao Cloud Services do Azure. Para os serviços de aplicação, consulte [mapear um nome DNS personalizado existente para Web Apps do Azure](../app-service/app-service-web-tutorial-custom-domain.md). Para contas do storage, consulte [configurar um nome de domínio personalizado para o ponto final de armazenamento de Blobs do Azure](../storage/blobs/storage-custom-domain-name.md).
> 
> 

<p/>

> [!TIP]
> Começar mais rapidamente – utilize novo Azure [orientado instruções](http://support.microsoft.com/kb/2990804)!  Faz a associação de um nome de domínio personalizado e proteger comunicação (SSL) com Cloud Services do Azure ou Web sites do Azure um snap.
> 
> 

## <a name="understand-cname-and-a-records"></a>Compreender os registos CNAME e A
CNAME (ou os registos de alias) e um registos permitem-lhe associar um nome de domínio com um servidor específico (ou serviço neste caso,) no entanto, funcionam de forma diferente. Também existem algumas considerações específicas ao utilizar registos com os serviços de nuvem do Azure que deve considerar antes de decidir quais pode ser utilizado.

### <a name="cname-or-alias-record"></a>Registo CNAME ou Alias
Um registo CNAME mapeia um *específico* domínio, tal como **contoso.com** ou **www.contoso.com**, para um nome de domínio canónico. Neste caso, é o nome de domínio canónico o **[myapp] .cloudapp .net** nome de domínio do seu Azure alojada a aplicação. Depois de criado, o CNAME cria um alias para o **[myapp] .cloudapp .net**. A entrada CNAME irá resolver para o endereço IP do seu **[myapp] .cloudapp .net** service automaticamente, pelo que o se alterar o endereço IP do serviço em nuvem, não precisa de efetuar qualquer ação.

> [!NOTE]
> Alguns registrars de domínio só permitem-lhe mapear os subdomínios quando utilizar um registo CNAME, como www.contoso.com e não os nomes de raiz, tal como contoso.com. Para obter mais informações sobre registos CNAME, consulte a documentação fornecida pela sua entidade de registo, [a entrada de Wikipedia no registo CNAME](http://en.wikipedia.org/wiki/CNAME_record), ou o [IETF os nomes de domínio - implementação e a especificação de](http://tools.ietf.org/html/rfc1035) documento.
> 
> 

### <a name="a-record"></a>Um registo
Um *A* registo mapeia um domínio, tal como **contoso.com** ou **www.contoso.com**, *ou de um domínio de caráter universal* como  **\*. contoso.com**, para um endereço IP. No caso de um Azure serviço em nuvem, o IP virtual do serviço. Para o benefício do principal de um registo através de um registo CNAME é que pode fazer com que uma entrada que utiliza um caráter universal, tais como \* **. contoso.com**, que seria processar pedidos de subdomínios vários como **mail.contoso.com**, **login.contoso.com**, ou **www.contso.com**.

> [!NOTE]
> Uma vez que um registo for mapeado para um endereço IP estático, este não é possível resolver automaticamente as alterações para o endereço IP do seu serviço em nuvem. O endereço IP utilizado pelo seu serviço em nuvem está alocado à primeira que implementação para uma ranhura vazia (produção ou transição.) Se eliminar a implementação para a ranhura, o endereço IP é libertado pelo Azure e todas as implementações futuras para a ranhura podem ser atribuídas um novo endereço IP.
> 
> Comodamente, o endereço IP de uma ranhura de implementação fornecido (produção ou transição) é mantido ao trocar entre testes e implementações de produção ou efetuar uma atualização direta de uma implementação existente. Para obter mais informações sobre estas ações a efetuar, consulte [como gerir cloud services](cloud-services-how-to-manage-portal.md).
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>Adicionar um registo CNAME para o domínio personalizado
Para criar um registo CNAME, tem de adicionar uma nova entrada na tabela de DNS para o seu domínio personalizado ao utilizar as ferramentas fornecidas pelo sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificação de um registo CNAME, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para localizar o **. cloudapp.net** atribuído ao seu serviço de nuvem de nome de domínio.
   
   * Início de sessão para o [portal do Azure], selecione o seu serviço de nuvem, observe o **Essentials** secção e, em seguida, localize o **URL do Site** entrada.
     
       ![secção de leitura rápida, que mostra o URL do site][csurl]
     
       **OU**
   * Instalar e configurar [Azure Powershell](/powershell/azure/overview)e, em seguida, utilize o seguinte comando:
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     Guarde o nome de domínio utilizado no URL devolvido por qualquer um dos métodos, como precisará ao criar um registo CNAME.
2. Inicie sessão no Web site da sua DNS da entidade de registo e aceda à página para gerir o DNS. Procure ligações ou áreas do site identificadas como **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.
3. Localize agora onde pode selecionar ou introduzir do CNAME. Poderá ter de selecionar o tipo de registo de um menu pendente ou aceda a uma página de definições avançadas. Deve procurar as palavras **CNAME**, **Alias**, ou **subdomínios**.
4. Tem também de fornecer o domínio ou subdomínio alias para CNAME, tais como **www** se pretender criar um alias para **www.customdomain.com**. Se pretender criar um alias para o domínio de raiz, podem ser apresentado como o '**@**' símbolo nas ferramentas DNS da sua entidade de registo.
5. Em seguida, tem de fornecer um nome de anfitrião canónico, que é a sua aplicação **cloudapp.net** domínio neste caso.

Por exemplo, o registo CNAME seguinte reencaminha todo o tráfego de **www.contoso.com** para **contoso.cloudapp.net**, o nome de domínio personalizado da sua aplicação implementada:

| Nome de alias/anfitrião/subdomínio | Domínio canónico |
| --- | --- |
| www |contoso.cloudapp.NET |

> [!NOTE]
> Visitantes de **www.contoso.com** nunca irá ver o anfitrião for VERDADEIRO (contoso.cloudapp.net), pelo que o processo de reencaminhamento é invisível ao utilizador final.
> 
> O exemplo acima aplica-se apenas ao tráfego no **www** subdomínio. Uma vez que não é possível utilizar carateres universais com registos CNAME, tem de criar um CNAME para cada domínio/subdomínio. Se pretender direcionar o tráfego de subdomínios, tal como *. contoso.com, para o seu endereço de cloudapp.net, pode configurar um **URL de redirecionamento** ou **URL reencaminhar** entrada nas suas definições de DNS, ou crie um registo.
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>Adicionar um registo a para o domínio personalizado
Para criar um registo a, primeiro tem de localizar o endereço IP virtual do seu serviço de nuvem. Em seguida, adicione uma nova entrada na tabela de DNS para o seu domínio personalizado ao utilizar as ferramentas fornecidas pelo sua entidade de registo. Cada entidade de registo tem um método semelhante, mas ligeiramente diferente de especificação de um registo, mas os conceitos são os mesmos.

1. Utilize um dos seguintes métodos para obter o endereço IP do seu serviço em nuvem.
   
   * Início de sessão para o [portal do Azure], selecione o seu serviço de nuvem, observe o **Essentials** secção e, em seguida, localize o **endereços IP públicos** entrada.
     
       ![secção de leitura rápida, que mostra o VIP][vip]
     
       **OU**
   * Instalar e configurar [Azure Powershell](/powershell/azure/overview)e, em seguida, utilize o seguinte comando:
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     Guarde o endereço IP, pois irá precisar ao criar um registo.
2. Inicie sessão no Web site da sua DNS da entidade de registo e aceda à página para gerir o DNS. Procure ligações ou áreas do site identificadas como **nome de domínio**, **DNS**, ou **nome do servidor de gestão**.
3. Localize agora onde pode selecionar ou introduzir um registo. Poderá ter de selecionar o tipo de registo de um menu pendente ou aceda a uma página de definições avançadas.
4. Selecione ou introduza o domínio ou subdomínio que irá utilizar este registo. Por exemplo, seleccione **www** se pretender criar um alias para **www.customdomain.com**. Se pretender criar uma entrada de caráter universal para todos os subdomínios, introduza ' ***'. Este irá cobrir todos os subdomínios como **mail.customdomain.com**, **login.customdomain.com**, e **www.customdomain.com**.
   
    Se pretender criar um registo a para o domínio de raiz, podem ser apresentado como o '**@**' símbolo nas ferramentas DNS da sua entidade de registo.
5. Introduza o endereço IP do seu serviço em nuvem no campo fornecido. Isto associa a entrada de domínio utilizada o registo com o endereço IP da sua implementação do serviço de nuvem.

Por exemplo, o seguinte um registo reencaminha todo o tráfego de **contoso.com** para **137.135.70.239**, o endereço IP da sua aplicação implementada:

| Nome do anfitrião/subdomínio | Endereço IP |
| --- | --- |
| @ |137.135.70.239 |

Este exemplo mostra como criar um registo a para o domínio de raiz. Se pretender criar uma entrada de caráter universal para cobrir todos os subdomínios, introduziria ' ***' como o subdomínio.

> [!WARNING]
> Endereços IP no Azure são dinâmicos por predefinição. É provável que pretenda utilizar uma [reservado de endereços IP](../virtual-network/virtual-networks-reserved-public-ip.md) para garantir que o seu endereço IP não alterar.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](../cdn/cdn-map-content-to-custom-domain.md)
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implementar um serviço em nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[portal do Azure]: https://portal.azure.com
[vip]: ./media/cloud-services-custom-domain-name-portal/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name-portal/csurl.png
