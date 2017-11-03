---
title: "Compreender os conectores de Proxy de aplicações do Azure AD | Microsoft Docs"
description: "Abrange as noções básicas sobre conectores de Proxy de aplicações do Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 9dce8c3132b60b0b0c44f9f9d1e9cf01f68fa280
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Compreender os conectores de Proxy de aplicações do Azure AD

Os conectores são o que fazer o Proxy de aplicações do Azure AD possíveis. São simples, fácil de implementar e manter e super poderosas. Este artigo aborda os conectores são, como funcionam e algumas sugestões sobre como otimizar a implementação. 

## <a name="what-is-an-application-proxy-connector"></a>O que é um conector do Proxy de aplicações?

Os conectores são agentes simples que manter-se no local e o facilitam a ligação de saída para o serviço de Proxy de aplicações. Conectores tem de ser instalados num servidor do Windows que tenha acesso à aplicação de back-end. Pode organizar os conectores em grupos de conector, com cada grupo de processamento de tráfego para aplicações específicas. Balanceamento de carga de conectores automaticamente e pode ajudar a otimizar a estrutura da sua rede. 

## <a name="requirements-and-deployment"></a>Requisitos e implementação

Para implementar o Proxy de aplicações com êxito, tem de, pelo menos, um conector, mas recomendamos dois ou mais para maior resiliência. Instale o conector no Windows Server 2012 R2 ou numa máquina 2016. O conector tem de conseguir comunicar com o serviço de Proxy de aplicações, bem como as aplicações no local que publicar. 

Para obter mais informações sobre os requisitos de rede para o servidor do conector, consulte [começar com o Proxy da aplicação e instale um conector](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Manutenção
Os conectores e o serviço asseguramos todas as tarefas de elevada disponibilidade. Podem ser adicionados ou removidos dinamicamente. Sempre que um novo pedido chega é encaminhado para um dos conectores que se encontra disponível. Se um conector temporariamente não estiver disponível, não responder a este tráfego.

Os conectores estão sem monitorização de estado e de que não existem dados de configuração da máquina. Os dados únicos armazenam são as definições para ligar o serviço e o seu certificado de autenticação. Quando se ligam ao serviço, estes extraia todos os dados de configuração necessárias e atualização-lo a cada alguns minutos.

Os conectores também consultam o servidor para saber se há uma versão mais recente do conector. Se for encontrado um, os conectores atualizar próprios.

Pode monitorizar os conectores da máquina que estão em execução, utilizar o registo de eventos e contadores de desempenho. Ou, pode ver o respetivo estado da página de Proxy de aplicações do portal do Azure:

 ![AzureAD Conetores da Proxy da aplicação](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Não tem de eliminar manualmente os conectores que são utilizados. Quando um conector está em execução, permanece ativa como se liga ao serviço. Os conectores são etiquetados como _inativa_ e são removidos depois de 10 dias de inatividade. Se pretender desinstalar um conetor, desinstale, o serviço de conectores e o serviço Atualizador do servidor. Reinicie o computador para remover completamente o serviço.

## <a name="automatic-updates"></a>Atualizações automáticas

O Azure AD fornece as atualizações automáticas para todos os conectores que implementar. O Atualizador de conector do Proxy de aplicação está em execução, desde que os conectores atualizar automaticamente. Se não vir o serviço Atualizador do conector no seu servidor, terá de [reinstalar o conector](active-directory-application-proxy-enable.md) para obter todas as atualizações. 

Se não pretender aguardar por uma atualização automática fique para o conector, pode efetuar uma atualização manual. Vá para o [página de transferência do conector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) no servidor onde o conector está localizado e selecione **transferir**. Este processo arranca uma atualização para o conector local. 

Para inquilinos com vários conectores, as atualizações automáticas ter como destino um conector numa altura em cada grupo para impedir que o período de indisponibilidade no seu ambiente. 

Pode deparar-se um período de indisponibilidade quando o conector atualiza se:  
- Tem apenas um conector. Para evitar este período de indisponibilidade e melhorar a elevada disponibilidade, recomendamos que instale um segundo conector e [criar um grupo de conector](active-directory-application-proxy-connectors-azure-portal.md).  
- Um conector foi meio de uma transação quando iniciou a atualização. Apesar da transação inicial se tenha perdida, o browser deverá automaticamente repetir a operação ou Atualize a página. Quando é reenviar o pedido, o tráfego é encaminhado um conector de cópia de segurança.

## <a name="creating-connector-groups"></a>Criar grupos de conector

Grupos de conetor permitem-lhe atribuir conectores específicos para servirem aplicações específicas. Pode agrupar vários conectores e, em seguida, atribua a cada aplicação para um grupo. 

Os grupos de conector tornam mais fácil de gerir implementações de grande. Também melhorar latência para inquilinos que têm aplicações alojadas em regiões diferentes, porque pode criar grupos de conector baseada na localização para servirem aplicações apenas locais. 

Para mais informações sobre grupos de conector, consulte o artigo [publicar aplicações em redes separadas e localizações utilizar grupos de conector](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Planeamento da Capacidade 

Enquanto conectores serão automaticamente o balanceamento de carga dentro de um grupo de conector, também é importante certificar-se de que ter planeada capacidade suficiente entre os conectores para processar o volume de tráfego esperado. Em geral, os utilizadores mais tiver, uma máquina que terá maior. Segue-se uma tabela dar uma descrição do volume de vários computadores pode processar. Tenha em atenção todos os baseia-se no esperado transações por segundo (TPS) em vez de por utilizador desde utilização padrões variam e não podem ser utilizados para prever a carga.  Tenha também em atenção que será algumas diferenças com base no tamanho de respostas e o tempo de resposta da aplicação de back-end - maior tamanhos de resposta e tempos de resposta mais lentos resultará num TPS máxima inferior.

|Núcleos|RAM|Era esperado latência (MS)-P99|TPS máx.|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\*Esta máquina tinha um limite de ligação de 800. Para todas as outras máquinas utilizámos o limite de ligação 200 predefinido.
 
>[!NOTE]
>Não é muito diferença nos TPS máximos entre 4, 8 e 16 núcleos máquinas. A principal diferença entre as está a ser a latência esperada.  

## <a name="security-and-networking"></a>Segurança e de rede

Os conectores podem ser instalados em qualquer lugar na rede que lhe permita enviar pedidos para o serviço de Proxy de aplicações. O que é importante é que o computador que executa o conector também tem acesso às suas aplicações. Pode instalar os conectores dentro da sua rede empresarial ou numa máquina virtual que é executado na nuvem. Os conectores podem ser executados dentro de uma zona desmilitarizada (DMZ), mas não é necessário porque todo o tráfego é de saída para a sua rede permanece segura.

Conectores enviam apenas pedidos de saída. O tráfego de saída é enviado para o serviço de Proxy de aplicações e as aplicações publicadas. Não tem de abrir portas de entrada, porque o tráfego flui ambas as direções assim que uma sessão for estabelecida. Não tem de configurar o balanceamento da carga entre os conectores ou configurar o acesso de entrada através das firewalls. 

Para obter mais informações sobre como configurar as regras de firewall de saída, consulte [funcionam com existente no local servidores proxy](application-proxy-working-with-proxy-servers.md).

Utilize o [do Azure AD aplicação Proxy portas teste ferramenta do conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) para verificar que o conector pode contactar o serviço de Proxy de aplicações. No mínimo, certifique-se de que a região EUA Central e a região mais próxima têm todas as marcas de verificação verdes. Para além disso, as marcas de verificação verde mais significa maior resiliência. 

## <a name="performance-and-scalability"></a>Desempenho e escalabilidade

Dimensionamento para o serviço de Proxy de aplicações é transparente, mas o dimensionamento é um fator para os conectores. Tem de ter suficiente conectores para processar o pico de tráfego. No entanto, não terá de configurar o balanceamento de carga porque todos os conectores dentro de um grupo de conector automaticamente o balanceamento de carga.

Uma vez que os conectores são sem monitorização de estado, não são afetados pelo número de utilizadores ou sessões. Em vez disso, se respondem ao número de pedidos e do tamanho do payload. Com o tráfego web padrão, uma máquina média pode processar alguns milhares pedidos por segundo. A capacidade específica depende as características de máquina exato. 

O desempenho do conector está vinculado à CPU e de rede. Desempenho da CPU necessária para a encriptação SSL e de desencriptação, enquanto redes são importante para obter conetividade rápida para as aplicações e o serviço online no Azure.

Em contrapartida, a memória é inferior de um problema de conectores. O serviço online encarrega-se de grande parte do processamento e todo o tráfego não autenticado. Tudo o que pode ser feito na nuvem é efetuado na nuvem. 

O balanceamento de carga ocorre entre os conectores de um grupo de conector indicado. Tal é uma variação de um round robin para determinar o conector no grupo serve um pedido específico. Depois de escolher um conector, vamos manter uma afinidade de sessão entre esse utilizador e a aplicação durante a sessão. Se, por qualquer motivo que conector ou máquina se tornar indisponível, o tráfego começará vai outro conector no grupo. A resiliência está também por que motivo Recomendamos ter vários conectores.

Outro fator que afeta o desempenho é a qualidade de rede entre os conectores, incluindo: 

* **O serviço online**: ligações lentas ou de latência alta ao Proxy da aplicação de serviço no Azure influência o desempenho do conector. Para obter o melhor desempenho, ligar a sua organização para o Azure com o Expressroute. Caso contrário, ter a sua equipa de rede, certifique-se de que as ligações para o Azure são processadas mais eficientemente possível. 
* **As aplicações de back-end**: em alguns casos, existem proxies adicionais entre o conector e as aplicações de back-end que podem lenta ou impedir ligações. Para resolver problemas neste cenário, abra um browser a partir do servidor do conector e tentar aceder à aplicação. Se executar os conectores no Azure, mas as aplicações estão no local, a experiência poderá não ser que o se esperava os seus utilizadores.
* **Os controladores de domínio**: se os conectores realizar SSO utilizando a delegação restrita de Kerberos, contactar controladores de domínio antes de enviar o pedido para o back-end. Os conectores tenham uma cache de permissões de Kerberos, mas num ambiente ocupado a capacidade de resposta dos controladores de domínio pode afetar o desempenho. Este problema é mais comum para os conectores que executam no Azure, mas comunicam com os controladores de domínio que estão no local. 

Para obter mais informações sobre a otimização de rede, consulte [considerações sobre a topologia de rede ao utilizar o Proxy de aplicações do Azure Active Directory](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Associar domínio

Conectores podem ser executado num computador que não está associado ao domínio. No entanto, se quiser início de sessão único (SSO) para as aplicações que utilizam a autenticação integrada de Windows (IWA), precisa de um computador associado a um domínio. Neste caso, as máquinas do conector tem de estar associadas a um domínio que pode realizar [Kerberos](https://web.mit.edu/kerberos) a delegação restrita em nome de utilizadores para as aplicações publicadas.

Os conectores também podem ser associados a domínios ou florestas que possui uma fidedignidade parcial ou para os controladores de domínio só de leitura.

## <a name="connector-deployments-on-hardened-environments"></a>Implementações de conector em ambientes protegidas

Normalmente, a implementação do conector é simples e não requer nenhuma configuração especial. No entanto, existem algumas condições exclusivas que devem ser consideradas:

* As organizações que limitam o tráfego de saída tem [abrir as portas necessárias](active-directory-application-proxy-enable.md#open-your-ports).
* Poderão ser necessário máquinas compatíveis com FIPS para alterar as respetivas configurações para permitir que os processos de conector gerar e armazenar um certificado.
* As organizações que bloqueie o respetivo ambiente com base nos processos que emitem pedidos de rede tem de certificar-se de que ambos os serviços de conector estão ativados para aceder a todas as portas necessárias e IPs.
* Em alguns casos, os proxies de reencaminhar saídas podem interromper a autenticação de certificado bidirecional e fazer com que a comunicação a falhar.

## <a name="connector-authentication"></a>Autenticação do conector

Para fornecer um serviço seguro, tem de autenticar para o serviço de conectores e o serviço tem de autenticar para o conector. Esta autenticação é efetuada utilizando certificados de cliente e servidor quando os conectores de iniciar a ligação. Desta forma o nome de utilizador e palavra-passe de administrador não são armazenadas na máquina de conector.

Os certificados utilizados são específicos para o serviço de Proxy de aplicações. Estes obter criados durante o registo inicial e são automaticamente renovado pelos conectores cada alguns meses. 

Se um conector não está ligado ao serviço para vários meses, os certificados podem estar desatualizados. Neste caso, desinstalar e reinstalar o conector do registo do acionador. Pode executar os seguintes comandos do PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Os bastidores

Conectores baseiam-se no Proxy de aplicações Web do servidor Windows, pelo que têm maior parte das ferramentas de gestão mesmo incluindo registos de eventos do Windows

 ![Gerir registos de eventos com o Visualizador de eventos](./media/application-proxy-understand-connectors/event-view-window.png)

e os contadores de desempenho do Windows. 

 ![Adicionar contadores para o conector com o Monitor de desempenho](./media/application-proxy-understand-connectors/performance-monitor.png)

Os conectores têm administrador e sessões registos. Os registos de admin incluir eventos de chaves e os erros. Os registos de sessão incluir todas as transações e os respetivos detalhes de processamento. 

Para obter os registos, vá para o Visualizador de eventos, abra o **vista** menu e ativar **Mostrar análise e os registos de depuração**. Em seguida, ativá-los iniciar a recolha de eventos. Estes registos não aparecem no Proxy de aplicações Web no Windows Server 2012 R2, como os conectores são baseados numa versão mais recente.

Pode examinar o estado do serviço na janela de serviços. O conector é composto por dois serviços do Windows: o conector real e do atualizador. Ambos os parâmetros tem de executar sempre.

 ![AzureAD serviços Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Passos seguintes


* [Publicar aplicações em redes separadas e localizações utilizar grupos de conector](active-directory-application-proxy-connectors-azure-portal.md)
* [Trabalhar com servidores de proxy no local existentes](application-proxy-working-with-proxy-servers.md)
* [Resolver erros de Proxy de aplicações e do conector](active-directory-application-proxy-troubleshoot.md)
* [Como instalar silenciosamente o conector de Proxy de aplicações do Azure AD](active-directory-application-proxy-silent-installation.md)

