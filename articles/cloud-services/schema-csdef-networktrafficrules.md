---
title: "Def de serviços em nuvem do Azure Esquema de NetworkTrafficRules | Microsoft Docs"
ms.custom: 
ms.date: 04/14/2015
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: "17"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 37ec6c771ad83be680d95e9b5597d8da3cace9ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Definição NetworkTrafficRules esquema de serviços em nuvem do Azure
O `NetworkTrafficRules` nó é um elemento opcional no ficheiro de definição de serviço que especifica a forma como as funções de comunicarem entre si. Limita que funções podem aceder os pontos finais internos da função específica. O `NetworkTrafficRules` não é um elemento de autónomo; for combinada com dois ou mais funções de um ficheiro de definição de serviço.

A extensão de predefinido para o ficheiro de definição de serviço é. csdef.

> [!NOTE]
>  O `NetworkTrafficRules` nó só está disponível a utilizar a versão do SDK do Azure 1.3 ou superior.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego de rede
Segue-se o formato básico de um ficheiro de definição de serviço que contém definições de tráfego de rede.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O `NetworkTrafficRules` nó do ficheiro de definição de serviço inclui estes elementos, descritos detalhadamente nas seções subsequentes deste tópico:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento de destinos](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

[Elemento AllowAllTraffic](#AllowAllTraffic)

[Elemento WhenSource](#WhenSource)

[Elemento de FromRole](#FromRole)

##  <a name="NetworkTrafficRules"></a>Elemento NetworkTrafficRules
O `NetworkTrafficRules` elemento Especifica que funções podem comunicar com o ponto final na outra função. Um serviço pode conter um `NetworkTrafficRules` definição.

##  <a name="OnlyAllowTrafficTo"></a>Elemento OnlyAllowTrafficTo
O `OnlyAllowTrafficTo` elemento descreve uma coleção de pontos finais de destino e as funções que podem comunicar com eles. Pode especificar vários `OnlyAllowTrafficTo` nós.

##  <a name="Destinations"></a>Elemento de destinos
O `Destinations` elemento descreve uma coleção de RoleEndpoints que pode ser comunicou com.

##  <a name="RoleEndpoint"></a>Elemento RoleEndpoint
O `RoleEndpoint` elemento descreve um ponto final numa função para permitirem comunicações com. Pode especificar vários `RoleEndpoint` elementos se existir mais do que um ponto final na função.

| Atributo      | Tipo     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Necessário. O nome do ponto final para permitir o tráfego.|
| `roleName`     | `string` | Necessário. O nome da função da web para permitir a comunicação.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
O `AllowAllTraffic` elemento é uma regra que permite que todas as funções comunicar com os pontos finais definidos no `Destinations` nós.

##  <a name="WhenSource"></a>Elemento WhenSource
O `WhenSource` elemento descreve uma coleção de funções que pode comunicar com os pontos finais definidos no `Destinations` nós.

| Atributo | Tipo     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Necessário. Especifica a regra se aplique ao permitir que as comunicações. O único valor válido é atualmente `AnyRule`.|
  
##  <a name="FromRole"></a>Elemento de FromRole
O `FromRole` elemento Especifica as funções que podem comunicar com os pontos finais definidos no `Destinations` nós. Pode especificar vários `FromRole` elementos se existirem mais de uma função que pode comunicar com os pontos finais.

| Atributo  | Tipo     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Necessário. O nome da função a partir do qual pretende permitir a comunicação.|

## <a name="see-also"></a>Veja Também
[Esquema de definição (clássica) serviço em nuvem](schema-csdef-file.md)