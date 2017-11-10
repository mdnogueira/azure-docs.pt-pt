---
title: Nome do emissor e chave do emissor nos BizTalk Services | Microsoft Docs
description: Saiba como obter o nome do emissor e chave do emissor para o Service Bus ou controlo de acesso (ACS) nos BizTalk Services. MABS, WABS
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>BizTalk Services: Nome e Chave do Emissor

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

BizTalk Services do Azure utiliza o nome de emissor de barramento de serviço e a chave do emissor e o nome do emissor de controlo de acesso e a chave do emissor. Especificamente:

| Tarefa | O nome do emissor e chave do emissor |
| --- | --- |
| Implementar a aplicação a partir do Visual Studio |Nome do emissor de controlo de acesso e a chave do emissor |
| Configurar o Portal de serviços do BizTalk do Azure |Nome do emissor de controlo de acesso e a chave do emissor |
| Criar reencaminhamentos LOB com os serviços de adaptador de BizTalk no Visual Studio |Nome do emissor de barramento de serviço e a chave do emissor |

Este tópico lista os passos para obter o nome de emissor e chave do emissor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nome do emissor de controlo de acesso e a chave do emissor
O nome do emissor de controlo de acesso e a chave do emissor são utilizados pelo seguinte:

* A aplicação do BizTalk Service do Azure criada no Visual Studio: com êxito a implementar a aplicação do serviço de BizTalk no Visual Studio para o Azure, introduza o nome do emissor de controlo de acesso e a chave do emissor. 
* BizTalk Services do Portal do Azure: Quando cria um BizTalk Service e abra o Portal de serviços do BizTalk, o nome do emissor de controlo de acesso e a chave do emissor são registados automaticamente para as implementações com os mesmos valores de controlo de acesso.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Obter o nome do emissor de controlo de acesso e a chave do emissor

Para utilizar ACS para autenticação e obter os valores de nome de emissor e chave do emissor, os passos globais incluem:

1. Instalar o [cmdlets Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Adicione a sua conta do Azure:`Add-AzureAccount`
3. Devolva o nome da sua subscrição:`get-azuresubscription`
4. Selecione a sua subscrição:`select-azuresubscription <name of your subscription>` 
5. Crie um novo espaço de nomes:`new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`

    Exemplo:`new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. Quando o novo espaço de nomes do ACS é criado (que pode demorar vários minutos), os valores de nome de emissor e chave do emissor estão listados na cadeia de ligação: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Resumindo:  
Nome do emissor = SharedSecretIssuer  
Chave do emissor = SharedSecretKey

Mais no [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx) cmdlet. 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Nome do emissor de barramento de serviço e a chave do emissor
Nome do emissor de barramento de serviço e a chave do emissor são utilizados pelo BizTalk Services do adaptador. No seu projeto dos BizTalk Services no Visual Studio, utilize o adaptador de BizTalk Services para ligar a um sistema de linha de negócio (LOB) no local. Para ligar, pode criar o reencaminhamento de LOB e introduza os detalhes de sistema LOB. Ao fazê-lo, também é introduzir o nome do emissor de barramento de serviço e a chave do emissor.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para obter o nome do emissor de barramento de serviço e a chave do emissor
1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Procurar **Service Bus**e selecione o seu espaço de nomes. 
3. Abra o **políticas de acesso partilhado** propriedades, selecione a política e ver o **cadeia de ligação** para o nome e valores de chave.  

## <a name="next"></a>Seguinte
Tópicos de BizTalk Services do Azure adicionais:

* [Instalar os BizTalk Services do Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Tutoriais: BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [BizTalk Services do Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Veja Também
* [Como: utilizar o serviço de gestão de ACS para configurar as identidades de serviço](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Os BizTalk Services: Programador, básicas, Standard e Premium gráfico de edições](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Os BizTalk Services: aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Serviços BizTalk: Gráfico de Estado de Aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Serviços BizTalk: Cópia de segurança e Restauro](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

