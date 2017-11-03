---
title: Criar um ambiente do App Service do Azure externo
description: "Explica como criar um ambiente de serviço de aplicações, ao criar uma aplicação ou autónomo"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 70c43b25aea364d7254137b46af31f851dcf8bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-external-app-service-environment"></a>Criar um ambiente de serviço de aplicações externas #

Ambiente de serviço de aplicações do Azure é uma implementação do App Service do Azure a uma sub-rede numa rede virtual do Azure (VNet). Existem duas formas de implementar um ambiente de serviço de aplicações (ASE):

- Com um VIP num endereço IP externo, frequentemente, designado ASE externo.
- Com o VIP num endereço IP, denominado frequentemente ILB ASE porque o ponto final interno é um balanceador de carga interno (ILB).

Este artigo mostra como criar ASE externo. Para uma descrição geral de ASE, consulte [uma introdução ao ambiente de serviço de aplicações][Intro]. Para obter informações sobre como criar ILB ASE, consulte [criar e utilizar ILB ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar o seu ASE ##

Depois de criar o ASE, não é possível alterar o seguinte:

- Localização
- Subscrição
- Grupo de recursos
- VNet utilizado
- Sub-rede utilizada
- Tamanho da sub-rede

> [!NOTE]
> Ao escolher uma VNet e especifique uma sub-rede, certifique-se de que é suficientemente grande para permitir um crescimento futuro. Recomendamos um tamanho de `/25` com endereços de 128.
>

## <a name="three-ways-to-create-an-ase"></a>Três formas de criar ASE ##

Existem três formas de criar ASE:

- **Ao criar um plano de serviço aplicacional**. Este método cria o ASE e o plano de serviço de aplicações num único passo.
- **Como uma ação de autónomo**. Este método cria uma autónoma ASE, que é ASE sem nada no mesmo. Este método é um processo mais avançado para criar ASE. Utilizá-la para criar ASE com um ILB.
- **A partir de um modelo Azure Resource Manager**. Este método é para utilizadores avançados. Para obter mais informações, consulte [criar ASE a partir de um modelo][MakeASEfromTemplate].

ASE externo tem um VIP público, o que significa que todo o tráfego HTTP/HTTPS para as aplicações a ASE chega a um endereço IP acessível através da internet. ASE com um ILB tem um endereço IP da sub-rede utilizada pelo ASE. As aplicações alojadas num ILB ASE não são expostas diretamente à internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar ASE e um plano de serviço de aplicações em conjunto ##

O plano de serviço de aplicações é um contentor de aplicações. Quando criar uma aplicação no App Service, escolher ou criar um plano de serviço de aplicações. Os ambientes de modelo do contentor reter planos de serviço de aplicações, sem soltar planos de serviços aplicacionais aplicações.

Para criar ASE enquanto cria um plano de serviço de aplicações:

1. No [portal do Azure](https://portal.azure.com/), selecione **novo** > **Web + móvel** > **aplicação Web**.

    ![Criação da aplicação Web][1]

2. Selecione a sua subscrição. A aplicação e o ASE são criados nas mesmas subscrições.

3. Selecione ou crie um grupo de recursos. Com grupos de recursos, pode gerir recursos relacionados do Azure como uma unidade. Grupos de recursos também são úteis quando estabelecer as regras de controlo de acesso baseado em funções para as suas aplicações. Para obter mais informações, consulte o [descrição geral do Azure Resource Manager][ARMOverview].

4. Selecione o plano de serviço de aplicações e, em seguida, selecione **criar novo**.

    ![Novo plano de serviço de aplicações][2]

5. No **localização** na lista pendente, selecione a região onde pretende criar o ASE. Se selecionar ASE existente, não é criado um novo ASE. O plano de serviço de aplicações é criado na ASE que selecionou. 

6. Selecione **escalão de preço**e escolha uma do **Isolated** SKUs de preço. Se escolher um **Isolated** cartão SKU e uma localização que não esteja ASE, um novo ASE é criado nessa localização. Para iniciar o processo de criação ASE, selecione **selecione**. O **Isolated** SKU está disponível apenas em conjunto com ASE. Também não pode utilizar qualquer outro SKU de preço num ASE diferente de **Isolated**.

    ![Seleção do escalão de preço][3]

7. Introduza o nome para a sua ASE. Este nome é utilizado no nome do endereçável para as suas aplicações. Se o nome do ASE _appsvcenvdemo_, o nome de domínio é *. appsvcenvdemo.p.azurewebsites.net*. Se criar uma aplicação com o nome *mytestapp*, é endereçável em mytestapp.appsvcenvdemo.p.azurewebsites.net. Não é possível utilizar o espaço em branco no nome. Se utilizar carateres maiúsculos, o nome de domínio é a versão em minúsculas total com esse nome.

    ![Novo nome do plano de serviço de aplicações][4]

8. Especifique os detalhes da rede virtuais do Azure. Selecione **criar novos** ou **selecionar existente**. A opção para selecionar uma VNet existente está disponível apenas se tiver uma VNet na região selecionada. Se selecionar **criar novo**, introduza um nome para a VNet. É criada uma nova VNet do Resource Manager com esse nome. Utiliza o espaço de endereços `192.168.250.0/23` na região selecionada. Se selecionar **selecione existente**, tem de:

    a. Selecione o bloco de endereços da VNet, se tiver mais do que um.

    b. Introduza um novo nome de sub-rede.

    c. Selecione o tamanho da sub-rede. *Lembre-se de selecionar um tamanho grande o suficiente para permitir um crescimento futuro da sua ASE.* Recomendamos `/25`, que tem 128 endereços e pode processar um ASE tamanho máximo. Não recomendamos `/28`, por exemplo, porque apenas 16 endereços estão disponíveis. A infraestrutura utiliza, pelo menos, cinco endereços. Num `/28` sub-rede, está a deixado com um dimensionamento máximo de 11 instâncias.

    d. Selecione o intervalo IP da sub-rede.

9. Selecione **criar** para criar o ASE. Este processo também cria o plano de serviço de aplicações e a aplicação. ASE, plano do App Service e aplicação estão todos na mesma subscrição e também no mesmo grupo de recursos. Se a sua ASE precisar de um grupo de recursos separado ou se precisar de ILB ASE, siga os passos para criar ASE por si só.

## <a name="create-an-ase-by-itself"></a>Criar ASE por si só ##

Se criar um ASE autónomo, tem nada no mesmo. ASE vazio ainda implica um custo mensal para a infraestrutura. Siga estes passos para criar ASE com um ILB ou criar ASE no seu próprio grupo de recursos. Depois de criar o ASE, pode criar aplicações no mesmo, utilizando o processo normal. Selecione o novo ASE como a localização.

1. Procure o Azure Marketplace para **ambiente de serviço de aplicações**, ou selecione **novo** > **Web Mobile** > **do serviço de aplicações Ambiente**. 

2. Introduza o nome do seu ASE. Este nome é utilizado para as aplicações criadas no ASE. Se o nome for *mynewdemoase*, é o nome de subdomínio *. mynewdemoase.p.azurewebsites.net*. Se criar uma aplicação com o nome *mytestapp*, é endereçável em mytestapp.mynewdemoase.p.azurewebsites.net. Não é possível utilizar o espaço em branco no nome. Se utilizar carateres maiúsculos, o nome de domínio é a versão total em minúsculas do nome. Se utilizar um ILB, o nome da sua ASE não é utilizado no seu subdomínio, mas em vez disso, é explicitamente indicado durante a criação de ASE.

    ![Nomenclatura ASE][5]

3. Selecione a sua subscrição. Esta subscrição também é aquele que todas as aplicações no ASE utilizam. Não é possível colocar o seu ASE numa VNet que está a ser outra subscrição.

4. Selecione ou especifique um novo grupo de recursos. O grupo de recursos utilizado para a sua ASE tem de ser o mesmo que é utilizado para a sua VNet. Se selecionar uma VNet existente, a seleção de grupo de recursos para o seu ASE é atualizada para refletir que a VNet. *Pode criar ASE com um grupo de recursos que é diferente do grupo de recursos de VNet, se utilizar um modelo do Resource Manager.* Para criar ASE a partir de um modelo, consulte [criar um ambiente de serviço de aplicações a partir de um modelo][MakeASEfromTemplate].

    ![Seleção de grupos de recursos][6]

5. Selecione a sua VNet e localização. Pode criar uma nova VNet ou selecionar uma VNet existente: 

    * Se selecionar uma nova VNet, pode especificar um nome e localização. A nova VNet tem o endereço intervalo 192.168.250.0/23 e uma sub-rede com o nome predefinido. A sub-rede está definida como 192.168.250.0/24. Só pode selecionar uma VNet do Resource Manager. O **VIP tipo** seleção determina se o ASE pode ser acedido diretamente da internet (externa) ou se utiliza um ILB. Para saber mais sobre estas opções, consulte [criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações][MakeILBASE]. 

      * Se selecionar **externo** para o **VIP tipo**, pode selecionar quantos endereços IP externos o sistema é criado com para fins SSL baseado em IP. 
    
      * Se selecionar **interno** para o **VIP tipo**, tem de especificar o domínio que utiliza o seu ASE. Pode implementar ASE para uma VNet que utiliza os intervalos de endereços público ou privado. Para utilizar uma VNet com um intervalo de endereços público, tem de criar a VNet antecedência. 
    
    * Se selecionar uma VNet existente, uma nova sub-rede é criada quando a ASE é criado. *Não é possível utilizar uma sub-rede previamente criada no portal. Pode criar ASE com uma sub-rede existente, se utilizar um modelo do Resource Manager.* Para criar ASE a partir de um modelo, consulte [criar um ambiente de serviço de aplicações a partir de um modelo][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicações v1 ##

Pode ainda criar instâncias da primeira versão do ambiente de serviço de aplicações (ASEv1). Para iniciar o processo, procurar no Marketplace para **v1 de ambiente de serviço de aplicações**. Criar o ASE da mesma forma que cria o autónomo ASE. Quando estiver concluída, o ASEv1 tem dois front-ends e técnicos de dois. Com ASEv1, tem de gerir o front-ends e profissionais. Eles estiver automaticamente adicionados ao criar os planos de serviço de aplicações. Os front-ends atuam como pontos finais de HTTP/HTTPS e enviar o tráfego para os trabalhadores. Os trabalhadores são as funções que alojam as suas aplicações. Pode ajustar a quantidade de front-ends e funcionários depois de criar o ASE. 

Para saber mais sobre ASEv1, consulte o artigo [introdução para o ambiente de serviço de aplicações v1][ASEv1Intro]. Para obter mais informações sobre como aumentar, gestão e monitorização ASEv1, consulte o artigo [como configurar um ambiente de serviço de aplicações][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



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
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
