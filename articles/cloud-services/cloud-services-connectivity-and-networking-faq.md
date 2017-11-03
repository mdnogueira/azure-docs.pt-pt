---
title: Problemas de conectividade e de rede para o Microsoft Azure Cloud Services FAQ | Microsoft Docs
description: "Este artigo apresenta uma lista de perguntas mais frequentes sobre a conectividade e de rede para serviços de nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: 7b435b6904b05228a63e3ed3a9fed78747b843c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de conectividade e de rede do Cloud Services do Azure: Perguntas mais frequentes sobre (FAQ)

Este artigo inclui perguntas mais frequentes sobre problemas de conectividade e de rede para [dos serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços em nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Posso não é possível reservar um IP num serviço de nuvem a várias VIP
Em primeiro lugar, certifique-se de que a instância de máquina virtual que está a tentar reservar o IP está ativada. Segundo, certifique-se de que está a utilizar IPs reservados para implementações de teste e produção. **Não** alterar as definições ao atualizar de implementação.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Como posso ambiente de trabalho remoto quando tiver um NSG?
Adicione regras para o NSG que permitam o tráfego em portas **3389** e **20000**.  Ambiente de trabalho remoto utiliza a porta **3389**.  Instâncias de serviço de nuvem são carga balanceada, pelo diretamente não é possível controlar que instância para ligar a.  O *RemoteForwarder* e *RemoteAccess* agentes gerir o tráfego RDP e permitir que o cliente enviar um cookie RDP e especifique uma instância individual para ligar a.  O *RemoteForwarder* e *RemoteAccess* agentes requerem essa porta **20000** ser aberto, que pode ser bloqueado se tiver um NSG.

## <a name="can-i-ping-a-cloud-service"></a>Posso ping um serviço em nuvem?

Não, não utilizando o normal "ping" / protocolo ICMP. O protocolo ICMP não é permitido através do Balanceador de carga do Azure.

Para testar a conectividade, recomendamos que efetue um ping de porta. Enquanto Ping.exe utiliza o ICMP, outras ferramentas, como o PSPing, Nmap e telnet permitem-lhe testar a conectividade à porta TCP específica.

Para obter mais informações, consulte [utilizar pings porta em vez de ICMP para testar a conectividade da VM do Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Como impedir a receção milhares de pedidos de endereços IP desconhecidos que indiquem algum tipo de ataque malicioso para o serviço em nuvem?
Azure implementa um segurança de rede multicamada para proteger os seus serviços da plataforma contra ataques em distribuída denial of service (DDoS distribuídos). O sistema de defesa Azure DDoS faz parte monitorização processo contínuo do Azure, que é continuamente melhorado através de penetração de teste. Este sistema de defesa DDoS foi concebido para conseguir não apenas os ataques de exterior, mas também a partir de outros inquilinos do Azure. Para obter mais detalhes, consulte [segurança de rede do Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Também pode criar uma tarefa de arranque para bloquear seletivamente alguns endereços IP específicos. Para obter mais informações, consulte [bloquear um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quando tento RDP à minha instância de serviço em nuvem, é apresentada a mensagem, "a conta de utilizador expirou."
Pode obter a mensagem de erro "Esta conta de utilizador expirou" quando a ignorar a data de expiração que está configurada nas definições do RDP. Pode alterar a data de expiração do portal, seguindo estes passos:
1. Inicie sessão na consola de gestão do Azure (https://manage.windowsazure.com), navegue até ao seu serviço de nuvem e selecione o **configurar** separador.
2. Selecione **remoto**.
3. Alterar a data "Expira em" e, em seguida, guarde a configuração.

Pode agora deve conseguir RDP para a máquina.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Por que motivo é LoadBalancer não balanceamento tráfego equitativamente?
Para obter informações sobre como interna funciona de Balanceador de carga, consulte [novo modo de distribuição do Balanceador de carga do Azure](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

O algoritmo de distribuição utilizado é uma 5 cadeias de identificação (IP, porta de origem, de origem, IP de destino, porta de destino, protocolo tipo) hash para mapear o tráfego para servidores disponíveis. Fornece retenções apenas dentro de uma sessão de transporte. Pacotes na mesma sessão TCP ou UDP serão direcionados para a mesma instância de IP (DIP) do Centro de dados por trás o ponto final com balanceamento de carga. Quando o cliente fecha e abra novamente a ligação ou inicia uma nova sessão a partir do mesmo IP de origem, a porta de origem é alterado e faz com que o tráfego Ir para um endpoint diferente do DIP.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>Como pode redirecionar o tráfego de entrada para a minha predefinido do URL do serviço de nuvem para um URL personalizado? 

O URL Rewrite módulo dos IIS poderia ser utilizado para redirecionar o tráfego proveniente para o URL predefinido para o serviço em nuvem (por exemplo, \*. cloudapp.net) para algumas DNS nome/URL personalizado. Uma vez que o URL Rewrite Module por predefinição, ativada nas funções da Web e as respetivas regras estão configuradas no Web. config da aplicação, seria sempre estar disponível na VM, independentemente das reinícios/reimages. Para obter mais informações, consulte:

- [Criar regras de reescrever para o URL Rewrite módulo](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Como remover ligação predefinida](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Como pode posso bloco/desativar o tráfego de entrada para o URL predefinido do meu serviço em nuvem? 

Pode evitar o tráfego de entrada predefinido/nome de URL do seu serviço de nuvem (por exemplo, \*. cloudapp.net) definindo o cabeçalho de anfitrião para um nome DNS personalizado (por exemplo, www. MyCloudService.com) em configuração de enlace de site no ficheiro de definição (*.csdef) do serviço em nuvem como indicado abaixo: 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
Como este enlace de cabeçalho de anfitrião é imposta através do ficheiro de csdef, o serviço só seria acessível através do nome personalizado 'www.MyCloudService.com', enquanto todas as recebidos pedidos para o ' *. cloudapp.net' domínio irão falhar sempre. Que a ser consiga aceder tal, se, no entanto, utilizar uma sonda SLB personalizada ou um balanceador de carga internos no serviço, bloquear predefinido/nome de URL do serviço pode interferir com o comportamento de pesquisa. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Como certificar-se de que o endereço IP destinado ao público de um serviço em nuvem (ou seja, o VIP) nunca é alterado para que poderia ser normalmente na lista de permissões por alguns clientes específicos?

Para adicionar à lista branca o endereço IP do seus serviços em nuvem, é recomendado que tem um IP reservado com associados; caso contrário, será possível desalocar IP Virtual fornecido pelo Azure da sua subscrição, se eliminar a implementação. Tenha em atenção que para a operação de alternância de VIP com êxito terá de IPs reservados individuais para produção e teste ranhuras, na ausência da qual troca operação falhará. Siga estes artigos para reservar um endereço IP e associá-la com os seus serviços em nuvem:  
 
- [O endereço IP de um serviço em nuvem existente de reserva](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associar um IP reservado para um serviço em nuvem, utilizando um ficheiro de configuração de serviço](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Desde que tenham mais do que uma instância para as funções, associar o seu serviço em nuvem RIP não deve causar qualquer período de inatividade. Em alternativa, pode lista branca o intervalo de IP do seu centro de dados do Azure. Pode encontrar todos os intervalos de IP de Azure [aqui](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Este ficheiro contém os intervalos de endereços IP (incluindo intervalos de Computação, SQL e Armazenamento) utilizados no Centro de Transferências da Microsoft. Um ficheiro atualizado é publicado semanalmente, refletindo os intervalos implementados atualmente e as alterações futuras para os intervalos IP. Os novo intervalos que aparecem no ficheiro não serão utilizados nos centros de dados durante, pelo menos, uma semana. Transfira o ficheiro xml novo todas as semanas e efetue as alterações necessárias no seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores Express Route poderão notar este ficheiro utilizado para atualizar o anúncio BGP do espaço Azure na primeira semana de cada mês. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Como utilizar as VNets do Azure Resource Manager com serviços em nuvem? 

Serviços cloud não não possível colocar um vnets do Gestor de recursos do Azure, mas um VNets do Gestor de recursos do Azure e um VNets clássicas podem ser ligados através do peering. Para obter mais informações, consulte [peering de rede Virtual](../virtual-network/virtual-network-peering-overview.md).