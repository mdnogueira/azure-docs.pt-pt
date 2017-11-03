---
title: Gateway de dados no local | Microsoft Docs
description: "Um gateway no local é necessário se o servidor de Analysis Services no Azure irá ligar a origens de dados no local."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/30/2017
ms.author: owend
ms.openlocfilehash: 0b11c005ddcf4a3416104e7cef39a7ce97957ba3
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>A ligar a origens de dados no local com o Gateway de dados do Azure no local
O gateway de dados no local atua como uma ponte, fornecer transferência de proteger os dados entre origens de dados no local e os servidores de serviços de análise do Azure na nuvem. Para além de trabalhar com múltiplos servidores do Azure Analysis Services na mesma região, a versão mais recente do gateway é também funciona com Azure Logic Apps, Power BI, aplicações de energia e Flow Microsoft. Pode associar vários serviços na mesma região com um único gateway. 

Obter a configuração com o gateway pela primeira vez é um processo de quatro partes:

- **Transfira e execute a configuração** -este passo instala um serviço de gateway num computador na sua organização. Pode também iniciar sessão no Azure através de uma conta no seu [do inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) do Azure AD. B2B do Azure não são suportadas contas (convidado).

- **Registar o gateway** - neste passo, especifique um nome e a chave do seu gateway de recuperação e selecione uma região, registar o gateway com o serviço de nuvem do Gateway. O recurso do gateway **têm de estar registados na mesma região** como os servidores do Analysis Services. 

- **Crie um recurso de gateway no Azure** -neste passo, cria um recurso de gateway na sua subscrição do Azure.

- **Ligar os servidores para o recurso do gateway** -depois de ter um recurso de gateway na sua subscrição, pode começar a ligar os servidores ao mesmo. Pode ligar vários servidores e outros recursos, fornecidos estão contidos na região.

Para obter os primeiros, consulte o artigo [instalar e configurar o gateway de dados no local](analysis-services-gateway-install.md).

## <a name="how-it-works"></a>Como funciona
O gateway que instala num computador na sua organização é executado como um serviço do Windows, **gateway de dados no local**. Este serviço local está registado no serviço em nuvem Gateway através do Service Bus do Azure. Em seguida, crie um recurso do gateway de serviço em nuvem Gateway para a sua subscrição do Azure. Os servidores do Azure Analysis Services, em seguida, são ligados para o recurso do gateway. Quando precisam de modelos no seu servidor ligar aos seus dados no local origens para consultas ou processamento, um fluxo de dados e de consulta atravessa o recurso de gateway, Service Bus do Azure, o serviço de gateway de dados local no local e as origens de dados. 

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais encriptados para a origem de dados no local. Em seguida, ser enviada para uma fila para o gateway processar.
2. O serviço de nuvem do gateway analisa a consulta e envia o pedido para o [Service Bus do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados no local consulta o Service Bus do Azure para pedidos pendentes.
4. O gateway obtém a consulta, desencripta as credenciais e liga-se às origens de dados com essas credenciais.
5. O gateway envia a consulta para a origem de dados para execução.
6. Os resultados são enviados da origem de dados, de volta para o gateway e, em seguida, para o serviço em nuvem e o seu servidor.

## <a name="windows-service-account"></a>Conta de serviço do Windows
O gateway de dados no local está configurado para utilizar *NT SERVICE\PBIEgwService* para as credenciais de início de sessão de serviço do Windows. Por predefinição, tem o direito de início de sessão como um serviço; no contexto da máquina que estiver a instalar o gateway. Esta credencial não é a mesma conta utilizada para ligar a origens de dados no local ou a sua conta do Azure.  

Se ocorrerem problemas com o seu servidor de proxy devido a autenticação, poderá pretender alterar a conta de serviço do Windows para um utilizador de domínio ou conta de serviço gerida.

## <a name="ports"></a>Portas
O gateway cria uma ligação de saída para o Service Bus do Azure. Comunica nas portas de saída: TCP 443 (predefinição), 5671, 5672, 9350 através de 9354.  O gateway não necessita de porta de entrada.

Recomendamos que lista branca os endereços IP para a região de dados na sua firewall. Pode transferir o [lista IP de centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista é atualizada semanalmente.

> [!NOTE]
> Os endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Por exemplo, 10.0.0.0/24 não significa 10.0.0.0 através de 10.0.0.24. Saiba mais sobre o [notação CIDR](http://whatismyipaddress.com/cidr).
>
>

Seguem-se os nomes de domínio completamente qualificado utilizados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *. powerbi.com |80 |Protocolo HTTP utilizada para transferir o instalador. |
| *. powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *. login.windows.net |443 |HTTPS |
| *. servicebus.windows.net |5671-5672 |(AMQP) do protocolo de colocação de mensagens de avançadas |
| *. servicebus.windows.net |443, 9350-9354 |Serviços de escuta barramento de serviço de reencaminhamento através de TCP (443 é necessária para a aquisição de token de controlo de acesso) |
| *. frontend.clouddatahub.net |443 |HTTPS |
| *. core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *. msftncsi.com |443 |Utilizado para testar a conectividade à internet, se o gateway não está acessível pelo serviço Power BI. |
| *.microsoftonline p.com |443 |Utilizado para autenticação, dependendo da configuração. |

### <a name="force-https"></a>Forçar a comunicação HTTPS com o Service Bus do Azure
Pode forçar o gateway para comunicar com o Service Bus do Azure através de HTTPS em vez do TCP direto; No entanto, fazê-lo, por isso, pode reduzir significativamente desempenho. Pode modificar o *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* ficheiro alterando o valor de `AutoDetect` para `Https`. Este ficheiro está normalmente localizado em *gateway de dados do local com o c:\Programas\Microsoft Files\On*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**Q**: É necessário um gateway para origens de dados na nuvem, tais como SQL Database do Azure? <br/>
**A**: não. É necessário para ligar a origens de dados no local apenas um gateway.

**Q**: ao gateway tem de ser instalado no mesmo computador como origem de dados? <br/>
**A**: não. O gateway tem apenas a capacidade de ligar ao servidor, normalmente na mesma rede.

<a name="why-azure-work-school-account"></a>

**Q**: por que motivo é necessário utilizar uma conta escolar ou profissional para iniciar sessão? <br/>
**A**: pode utilizar apenas um organizacional conta escolar ou profissional quando a instalação do gateway de dados no local. E, que conta tem de estar no mesmo inquilino como a subscrição estão a configurar o recurso de gateway no. A conta de início de sessão é armazenada num inquilino gerida pelo Azure Active Directory (Azure AD). Normalmente, o nome de principal de utilizador (UPN) da sua conta do Azure AD corresponde o endereço de e-mail.

**Q**: onde são as minhas credenciais armazenadas? <br/>
**A**: as credenciais que introduziu para uma origem de dados são encriptadas e armazenadas no serviço de nuvem do Gateway. As credenciais são desencriptadas no gateway de dados no local.

**Q**: existem quaisquer requisitos de largura de banda de rede? <br/>
**A**: recomenda-se a ligação de rede tem boa débito. Cada ambiente é diferente e a quantidade de dados enviados afeta os resultados. Com o ExpressRoute pode ajudar a garantir um nível de débito no local e os datacenters do Azure.
Pode utilizar a aplicação de teste de velocidade do Azure da ferramenta de terceiros para ajudar a avaliar o débito.

**Q**: qual é a latência de execução de consultas para uma origem de dados do gateway? O que é a arquitetura melhor? <br/>
**A**: para reduzir a latência de rede, instalar o gateway tão próximos a origem de dados quanto possível. Se pode instalar o gateway na origem de dados real, este proximidade minimiza a latência introduzida. Considere demasiado dos centros de dados. Por exemplo, se o serviço utiliza o Centro de dados de EUA oeste e tiver o SQL Server alojado numa VM do Azure, a VM do Azure deve estar no EUA oeste demasiado. Este proximidade minimiza a latência e evita os encargos associados à saída na VM do Azure.

**Q**: como são resultados enviados para a nuvem? <br/>
**A**: os resultados são enviados através do Service Bus do Azure.

**Q**: existem quaisquer ligações de entrada para o gateway da nuvem? <br/>
**A**: não. O gateway utiliza ligações de saída para o Service Bus do Azure.

**Q**: E se bloquear o ligações de saída? O que é necessário abrir? <br/>
**A**: consulte as portas e os anfitriões que utiliza o gateway.

**Q**: o que o serviço do Windows real denomina?<br/>
**A**: nos serviços, o gateway denomina-se o serviço de gateway de dados no local.

**Q**: o serviço Windows do gateway pode executar com uma conta do Azure Active Directory? <br/>
**A**: não. O serviço do Windows tem de ter uma conta do Windows válida. Por predefinição, o serviço seja executado com o SID de serviço, NT SERVICE\PBIEgwService.

**Q**: como posso alimentar um gateway? <br/>
**A**: na ordem para aquisições um gateway (executando o programa de configuração/alterar no painel de controlo > programas) tem de ser um proprietário para o recurso de gateway no Azure e ter a chave de recuperação. Os proprietários de recursos de gateway são configuráveis no controlo de acesso.

### <a name="high-availability"></a>Elevada disponibilidade e recuperação após desastre

**Q**: que opções estão disponíveis para recuperação após desastre? <br/>
**A**: pode utilizar a chave de recuperação para restaurar ou mover um gateway. Quando a instalação do gateway, especifique a chave de recuperação.

**Q**: qual é a vantagem da chave de recuperação? <br/>
**A**: A chave de recuperação fornece uma forma para migrar ou recuperar as definições do gateway após desastres.

## <a name="troubleshooting"></a>Resolução de problemas

**Q**: por que motivo não vejo o meu gateway na lista de instâncias de gateway ao tentar criar o recurso de gateway no Azure? <br/>
**A**: Existem duas razões possíveis. Primeiro, é que um recurso já está a ser criado para o gateway atual ou alguma outra subscrição. Para eliminar essa possibilidade, enumeração de recursos do tipo **Gateways de dados no local** do portal. Certifique-se de que seleciona todas as subscrições, ao enumerar todos os recursos. Tenha em atenção que quando o recurso for criado, o gateway não irão aparecer na lista de instâncias de gateway a experiência de portal criar recursos de Gateway. A possibilidade de segundo é que a identidade do Azure AD do utilizador que instalou o gateway é diferente do que o utilizador tiver sessão iniciado no Portal do Azure. Para resolver este problema, inicie sessão no portal com a mesma conta que o utilizador que instalou o gateway.

**Q**: como posso ver quais os consultas que estão a ser enviados para a origem de dados no local? <br/>
**A**: pode ativar o rastreio de consulta, que inclui as consultas que são enviadas. Lembre-se alterar a consulta novamente rastreio para o valor original quando terminar de resolução de problemas. Abandonar o fileparser o rastreio de consulta ativado cria registos de maior.

Também pode ver as ferramentas que tenha a sua origem de dados para consultas de rastreio. Por exemplo, pode utilizar o evento expandido ou gerador de perfis do SQL Server para o SQL Server e do Analysis Services.

**Q**: onde estão os registos do gateway? <br/>
**A**: consulte os registos neste artigo.

### <a name="update"></a>Atualizar para a versão mais recente

Muitos problemas podem superfície quando a versão do gateway fica desatualizada. Como uma boa prática geral, certifique-se de que utiliza a versão mais recente. Se o gateway ainda não atualizado durante um mês ou mais, que considere instalar a versão mais recente do gateway e veja se pode reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: Falha ao adicionar o utilizador ao grupo. (-2147463168 PBIEgwService desempenho registo utilizadores)

Pode obter este erro se tentar instalar o gateway num controlador de domínio, que não é suportado. Certifique-se de que implementar o gateway num computador que não é um controlador de domínio.

## <a name="logs"></a>Registos

Ficheiros de registo são um recurso importante quando a resolução de problemas.

#### <a name="enterprise-gateway-service-logs"></a>Registos do serviço de gateway de empresa

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Registos de configuração

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Registos de eventos

Pode encontrar os registos do Data Management Gateway e PowerBIGateway em **registos de serviços e aplicações**.


## <a name="telemetry"></a>Telemetria
Telemetria pode ser utilizada para monitorização e resolução de problemas. Por predefinição

**Para ativar a telemetria**

1.  Verifique o diretório de cliente do gateway de dados no local no computador. Normalmente, é **gateway de dados de %systemdrive%\Program Files\On local**. Em alternativa, pode abrir uma consola de serviços e verifique o caminho do executável: uma propriedade do serviço de gateway de dados no local.
2.  No ficheiro Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config do directory do cliente. Altere a definição de SendTelemetry como true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Guardar as alterações e reinicie o serviço do Windows: o serviço de gateway de dados no local.




## <a name="next-steps"></a>Passos seguintes
* [Instalar e configurar o gateway de dados no local](analysis-services-gateway-install.md).   
* [Gerir do Analysis Services](analysis-services-manage.md)
* [Obter dados a partir do Azure Analysis Services](analysis-services-connect.md)
