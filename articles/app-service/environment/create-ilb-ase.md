---
title: Criar e utilizar um balanceador de carga interno com um ambiente do App Service do Azure
description: Detalhes sobre como criar e utilizar um ambiente de App Service do Azure isolada de internet
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: cc7bdd7860506c20187dc913b72111824d1737ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>Criar e utilizar um balanceador de carga interno com um ambiente de serviço de aplicações #

 Ambiente de serviço de aplicações do Azure é uma implementação do App Service do Azure a uma sub-rede numa rede virtual do Azure (VNet). Existem duas formas de implementar um ambiente de serviço de aplicações (ASE): 

- Com um VIP num endereço IP externo, frequentemente, designado ASE externo.
- Com um VIP num endereço IP, denominado frequentemente ILB ASE porque o ponto final interno é um balanceador de carga interno (ILB). 

Este artigo mostra como criar ILB ASE. Para obter uma descrição geral sobre o ASE, consulte [introdução para ambientes do App Service][Intro]. Para saber como criar ASE externas, consulte [criar ASE externo][MakeExternalASE].

## <a name="overview"></a>Descrição geral ##

Pode implementar ASE com um ponto final acessível através da internet ou com um endereço IP na sua VNet. Para definir o endereço IP para um endereço VNet, o ASE tem de ser implementada com um ILB. Quando implementa o ASE com um ILB, tem de fornecer:

-   O seus próprios domínio que utilizar quando criar as suas aplicações.
-   O certificado utilizado para HTTPS.
-   Gestão de DNS para o seu domínio.

No return, pode fazer coisas como:

-   Aplicações de intranet de anfitrião em segurança na nuvem, que pode aceder através de um site para site ou a VPN do Azure ExpressRoute.
-   Aplicações de anfitrião na nuvem que não estão listadas em servidores DNS públicos.
-   Crie aplicações do back-end isolada de internet que as aplicações de front-end em segurança podem ser integrado.

### <a name="disabled-functionality"></a>Funcionalidade desativada ###

Existem algumas coisas que não é possível fazer quando utilizar ILB ASE:

-   Utilize SSL baseado em IP.
-   Atribua endereços IP para aplicações específicas.
-   Comprar e utilizar um certificado com uma aplicação através do portal do Azure. Pode obter certificados diretamente a partir de uma autoridade de certificação e utilizá-los com as suas aplicações. Não é possível obtê-las através do portal do Azure.

## <a name="create-an-ilb-ase"></a>Criar ASE do ILB ##

Para criar ILB ASE:

1. No portal do Azure, selecione **novo** > **Web + móvel** > **ambiente de serviço de aplicações**.

2. Selecione a sua subscrição.

3. Selecione ou crie um grupo de recursos.

4. Selecione ou crie uma VNet.

5. Se selecionar uma VNet existente, terá de criar uma sub-rede para conter o ASE. Certifique-se de que define um tamanho de sub-rede suficientemente grande para contemplar qualquer crescimento futuro da sua ASE. Recomendamos um tamanho de `/25`, que tem 128 endereços e pode processar um ASE tamanho máximo. O tamanho mínimo que pode selecionar é um `/28`. Depois de tem de infraestrutura, este tamanho pode ser escalado para um máximo de 11 instâncias.

    * Ponha o máximo predefinido de instâncias de 100 os planos de serviço de aplicações.

    * Dimensionar quase 100 mas com mais rápido dimensionamento front-end.

6. Selecione **/localização de rede Virtual** > **configuração de rede Virtual**. Definir o **VIP tipo** para **interno**.

7. Introduza um nome de domínio. Este domínio é utilizada para aplicações criadas neste ASE. Existem algumas restrições. Não pode ser:

    * NET   

    * azurewebsites.NET

    * p.azurewebsites.NET

    * &lt;asename&gt;. p.azurewebsites.net

   O nome de domínio personalizado utilizado para aplicações e o nome de domínio utilizado pelo seu ASE não se podem sobrepor. Para ASE ILB com o nome de domínio _contoso.com_, não é possível utilizar nomes de domínio personalizados para as suas aplicações, como:

    * www.contoso.com

    * abcd.def.contoso.com

    * abcd.contoso.com

   Se souber os nomes de domínio personalizado para as suas aplicações, escolha um domínio para o ILB ASE que não tem um conflito com esses nomes de domínio personalizado. Neste exemplo, pode utilizar algo semelhante ao seguinte *contoso internal.com* para o domínio da sua ASE porque que não estão em conflito com nomes de domínio personalizado que terminem no *. contoso.com*.

8. Selecione **OK**e, em seguida, selecione **criar**.

    ![Criação do ASE][1]

No **rede Virtual** painel, há um **configuração de rede Virtual** opção. Pode utilizá-lo para selecionar um VIP externo ou um VIP interno. A predefinição é **externo**. Se selecionar **externo**, sua ASE utiliza um VIP acessível através da internet. Se selecionar **interno**, sua ASE está configurado com um ILB um endereço IP na sua VNet.

Depois de selecionar **interno**, a capacidade de adicionar mais endereços IP ao seu ASE é removida. Em vez disso, tem de fornecer o domínio do ASE. Está num ASE com um VIP externo, o nome do ASE é utilizado no domínio para aplicações criadas nesse ASE.

Se definir **VIP tipo** para **interno**, o nome da sua ASE não é utilizado no domínio para o ASE. Especifique o domínio explicitamente. Se o seu domínio for *contoso.corp.net* e criar uma aplicação em que com o nome ASE *timereporting*, o URL para essa aplicação é timereporting.contoso.corp.net.


## <a name="create-an-app-in-an-ilb-ase"></a>Criar uma aplicação está num ASE do ILB ##

Criar uma aplicação está num ILB ASE da mesma forma que criar uma aplicação está num ASE normalmente.

1. No portal do Azure, selecione **novo** > **Web + móvel** > **Web** ou **Mobile** ou **aplicação API**.

2. Introduza o nome da aplicação.

3. Selecione uma subscrição.

4. Selecione ou crie um grupo de recursos.

5. Selecione ou crie um plano de serviço de aplicações. Se pretender criar um novo plano de serviço de aplicações, selecione o seu ASE como a localização. Selecione o conjunto de trabalho onde pretende que o seu plano de serviço de aplicações a ser criado. Quando cria o plano de serviço de aplicações, selecione o seu ASE como a localização e o conjunto de trabalho. Quando especificar o nome da aplicação, o domínio em nome da aplicação foi substituído pelo domínio para o seu ASE.

6. Selecione **Criar**. Se pretender que a aplicação aparece no dashboard, selecione o **afixar ao dashboard** caixa de verificação.

    ![Criação do plano de serviço de aplicações][2]

    Em **nome da aplicação**, o nome de domínio é atualizado para refletir o domínio da sua ASE.

## <a name="post-ilb-ase-creation-validation"></a>Validação de criação do POST ILB ASE ##

É ligeiramente diferente do que o não - ILB ASE ILB ASE. Que já foram anotados, tem de gerir os seus próprios DNS. Também tem de fornecer o seu próprio certificado para ligações HTTPS.

Depois de criar o ASE, o nome de domínio mostra o domínio especificado. Um novo item é apresentado no **definição** menu chamado **ILB certificado**. O ASE é criado com um certificado que não especifique o domínio de ILB ASE. Se utilizar o ASE com esse certificado, o seu browser indica que é inválido. Este certificado torna mais fácil testar o HTTPS, mas terá de carregar o seu próprio certificado que está associado ao seu domínio ILB ASE. Este passo é necessário independentemente se o certificado é autoassinado ou adquirir a partir de uma autoridade de certificação.

![Nome de domínio do ILB ASE][3]

O ILB ASE necessita de um certificado SSL válido. Utilizar autoridades de certificação interna, comprar um certificado de um emissor externo ou utilizar um certificado autoassinado. Independentemente da origem do certificado SSL, os seguintes atributos de certificado tem de ser configurados corretamente:

* **Requerente**: este atributo deve ser definido como *.your-raiz-domínio-aqui.
* **Nome alternativo do requerente**: este atributo tem de incluir os **.your-raiz-domínio-aqui* e **.scm.your-raiz-domínio-aqui*. Ligações de SSL para o site SCM/Kudu associados a cada aplicação utilizam um endereço do formulário *your-app-name.scm.your-root-domain-here*.

Converter/guardar o certificado SSL como um ficheiro. pfx. O ficheiro. pfx tem de incluir todos os intermédio e certificados de raiz. Proteja-a com uma palavra-passe.

Se pretender criar um certificado autoassinado, pode utilizar os comandos do PowerShell aqui. Certifique-se de utilizar o seu nome de domínio do ILB ASE em vez de *internal.contoso.com*: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

O certificado que geram estes comandos do PowerShell está assinalado por browsers porque o certificado não foi criado por uma autoridade de certificado na cadeia de fidedignidade do seu browser. Para obter um certificado que confie na seu browser, obtê-lo a partir de uma autoridade de certificação comercial na cadeia de fidedignidade do seu browser. 

![Defina o certificado do ILB][4]

Para carregar os seus próprios certificados e testar o acesso:

1. Depois de criado o ASE, vá para a IU do ASE. Selecione **ASE** > **definições** > **ILB certificado**.

2. Para definir o certificado do ILB, selecione o ficheiro. pfx do certificado e introduza a palavra-passe. Este passo demora algum tempo a processar. É apresentada uma mensagem a indicar que uma operação de carregamento está em curso.

3. Obter o endereço do ILB para sua ASE. Selecione **ASE** > **propriedades** > **endereço Virtual IP**.

4. Crie uma aplicação web no seu ASE depois do ASE é criado.

5. Crie uma VM, se não tiver um nessa VNet.

    > [!NOTE] 
    > Não tente criar esta VM na mesma sub-rede que o ASE porque irá falhar ou causar problemas.
    >
    >

6. Defina o DNS para o seu domínio ASE. Pode utilizar um caráter universal com o domínio no seu DNS. Para fazer alguns testes simples, edite o ficheiro de anfitriões na sua VM para definir o nome da aplicação web para o endereço IP de VIP:

    a. Se o seu ASE tem o nome de domínio _. ilbase.com_ e criar a aplicação web com o nome _mytestapp_, é resolvido em _mytestapp.ilbase.com_. Em seguida, defina _mytestapp.ilbase.com_ resolver para o endereço do ILB. (No Windows, o ficheiro de anfitriões está no _C:\Windows\System32\drivers\etc\_.)

    b. Para testar a publicação de implementação web ou de acesso à consola do avançada, crie um registo para _mytestapp.scm.ilbase.com_.

7. Utilizar um browser nessa VM e aceda a http://mytestapp.ilbase.com. (Ou vá para o que é o nome da aplicação web com o seu domínio.)

8. Utilizar um browser nessa VM e aceda a https://mytestapp.ilbase.com. Se utilizar um certificado autoassinado, aceite a falta de segurança.

    O endereço IP para o ILB está listado em **endereços IP**. Esta lista também tem os endereços IP utilizados pelo VIP externo e para o tráfego de entrada de gestão.

    ![Endereço IP do ILB][5]

## <a name="web-jobs-functions-and-the-ilb-ase"></a>Tarefas da Web, as funções e o ILB ASE ##

Funções e trabalhos web são suportados em ILB ASE, mas para o portal trabalhar com os mesmos, tem de ter acesso à rede para o site SCM.  Isto significa que o browser tem de estar num anfitrião que está na ou ligado à rede virtual.  

Quando utiliza as funções do Azure num ILB ASE, poderá receber uma mensagem de erro que indica "não é possível obter as suas funções neste momento. Tente novamente mais tarde." Este erro ocorre porque a IU de funções tira partido do site SCM através de HTTPS e o certificado de raiz não está a ser a cadeia de fidedignidade de browser. Trabalhos Web tem um problema semelhante. Para evitar este problema, pode efetuar um dos seguintes:

- Adicione o certificado para o arquivo de certificados fidedignos. Isto unblocks Edge e do Internet Explorer.
- Utilizar o Chrome e vá para o site SCM pela primeira vez, aceitar o certificado não fidedigno e, em seguida, aceda ao portal do.
- Utilize um certificado comercial que está a ser a cadeia de fidedignidade de browser.  Esta é a melhor opção.  

## <a name="dns-configuration"></a>Configuração de DNS ##

Quando utiliza um VIP externo, o DNS é gerido pelo Azure. Qualquer aplicação que criou no seu ASE é adicionada automaticamente ao DNS do Azure, que é um DNS público. Está num ILB ASE, tem de gerir os seus próprios DNS. Para um determinado domínio, tal como _contoso.net_, terá de criar registos de DNS A no seu DNS que apontam para o seu endereço ILB para:

- *. contoso.net
- *. scm.contoso.net

Se o domínio ASE do ILB for utilizado para várias coisas fora deste ASE, poderá ter de gerir o DNS numa base por aplicação-nome. Este método é difícil, porque terá de adicionar cada novo nome de aplicação no seu DNS aquando da respetiva criação. Por este motivo, recomendamos que utilize um domínio dedicado.

## <a name="publish-with-an-ilb-ase"></a>Publicar com ASE do ILB ##

Para cada aplicação que é criada, existem dois pontos finais. Está num ILB ASE, tiver  *&lt;nome da aplicação >.&lt; Domínio do ILB ASE >* e  *&lt;nome da aplicação > SCM.&lt; Domínio do ILB ASE >*. 

O nome do site SCM leva-o para a consola do Kudu, denominada o **portal avançada**, no portal do Azure. A consola do Kudu permite-lhe ver as variáveis de ambiente, explore o disco, utilize uma consola e muito mais. Para obter mais informações, consulte [App Service do Azure na consola do Kudu][Kudu]. 

O multi-inquilino do serviço de aplicações e está num ASE externa, não há início de sessão único entre o portal do Azure e a consola do Kudu. Para ILB ASE, no entanto, tem de utilizar as credenciais da sua publicação para iniciar sessão na consola do Kudu.

Sistemas CI baseado na Internet, tais como o GitHub e o Visual Studio Team Services, não funcionam com ILB ASE, porque o ponto final da publicação não está acessível a internet. Em vez disso, tem de utilizar um sistema de CI que utiliza um modelo de extração, tais como o Dropbox.

Os pontos finais de publicação de aplicações está num ILB ASE utilizam o domínio que o ILB ASE foi criado com. Este domínio é apresentado no perfil de publicação da aplicação e no painel da aplicação do portal (**descrição geral** > **Essentials** e também **propriedades**). Se tiver ASE ILB com o subdomínio *contoso.net* e uma aplicação com o nome *mytest*, utilize *mytest.contoso.net* para FTP e *mytest.scm.contoso.net* para implementação na web.

## <a name="couple-an-ilb-ase-with-a-waf-device"></a>Couple ILB ASE com um dispositivo WAF ##

App Service do Azure fornece várias medidas de segurança que proteger o sistema. Também ajudam a determinar se uma aplicação foi vítima de acesso ilícito. A melhor proteção para uma aplicação web é couple uma plataforma de alojamento, tais como o App Service do Azure, com uma firewall de aplicação web (WAF). Uma vez o ILB ASE tem um ponto final da aplicação de isolada de rede, é adequado para uma utilização.

Para obter mais informações sobre como configurar o ILB ASE com um dispositivo WAF, consulte [configurar uma firewall de aplicação web com o seu ambiente de serviço de aplicações][ASEWAF]. Este artigo mostra como utilizar uma aplicação virtual Barracuda com seu ASE. Outra opção consiste em utilizar o Gateway de aplicação do Azure. Gateway de aplicação utiliza as regras de núcleos OWASP para proteger as aplicações colocadas atrás-lo. Para obter mais informações sobre o Gateway de aplicação, consulte [introdução à firewall de aplicação web do Azure][AppGW].

## <a name="get-started"></a>Introdução ##

* Para obter uma introdução ASEs, consulte o artigo [introdução para ambientes do App Service][Intro].
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

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
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
