---
title: Utilizar um ambiente do App Service do Azure
description: "Como criar, publicar e dimensionar as aplicações de um ambiente do App Service do Azure"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 64746f7b1a09e35b35e794f5a11d69bef39a03a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-an-app-service-environment"></a>Utilizar um ambiente de serviço de aplicações #

## <a name="overview"></a>Descrição geral ##

Ambiente de serviço de aplicações do Azure é uma implementação do App Service do Azure a uma sub-rede na rede virtual do Azure de um cliente. É composto por:

- **Extremidades do FrontPage**: os front-ends são onde HTTP/HTTPS termina num ambiente de serviço de aplicações (ASE).
- **Os funcionários**: os trabalhadores são os recursos que alojam as suas aplicações.
- **Base de dados**: A base de dados contém informações que define o ambiente.
- **Armazenamento**: O armazenamento é utilizado para alojar as aplicações publicadas de cliente.

> [!NOTE]
> Existem duas versões do ambiente de serviço de aplicações: ASEv1 e ASEv2. ASEv1, tem de gerir os recursos antes de poder utilizá-los. Para saber como configurar e gerir ASEv1, consulte [configurar um v1 de ambiente de serviço de aplicações][ConfigureASEv1]. O resto deste artigo está centrado nas ASEv2.
>
>

Pode implementar ASE (ASEv1 e ASEv2) com um VIP interno ou externo para acesso à aplicação. A implementação com um VIP externo é normalmente denominada ASE externo. A versão interna é denominada o ILB ASE porque utiliza um balanceador de carga interno (ILB). Para saber mais sobre o ASE do ILB, veja [criar e utilizar ILB ASE][MakeILBASE].

## <a name="create-a-web-app-in-an-ase"></a>Criar uma aplicação web está num ASE ##

Para criar uma aplicação web está num ASE, utilize o mesmo processo que quando cria normalmente, mas com algumas pequenas diferenças. Quando cria um novo plano de serviço de aplicações:

- Em vez de selecionar uma localização geográfica na qual pretende implementar a sua aplicação, escolha ASE como a localização.
- Todos os planos de serviço de aplicações criados num ASE tem de ser um Isolated escalão de preço.

Se não tiver ASE, pode criar um, seguindo as instruções em [criar um ambiente de serviço de aplicações][MakeExternalASE].

Para criar uma aplicação web está num ASE:

1. Selecione **novo** > **Web + móvel** > **aplicação Web**.

2. Introduza um nome para a aplicação web. Se já tiver selecionado um plano de serviço aplicacional está num ASE, o nome de domínio para a aplicação reflete o nome de domínio do ASE.

    ![Escolha do nome da aplicação Web][1]

3. Selecione uma subscrição.

4. Introduza um nome para um novo grupo de recursos ou selecione **utilizar existente** e selecione um na lista pendente.

5. Selecione um plano de serviço aplicacional existente na sua ASE ou crie um novo, seguindo estes passos:

    a. Selecione **criar novos**.

    b. Introduza o nome para o seu plano de serviço de aplicações.

    c. Selecione o seu ASE no **localização** na lista pendente.

    d. Selecione um **Isolated** escalão de preço. Selecione **selecione**.

    e. Selecione **OK**.
    
    ![Escalões de preços isolados][2]

6. Selecione **Criar**.

## <a name="how-scale-works"></a>Como dimensionar funciona ##

Todas as aplicações de serviço de aplicações é executado num plano do serviço de aplicações. O modelo do contentor é ambientes reter planos de serviço de aplicações, e planos de serviços aplicacionais manter as aplicações. Quando dimensionar uma aplicação, pode dimensionar o plano de serviço de aplicações e dimensionar, por conseguinte, todas as aplicações no mesmo plano.

No ASEv2, quando dimensiona um plano de serviço de aplicações, a infraestrutura necessária é adicionada automaticamente. Não há um atraso de tempo para as operações de dimensionamento enquanto a infraestrutura é adicionada. No ASEv1, a infraestrutura necessária tem de ser adicionada antes de poder criar ou ampliar o seu plano de serviço de aplicações. 

O multi-inquilino do serviço de aplicações, dimensionamento é normalmente imediato porque um conjunto de recursos é prontamente disponível para o suportar. Está num ASE, não existe nenhum desse memória intermédia, e após a necessidade dos recursos são atribuídos.

Está num ASE, pode dimensionar até 100 instâncias. Esses 100 instâncias podem ser todos num único plano de serviço de aplicações ou distribuídos por vários planos de serviço de aplicações.

## <a name="ip-addresses"></a>Endereços IP ##

Serviço de aplicações tem a capacidade de atribuir um endereço IP dedicado a uma aplicação. Esta capacidade está disponível depois de configurar o SSL baseada em IP, conforme descrito em [vincular um certificado SSL personalizado existente para aplicações web do Azure][ConfigureSSL]. No entanto, num ASE, não há uma exceção de relevantes. Não é possível adicionar endereços IP adicionais a utilizar para um SSL baseado em IP está num ILB ASE.

ASEv1, terá de atribuir os endereços IP como recursos antes de poder utilizá-los. ASEv2, utiliza-os da sua aplicação tal como o que fazer no multi-inquilino do serviço de aplicações. Não há sempre um endereço de reserva no ASEv2 até 30 endereços IP. Sempre que utilizar um, outro, é adicionado para que um endereço é sempre prontamente disponível para utilização. Um tempo de atraso é necessário atribuir outro endereço IP, que impede a adicionar IP endereços sucessivamente rápida.

## <a name="front-end-scaling"></a>Dimensionamento de front-end ##

No ASEv2, quando ampliar os planos do App Service, trabalhadores são adicionados automaticamente para os suportar. Cada ASE é criado com dois front-ends. Além disso, a frente termina automaticamente aumentar horizontalmente uma taxa de um front-end para todas as instâncias de 15 nos planos de serviço de aplicações. Por exemplo, se tiver 15 instâncias, em seguida, terá três extremidades front. Se dimensionar a 30 instâncias, em seguida, terá quatro extremidades do FrontPage e assim sucessivamente.

Este número de front-ends deve ser mais do que o suficiente para a maioria dos cenários. No entanto, pode aumentar horizontalmente rapidamente. Pode alterar a relação entre mais baixa um frente terminar para todas as instâncias de cinco. É cobrada uma taxa para alterar o rácio. Para obter mais informações, consulte [preços do App Service do Azure][Pricing].

Recursos de front-end são o ponto final HTTP/HTTPS para o ASE. Com a configuração predefinida do front-end, utilização de memória por front-end é consistentemente cerca de 60 por cento. Não executam cargas de trabalho do cliente num front-end. O fator chave para um front-end com relativamente a escala é a CPU, o que é suscitada pelo departamento principalmente pelo tráfego HTTPS.

## <a name="app-access"></a>Acesso à aplicação ##

Está num ASE externo, o domínio que é utilizado quando criar aplicações é diferente do multi-inquilino do serviço de aplicações. Inclui o nome do ASE. Para obter mais informações sobre como criar ASE externas, consulte [criar um ambiente de serviço de aplicações][MakeExternalASE]. O nome de domínio está num ASE externo aspeto *.&lt; asename&gt;. p.azurewebsites.net*. Por exemplo, se o seu ASE é denominado _externo ase_ e alojar uma aplicação chamada _contoso_ nessa ASE, chegá-lo nos seguintes URLs:

- contoso.external ase.p.azurewebsites.net
- contoso.SCM.external ase.p.azurewebsites.net

O URL contoso.scm.external-ase.p.azurewebsites.net é utilizado para aceder à consola do Kudu ou para publicar a aplicação através da utilização de web implementar. Para obter informações sobre a consola do Kudu, consulte [App Service do Azure na consola do Kudu][Kudu]. A consola do Kudu fornece uma IU da web para depuração, carregar ficheiros, editar os ficheiros e muito mais.

Está num ILB ASE, determine o domínio no momento da implementação. Para obter mais informações sobre como criar ILB ASE, consulte [criar e utilizar ILB ASE][MakeILBASE]. Se especificar o nome de domínio _ilb ase.info_, as aplicações que ASE utilizam esse domínio durante a criação da aplicação. Para a aplicação com o nome _contoso_, os URLs são:

- contoso.ilb ase.info
- contoso.SCM.ilb ase.info

## <a name="publishing"></a>Publicação ##

Tal como acontece com a multi-inquilino do serviço de aplicações, está num ASE pode publicar com:

- Implementação na Web.
- FTP.
- Integração contínua.
- Arraste e largue na consola do Kudu.
- Um IDE, tais como IDEIA Studio, Eclipse ou IntelliJ Visual.

Com ASE externo, todas estas opções de publicação comportam-se a mesma. Para obter mais informações, consulte [implementação no App Service do Azure][AppDeploy]. 

A principal diferença com a publicação é relativamente à ILB ASE. Com ILB ASE, os pontos finais de publicação estão disponíveis apenas através do ILB. O ILB é um IP privado na sub-rede ASE na rede virtual. Se não tiver acesso à rede para o ILB, não é possível publicar quaisquer aplicações nesse ASE. Conforme indicado no [criar e utilizar ILB ASE][MakeILBASE], terá de configurar o DNS para as aplicações no sistema. Que inclui o ponto final SCM. Se estes não estão definidas corretamente, não é possível publicar. Os IDEs também tem de ter acesso à rede para o ILB para publicar diretamente ao mesmo.

Sistemas CI baseado na Internet, tais como o GitHub e o Visual Studio Team Services, não funcionam com ILB ASE, porque o ponto final da publicação não está acessível a Internet. Em vez disso, tem de utilizar um sistema de CI que utiliza um modelo de extração, tais como o Dropbox.

Os pontos finais de publicação de aplicações está num ILB ASE utilizam o domínio que o ILB ASE foi criado com. Pode ver no perfil de publicação da aplicação e no painel do portal da aplicação (na **descrição geral** > **Essentials** e também em **propriedades**). 

## <a name="pricing"></a>Preços ##

Os preços SKU chamado **Isolated** foi criada para utilização apenas com ASEv2. Todos os planos de serviço de aplicações que estão alojados no ASEv2 são Isolated SKU de preço. Isolado taxas de plano de serviço de aplicações podem variar por região. 

Para além de preços para os planos do App Service, não há uma taxa de plana para ASE próprio. A taxa de plana não altera com o tamanho do seu ASE e pays para a infraestrutura de ASE numa predefinição de dimensionamento da taxa de 1 adicionais front-end para todos os 15 instâncias do plano de serviço de aplicações.  

Se a taxa de dimensionamento predefinido de 1 front-end para todos os 15 instâncias do plano de serviço de aplicações não é suficientemente rápida, pode ajustar o rácio entre as extremidades de frente são adicionadas ou o tamanho da frente-ends.  Ao ajustar o rácio ou tamanho, paga para os núcleos de front-end que não seriam adicionados por predefinição.  

Por exemplo, se ajustar o rácio de dimensionamento para 10, um front-end é adicionado para cada 10 instâncias os planos de serviço de aplicações. A taxa simples abrange uma taxa de escala de um front-end para todas as instâncias de 15. Com um rácio de escala de 10, paga uma taxa para o terceiro front-end que é adicionado para as 10 instâncias do plano de serviço de aplicações. Não precisa de pagar para o mesmo quando chegar 15 instâncias porque foi adicionada automaticamente.

Se ajustar o tamanho as extremidades de frente para 2 núcleos, mas não ajustar o rácio, em seguida, paga para os núcleos adicionais.  ASE é criado com 2 frente-ends de, por isso mesmo inferior ao limiar de dimensionamento automático que seria paga 2 núcleos adicionais se aumentar o tamanho para 2 núcleos frente-ends.

Para obter mais informações, consulte [preços do App Service do Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminar ASE ##

Para eliminar ASE: 

1. Utilize **eliminar** na parte superior do **ambiente de serviço de aplicações** painel. 

2. Introduza o nome do seu ASE para confirmar que pretende eliminá-lo. Quando elimina ASE, elimine todo o conteúdo dentro da mesma bem. 

    ![Eliminação de ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
