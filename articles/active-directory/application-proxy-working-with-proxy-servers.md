---
title: Trabalhar com existente no local servidores proxy e o Azure AD | Microsoft Docs
description: Abrange como trabalhar com servidores de proxy no local existentes.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 47b17c0d81e8597d3125949b26906cc356a9af11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Trabalhar com servidores de proxy no local existentes

Este artigo explica como configurar conectores de Proxy de aplicações do Azure Active Directory (Azure AD) para trabalhar com servidores de proxy de saída. Destina-se para clientes com ambientes de rede que tenham proxies existentes.

Iremos começar observando nestes cenários de implementação principal:
* Configure os conectores para ignorar os proxies de saída no local.
* Configure os conectores para utilizar um proxy de saída para aceder a Proxy de aplicações do Azure AD.

Para obter mais informações sobre como funcionam os conectores, consulte [conetores da Proxy da aplicação Azure compreender AD](application-proxy-understand-connectors.md).

## <a name="bypass-outbound-proxies"></a>Proxies de saída de Desativação

Os conectores têm subjacentes componentes de SO que efetuam pedidos de saída. Estes componentes automaticamente tentarem localizar um servidor proxy na rede através da deteção automática de Proxy Web (WPAD).

Os componentes de SO tentarem localizar um servidor proxy, execute uma pesquisa DNS para wpad.domainsuffix. Se a pesquisa é resolvido no DNS, em seguida, é efetuado um pedido HTTP para o endereço IP para wpad.dat. Este pedido torna-se o script de configuração de proxy no seu ambiente. O conector utiliza este script para selecionar um servidor de proxy de saída. No entanto, o tráfego de conector poderá ainda não percorrer, devido às definições de configuração adicional necessárias no proxy.

Pode configurar o conector para ignorar o proxy no local para se certificar de que utiliza uma ligação direta aos serviços do Azure. Recomendamos esta abordagem, desde que a política de rede permite, porque significa que tem uma menor configuração manter.

Para desativar a utilização de proxy de saída para o conector, edite o ficheiro de C:\Program Files\Microsoft AAD aplicação Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione o *system.net* secção mostrada neste exemplo de código:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
Para garantir que o serviço Atualizador do conector também ignora o proxy, efetue uma alteração semelhante para o ficheiro ApplicationProxyConnectorUpdaterService.exe.config. Este ficheiro está localizado em C:\Program Files\Microsoft AAD aplicação Proxy conector Atualizador.

Lembre-se de que faça cópias dos ficheiros originais, caso necessário reverter para os ficheiros. config de predefinição.

## <a name="use-the-outbound-proxy-server"></a>Utilizar o servidor de proxy de saída

Em alguns ambientes necessitam de todo o tráfego de saída passar por um proxy de saída, sem exceção. Como resultado, a ignorar o proxy não é uma opção.

Pode configurar o tráfego de conector para percorrer o proxy de saída, conforme mostrado no diagrama seguinte:

 ![Configurar conector tráfego para passar por um proxy de saída para Proxy de aplicações do Azure AD](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

Como resultado ter apenas o tráfego de saída, não é necessário para configurar o acesso de entrada através das firewalls.

>[!NOTE]
>Proxy de aplicações não suporta a autenticação para outros proxies. As contas de serviço de rede do conector/atualizador devem ser capazes de ligar para o proxy sem ser solicitados para autenticação.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Passo 1: Configurar o conector e os serviços relacionados ao percorrer o proxy de saída

Se WPAD está ativado no ambiente e corretamente configurado, o conector Deteta automaticamente o servidor de proxy de saída e a tentar utilizá-lo. No entanto, pode configurar explicitamente o conector passar por um proxy de saída.

Para fazê-lo, edite o ficheiro de C:\Program Files\Microsoft AAD aplicação Proxy Connector\ApplicationProxyConnectorService.exe.config e adicione o *system.net* secção mostrada neste exemplo de código. Alteração *proxyserver:8080* para refletir o nome do servidor local proxy ou endereço IP e a porta que está a escutar no.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Em seguida, configure o serviço Atualizador do conector para utilizar o proxy ao fazer uma alteração semelhante para o ficheiro de C:\Program Files\Microsoft AAD aplicação Proxy conector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Passo 2: Configurar o proxy para permitir tráfego do conector e serviços relacionados a passagem

Existem quatro aspetos a considerar ao proxy de saída:
* Regras de saída de proxy
* Autenticação do proxy
* Portas do proxy
* Inspeção de SSL

#### <a name="proxy-outbound-rules"></a>Regras de saída de proxy
Permitir o acesso para os seguintes pontos finais para o acesso de serviço do conector:

* *. msappproxy.net
* *. servicebus.windows.net

Para o registo inicial permitir o acesso aos pontos finais do seguintes:

* login.windows.net
* login.microsoftonline.com

Se não é possível permitir a conetividade através do FQDN e tem de especificar os intervalos de IP em vez disso, utilize estas opções:

* Permita o acesso de saída do conector para todos os destinos.
* Permitir o acesso de saída do conector para todas as [intervalos IP do datacenter do Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). O desafio com utilizando a lista de intervalos IP do datacenter do Azure é o que é atualizada semanalmente. Tem de colocar um processo no local para garantir que as regras de acesso são atualizadas em conformidade. Utilizar apenas um subconjunto dos endereços IP pode fazer com que a configuração para interromper.

#### <a name="proxy-authentication"></a>Autenticação do proxy

Autenticação do proxy não é atualmente suportada. A nossa recomendação atual é permitir o acesso anónimo do conector para os destinos de Internet.

#### <a name="proxy-ports"></a>Portas do proxy

O conector permite ligações de saída baseada em SSL utilizando o método de ligação. Este método configura essencialmente um túnel através do proxy de saída. Configure o servidor de proxy para permitir o protocolo de túnel para as portas 443 e 80.

>[!NOTE]
>Quando o Service Bus é executada através de HTTPS, utiliza a porta 443. No entanto, por predefinição, o barramento de serviço tenta estabelecer ligações de TCP diretas e retrocede para HTTPS apenas se a falha de ligação direta.

#### <a name="ssl-inspection"></a>Inspeção de SSL
Não utilize inspeção de SSL para o tráfego de conector, porque faz com que problemas para o tráfego de conector. O conector utiliza um certificado para autenticar para o serviço de Proxy de aplicações e esse certificado pode ser perdido durante a inspeção de SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Resolver problemas de proxy de conector e problemas de conectividade de serviço
Agora deverá ver todo o tráfego que flui através do proxy. Se tiver problemas, devem ajudar as seguintes informações de resolução de problemas.

É a melhor forma de identificar e resolver problemas de conectividade do conector para tirar uma captura de rede ao iniciar o serviço de conectores. Aqui estão algumas sugestões rápidas sobre a captura e filtragem de rastreios de rede.

Pode utilizar a ferramenta monitorização à sua escolha. Para efeitos deste artigo, utilizámos o Microsoft Message Analyzer. Pode [transfiram a partir do Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Os exemplos seguintes são específicos para o Message Analyzer, mas estes princípios podem ser aplicados a qualquer ferramenta de análise.

### <a name="take-a-capture-of-connector-traffic"></a>Tirar uma captura de tráfego de conector

Para resolução de problemas iniciais, execute os seguintes passos:

1. De services.msc, pare o serviço do conector de Proxy de aplicações do Azure AD.

   ![Serviço de conector do Proxy da aplicação AD do Azure no services.msc](./media/application-proxy-working-with-proxy-servers/services-local.png)

2. Execute o Message Analyzer como administrador.
3. Selecione **Iniciar rastreio local**.

   ![Captura de rede de início](./media/application-proxy-working-with-proxy-servers/start-local-trace.png)

3. Inicie o serviço do conector de Proxy de aplicações do Azure AD.
4. Pare a captura de rede.

   ![Pare a captura de rede](./media/application-proxy-working-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Verifique se o tráfego de conector ignora proxies de saída

Se tiver configurado o conector do Proxy de aplicação para ignorar os servidores de proxy e ligar diretamente ao serviço de Proxy de aplicações, que pretende ver na captura de rede para as tentativas de ligação de TCP falhou. 

Utilize o filtro de Message Analyzer para identificar estes tentativas. Introduza `property.TCPSynRetransmit` nas caixa para filtrar e selecione **aplicar**. 

Um pacote de SIN é o primeiro pacote enviado para estabelecer uma ligação de TCP. Se este pacote não devolverem uma resposta, a SIN é reattempted. Pode utilizar o filtro anterior para ver qualquer SYNs retransmitted. Em seguida, pode verificar se estes SYNs correspondem a qualquer tráfego relacionado com o conector.

Se espera que o conector para tornar as ligações diretas para os serviços do Azure, as respostas de SynRetransmit na porta 443 são uma indicação de que tem um problema de rede ou firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Verifique se o tráfego de conector utiliza proxies de saída

Se tiver configurado o tráfego de conector do Proxy de aplicações para percorrer os servidores proxy, que pretende procurar ligações https falhou para o proxy. 

Para filtrar a captura de rede para estes tentativas de ligação, introduza `(https.Request or https.Response) and tcp.port==8080` no filtro Message Analyzer, substituindo 8080 com a porta do serviço de proxy. Selecione **aplicar** para ver os resultados do filtro. 

O filtro anterior mostra apenas a pedidos de HTTPs e respostas de/para a porta de proxy. Procura os pedidos de ligação que mostram a comunicação com o servidor proxy. Após com êxito, receberá uma resposta de HTTP OK (200).

Se vir outros códigos de resposta, como 407 ou 502, o que significa que o proxy exigir autenticação ou não permitir o tráfego por algum motivo. Neste momento, interagir com a equipa de suporte de servidor proxy.

## <a name="next-steps"></a>Passos seguintes

- [Compreender os conectores de Proxy de aplicações do Azure AD](application-proxy-understand-connectors.md)

- Se tiver problemas com problemas de conectividade do conector, pergunte a sua pergunta no [fórum do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) ou crie um pedido com a nossa equipa de suporte.
