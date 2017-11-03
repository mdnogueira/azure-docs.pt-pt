---
title: "Esquema de NetworkConfiguration de serviços em nuvem do Azure | Microsoft Docs"
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: c1b94a9e-46e8-4a18-ac99-343c94b1d4bd
caps.latest.revision: "28"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 2438876e210363e9918e700397d4181990a3983f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Esquema de NetworkConfiguration de configuração de serviços em nuvem do Azure

O `NetworkConfiguration` elemento do ficheiro de configuração do serviço Especifica os valores DNS e de rede Virtual. Estas definições são opcionais para serviços em nuvem.

Pode utilizar os recursos seguintes para obter mais informações sobre redes virtuais e os esquemas associados:

- [Esquema de configuração (clássica) serviço em nuvem](schema-cscfg-file.md)
- [Esquema de definição (clássica) serviço em nuvem](schema-csdef-file.md)
- [Criar uma rede Virtual (clássica)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Elemento NetworkConfiguration
O seguinte exemplo mostra o `NetworkConfiguration` elemento e os respetivos elementos subordinados.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="<site-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

A tabela seguinte descreve os elementos subordinados do `NetworkConfiguration` elemento.

| Elemento       | Descrição |
| ------------- | ----------- |
| AccessControl | Opcional. Especifica regras para aceder aos pontos finais num serviço em nuvem. O nome do controlo de acesso é definido por uma cadeia para `name` atributo. O `AccessControl` elemento contém um ou mais `Rule` elementos. Mais do que um `AccessControl` elemento pode ser definido.|
| Regra | Opcional. Especifica a ação que deverão ser executada para uma sub-rede especificada intervalo de endereços IP. A ordem da regra está definida por um valor de cadeia para o `order` atributo. O inferiores a regra número maior prioridade. Por exemplo, regras podem ser especificadas com números de ordem de 100, 200 e 300. A regra com o número de ordem de 100 tem precedência sobre a regra que tem uma ordem de 200.<br /><br /> A ação para a regra está definida por uma cadeia para o `action` atributo. Os valores possíveis são:<br /><br /> -   `permit`– Especifica que apenas os pacotes do intervalo de sub-rede especificado podem comunicar com o ponto final.<br />-   `deny`– Especifica que o acesso é negado para os pontos finais no intervalo de sub-rede especificada.<br /><br /> O sub-rede intervalo de endereços IP que é afetados pela regra é definidos por uma cadeia para o `remoteSubnet` atributo. A descrição da regra está definida por uma cadeia para o `description` atributo.|
| EndpointAcl | Opcional. Especifica a atribuição de regras de controlo de acesso para um ponto final. O nome da função que contém o ponto final está definido por uma cadeia para o `role` atributo. O nome do ponto final está definido por uma cadeia para o `endpoint` atributo. O nome do conjunto de `AccessControl` estiverem definidas regras que devem ser aplicadas para o ponto final de uma cadeia para o `accessControl` atributo. Mais do que um `EndpointAcl` elementos podem ser definidos.|
| DnsServer | Opcional. Especifica as definições para um servidor DNS. Pode especificar definições para os servidores DNS sem uma rede Virtual. O nome do servidor DNS é definido por uma cadeia para o `name` atributo. O endereço IP do servidor DNS é definido por uma cadeia para o `IPAddress` atributo. O endereço IP tem de ser um endereço IPv4 válido.|
| VirtualNetworkSite | Opcional. Especifica o nome do site de rede Virtual em que pretende implementar o serviço em nuvem. Esta definição não cria um Site de rede Virtual. Referencia um site que foi anteriormente definido no ficheiro de rede para a rede Virtual. Um serviço em nuvem só pode ser um membro de uma rede Virtual. Se não especificar esta definição, o serviço em nuvem não será possível implementar a uma rede Virtual. O nome do site de rede Virtual é definido por uma cadeia para o `name` atributo.|
| InstanceAddress | Opcional. Especifica a associação de uma função a uma sub-rede ou um conjunto de sub-redes na rede Virtual. Quando associa um nome de função para um endereço de instância, pode especificar as sub-redes a que pretende que esta função a associar. O `InstanceAddress` contém um elemento de sub-redes. O nome da função de que está associado à sub-rede ou sub-redes está definido por uma cadeia para o `roleName` atributo.|
| Subrede | Opcional. Especifica a sub-rede que corresponde ao nome de sub-rede no ficheiro de configuração de rede. O nome da sub-rede é definido por uma cadeia para o `name` atributo.|
| ReservedIP | Opcional. Especifica o endereço IP reservado que deve ser associado à implementação. Criar endereço IP reservado tem de utilizar para criar o endereço IP reservado. Cada implementação num serviço em nuvem pode ser associada com um endereço IP reservado. O nome do endereço IP reservado está definido por uma cadeia para o `name` atributo.|

## <a name="see-also"></a>Veja Também
[Esquema de configuração (clássica) serviço em nuvem](schema-cscfg-file.md)