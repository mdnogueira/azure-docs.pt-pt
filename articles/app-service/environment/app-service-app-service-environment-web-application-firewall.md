---
title: "Configurar uma Firewall de aplicação Web (WAF) para o ambiente de serviço de aplicações"
description: "Saiba como configurar uma firewall de aplicação web à frente do seu ambiente de serviço de aplicações."
services: app-service\web
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: naziml
ms.openlocfilehash: 4c0e2d649f71d7797efbfe2c8e93ea0c844152df
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configurar uma Firewall de aplicação Web (WAF) para o ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Firewalls de aplicação Web, como o [Barracuda WAF para o Azure](https://www.barracuda.com/programs/azure) que está disponível na [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) ajuda a proteger as aplicações web através da inspeção de tráfego de entrada web para bloquear o SQL Server injections, processamento de scripts entre sites, software maligno carregamentos & aplicação DDoS e outros ataques. É também inspeciona as respostas de servidores web de back-end para prevenção de perda de dados (DLP). Combinada com o isolamento e dimensionamento adicionais fornecidas pelo ambientes do App Service, isto fornece um ambiente ideal para alojar empresariais críticos web Apps que têm de conseguir pedidos maliciosos e o tráfego de elevado volume.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Configurar
Para este documento que irá configurar o nosso ambiente de serviço de aplicações por trás da carga de vários com balanceamento de instâncias de Barracuda WAF para que apenas o tráfego do WAF pode alcançar o ambiente de serviço de aplicações e não estar acessível a partir da rede de Perímetro. Também temos Traffic Manager do Azure à frente do nosso instâncias Barracuda WAF para equilibrar a carga entre centros de dados do Azure e regiões. Um diagrama de alto nível da configuração teria aspeto que é mostrado abaixo.

![Arquitetura][Architecture] 

> Nota: com a introdução de [ILB suporte para o ambiente de serviço de aplicações](app-service-environment-with-internal-load-balancer.md), pode configurar o ASE para estar acessível a partir da rede de Perímetro e apenas à rede privada. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Configurar o ambiente de serviço de aplicações
Para configurar um ambiente de serviço de aplicações, consulte a [nossa documentação](app-service-web-how-to-create-an-app-service-environment.md) sobre o assunto. Assim que tiver um ambiente de serviço de aplicações criado, pode criar Web Apps, API Apps e [Mobile Apps](../../app-service-mobile/app-service-mobile-value-prop.md) neste ambiente que serão todas protegidos por trás WAF configurarmos na secção seguinte.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configurar o serviço de nuvem WAF Barracuda
Barracuda tem um [artigo detalhado](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sobre como implementar o respetivo WAF numa máquina virtual no Azure. Mas, uma vez que pretende redundância e introduz um ponto único de falha, que pretende implementar, pelo menos, 2 VMs de instância de WAF no mesmo serviço em nuvem, ao seguir estas instruções.

### <a name="adding-endpoints-to-cloud-service"></a>Adicionar pontos finais de serviço em nuvem
Assim que tiver 2 ou instâncias de VM de WAF mais no seu serviço em nuvem pode utilizar o [portal do Azure](https://portal.azure.com/) para adicionar pontos finais HTTP e HTTPS que são utilizados pela sua aplicação, como mostrado na imagem abaixo.

![Configurar o ponto final][ConfigureEndpoint]

Se as suas aplicações utilizam outros pontos finais, certifique-se adicionar os a esta lista bem. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configurar Barracuda WAF através do seu Portal de gestão
Barracuda WAF utiliza 8000 de porta de TCP para a configuração através do seu portal de gestão. Uma vez que temos várias instâncias de VMs WAF, terá de repetir os passos aqui descritos para cada instância VM. 

> Nota: Depois de terminar com a configuração de WAF, remova o ponto final TCP/8000 todas as suas VMs WAF para manter a sua WAF segura.
> 
> 

Adicione o ponto final de gestão, conforme mostrado na imagem abaixo para configurar o seu WAF Barracuda.

![Adicionar ponto final de gestão][AddManagementEndpoint]

Utilize um browser para navegar para o ponto final de gestão do seu serviço de nuvem. Se o seu serviço em nuvem é chamado test.cloudapp.net, seria aceder a este ponto final ao navegar para http://test.cloudapp.net:8000. Deverá ver uma página de início de sessão, conforme mostrado abaixo que pode iniciar sessão utilizando as credenciais especificadas na fase de configuração de WAF VM.

![Página de início de sessão de gestão][ManagementLoginPage]

Uma vez que iniciar sessão, deverá ver um dashboard que na imagem abaixo que irá apresentar estatísticas básicas sobre a proteção de WAF.

![Dashboard de gestão][ManagementDashboard]

Clicar no separador de serviços irá permitir-lhe configurar o seu WAF para serviços que está a proteger. Para obter mais detalhes sobre como configurar a sua WAF Barracuda pode consultar [respetiva documentação](https://techlib.barracuda.com/waf/getstarted1). No exemplo abaixo de uma aplicação Web do Azure que serve o tráfego HTTP e HTTPS foi configurada.

![Gestão de adicionar serviços][ManagementAddServices]

> Nota: Dependendo do modo como estão configuradas as suas aplicações e quais as funcionalidades que estão a ser utilizadas no seu ambiente de serviço de aplicações, terá de encaminhar o tráfego para TCP portas que não sejam 80 e 443, por exemplo, se tiver o programa de configuração de SSL de IP para uma aplicação Web. Para obter uma lista das portas de rede utilizados em ambientes do App Service, consulte [da documentação do tráfego de entrada do controlo](app-service-app-service-environment-control-inbound-traffic.md) secção de portas de rede.
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configurar o Gestor de tráfego do Microsoft Azure (opcional)
Se a aplicação está disponível em várias regiões, em seguida, pretender carregar equilibrá-los por trás [Traffic Manager do Azure](../../traffic-manager/traffic-manager-overview.md). Para fazê pode adicionar um ponto final no [portal clássico do Azure](https://manage.azure.com) utilizando o nome de serviço em nuvem para a sua WAF no perfil do Gestor de tráfego, conforme mostrado na imagem abaixo. 

![Ponto final do Gestor de tráfego][TrafficManagerEndpoint]

Se a sua aplicação requer autenticação, certifique-se de que tem alguns recursos que não requerem qualquer autenticação para o Gestor de tráfego para enviar um ping para a disponibilidade da aplicação. Pode configurar o URL na secção Configurar no [portal clássico do Azure](https://manage.azure.com) conforme mostrado abaixo.

![Configurar o Gestor de Tráfego][ConfigureTrafficManager]

Para reencaminhar os pings do Traffic Manager do seu WAF à sua aplicação, precisa de configuração do Web site traduções no seu WAF Barracuda para reencaminhar tráfego para a aplicação conforme mostrado no exemplo abaixo.

![Traduções de Web site][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Proteger o tráfego para o ambiente de serviço de aplicações utilizando grupos de segurança de rede (NSG)
Siga o [documentação de tráfego de entrada do controlo](app-service-app-service-environment-control-inbound-traffic.md) para obter detalhes sobre a restringir tráfego para o ambiente de serviço de aplicações do WAF apenas utilizando o endereço VIP do seu serviço de nuvem. Eis um comando do Powershell de exemplo para efetuar esta tarefa para a porta TCP 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Substitua o SourceAddressPrefix o endereço Virtual IP (VIP) do serviço em nuvem seu WAF.

> Nota: O VIP do seu serviço de nuvem será alterado quando eliminar e voltar a criar o serviço em nuvem. Certifique-se de que atualiza o endereço IP no grupo de recursos de rede depois de fazê-lo. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
