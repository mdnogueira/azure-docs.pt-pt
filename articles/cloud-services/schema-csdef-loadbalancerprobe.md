---
title: "Def de serviços em nuvem do Azure Esquema de LoadBalancerProbe | Microsoft Docs"
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: "14"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 31c974c5a4b9dc9cff882ff42b73ee023fc4ad9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Definição LoadBalancerProbe esquema de serviços em nuvem do Azure
A sonda do Balanceador de carga é uma sonda do Estado de funcionamento definido de cliente de pontos finais do UDP e os pontos finais em instâncias de função. O `LoadBalancerProbe` não é um elemento de autónomo; for combinada com a função da web ou função de trabalho num ficheiro de definição de serviço. A `LoadBalancerProbe` pode ser utilizado por mais de uma função.

A extensão de predefinido para o ficheiro de definição de serviço é. csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>A função de uma sonda do Balanceador de carga
O Balanceador de carga do Azure é responsável por encaminhamento de tráfego de entrada para as instâncias da função. O Balanceador de carga determina que instâncias podem receber tráfego por regularmente pesquisa cada instância para determinar o estado de funcionamento nessa instância. O Balanceador de carga sondas todas as instâncias várias vezes por minuto. Existem duas opções diferentes para fornecer o estado de funcionamento de instância para o Balanceador de carga – a sonda de Balanceador de carga predefinida, ou um balanceador de carga personalizado sonda, que é implementado por definir a LoadBalancerProbe no ficheiro. csdef.

A sonda do Balanceador de carga predefinido utiliza o agente convidado dentro da máquina virtual, o qual escuta e responde com uma resposta de HTTP 200 OK apenas quando a instância está no estado pronto (por exemplo, quando a instância não está a ser o ocupado, reciclagem, parar, Estados de etc.). Se o agente convidado não conseguir responder com HTTP 200 OK, o Balanceador de carga do Azure marca a instância como responder e deixa de envio de tráfego para essa instância. O Balanceador de carga do Azure continua a executar um ping a instância e, se o agente convidado responde com uma HTTP 200, o Balanceador de carga do Azure envia tráfego a essa instância novamente. Quando utilizar uma função da web que o código de site, normalmente, é executado num w3wp.exe que não é monitorizado pelos recursos de infraestrutura do Azure ou o agente convidado, o que significa falhas no w3wp.exe (ex. Respostas de HTTP 500) não foi reportado para o agente convidado e a carga balanceador não souber a efetuar essa instância fora de rotação.

A sonda do Balanceador de carga personalizado substitui a sonda de agente de convidados predefinida e permite-lhe criar a sua própria lógica personalizada para determinar o estado de funcionamento da instância de função. O Balanceador de carga regularmente sondas que o ponto final (cada 15 segundos, por predefinição) e a instância está a ser considerados nas rotação se responder com uma confirmação de TCP ou HTTP 200 dentro do período de tempo limite (predefinição de segundos 31). Isto pode ser útil para implementar a sua própria lógica para remover as instâncias de rotação de Balanceador de carga, por exemplo um Estado não 200 a devolver se a instância está acima da CPU de 90%. Para funções da web através de w3wp.exe, isto também significa que obtém automática de monitorização do seu site, uma vez que as falhas no seu código de site devolvem um Estado de não 200 para a sonda do Balanceador de carga. Se não definir um LoadBalancerProbe no ficheiro. csdef, em seguida, o comportamento de Balanceador de carga predefinido (conforme descrito anteriormente) é utilizado.

Se utilizar uma sonda do Balanceador de carga personalizada, certifique-se de que a lógica leva em consideração o método RoleEnvironment.OnStop. Quando utilizar a sonda do Balanceador de carga predefinido, a instância é retirada rotação antes OnStop a ser chamado, mas uma sonda do Balanceador de carga personalizado pode continuar a devolver um 200 OK durante o evento OnStop. Se estiver a utilizar o evento OnStop para limpar a cache, pare o serviço ou, caso contrário, efetuar as alterações que podem afetar o comportamento em runtime do seu serviço, terá de garantir que a lógica de sonda do Balanceador de carga personalizado remove a instância de rotação.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Esquema de definição de serviço básico de uma sonda do Balanceador de carga
 Segue-se o formato básico de um ficheiro de definição de serviço que contém uma sonda do Balanceador de carga.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O `LoadBalancerProbes` elemento do ficheiro de definição de serviço inclui os seguintes elementos:

- [Elemento LoadBalancerProbes](#LoadBalancerProbes)
- [Elemento LoadBalancerProbe](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a>Elemento LoadBalancerProbes
O `LoadBalancerProbes` elemento descreve a coleção de pesquisas de Balanceador de carga. Este elemento é o elemento principal de [LoadBalancerProbe elemento](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a>Elemento LoadBalancerProbe
O `LoadBalancerProbe` elemento define a sonda de estado de funcionamento para um modelo. Pode definir vários sondas de Balanceador de carga. 

A tabela seguinte descreve os atributos do `LoadBalancerProbe` elemento:

|Atributo|Tipo|Descrição|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Necessário. O nome da sonda de Balanceador de carga. O nome tem de ser exclusivo.|
| `protocol`          | `string` | Necessário. Especifica o protocolo de ponto final. Os valores possíveis são `http` ou `tcp`. Se `tcp` for especificado, uma confirmação recebida não é necessária para a sonda seja concluída com êxito. Se `http` for especificado, uma resposta 200 OK do URI especificado não é necessária para a sonda seja concluída com êxito.|
| `path`              | `string` | O URI utilizado para pedir o estado de funcionamento da VM. `path`é necessário se `protocol` está definido como `http`. Caso contrário, não é permitida.<br /><br /> Não há nenhum valor predefinido.|
| `port`              | `integer` | Opcional. A porta para comunicar a pesquisa. Isto é opcional para qualquer ponto final, como a mesma porta, em seguida, será utilizada para a sonda. Pode configurar uma porta diferente para os respetivos pesquisar, bem. Intervalo de valores possíveis de 1 a 65535, inclusive.<br /><br /> O valor predefinido é definido pelo ponto final.|
| `intervalInSeconds` | `integer` | Opcional. O intervalo, em segundos, para a frequência de sonda do ponto final do Estado de funcionamento. Normalmente, o intervalo é ligeiramente inferior a meio alocado período de tempo limite (em segundos) que permite que as pesquisas completas dois antes de colocar a instância de rotação.<br /><br /> O valor predefinido é 15, o valor mínimo é 5.|
| `timeoutInSeconds`  | `integer` | Opcional. O período de tempo limite, em segundos, aplicada para a sonda onde sem resposta resultará na parar mais tráfego sejam entregues para o ponto final. Este valor permite pontos finais para ficar fora de rotação mais rápida ou mais lenta do que as horas típicas utilizadas no Azure (que são as predefinições).<br /><br /> O valor predefinido é de 31, o valor mínimo é de 11.|

## <a name="see-also"></a>Veja Também
[Esquema de definição (clássica) serviço em nuvem](schema-csdef-file.md)