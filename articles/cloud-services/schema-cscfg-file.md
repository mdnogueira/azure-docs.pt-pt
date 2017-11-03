---
title: "Definição de esquema (ficheiro. cscfg) para serviços em nuvem do Azure | Microsoft Docs"
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: "35"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: bba02688e443d4b5bde89691ca0b74b3597b453a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Esquema de configuração (ficheiro. cscfg) para serviços em nuvem do Azure
O ficheiro de configuração do serviço Especifica o número de instâncias de função para implementar para cada função no serviço, os valores de quaisquer definições de configuração e os thumbprints para todos os certificados associados a uma função. Se o serviço for parte de uma rede Virtual, informações de configuração para a rede tem de ser fornecidas no ficheiro de configuração do serviço, bem como no ficheiro de configuração de rede virtual. A extensão de predefinido para o ficheiro de configuração de serviço é. cscfg.

O modelo de serviço é descrito pelo [esquema de definição de serviço em nuvem (clássica)](schema-csdef-file.md).

Por predefinição, o ficheiro de esquema de configuração de diagnósticos do Azure está instalado para a `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. Substitua `<version>` com a versão instalada do [Azure SDK](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre como configurar funções num serviço, consulte [o que é o modelo de serviço em nuvem](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Esquema de configuração de serviço básico
Segue-se o formato básico do ficheiro de configuração do serviço.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definições de esquema
Os tópicos seguintes descrevem o esquema para o `ServiceConfiguration` elemento:

- [Esquema de função](schema-cscfg-role.md)
- [Esquema de NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Espaço de nomes de configuração de serviço
O espaço de nomes XML para o ficheiro de configuração de serviço é: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a>ServiceConfiguration elemento
O `ServiceConfiguration` elemento é o elemento de nível superior do ficheiro de configuração de serviço.

A tabela seguinte descreve os atributos do `ServiceConfiguration` elemento. Todos os valores de atributos são tipos de cadeia.

| Atributo | Descrição |
| --------- | ----------- |
|ServiceName|Necessário. O nome do serviço de nuvem. O nome especificado aqui tem de corresponder ao nome especificado no ficheiro de definição de serviço.|
|osFamily|Opcional. Especifica o SO convidado que vão ser executadas em instâncias de função no serviço em nuvem. Para obter informações sobre versões de SO convidado suportadas, consulte [versões de SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se não incluir um `osFamily` valor e não tiver definido a `osVersion` é utilizado o atributo para uma versão de SO convidado específico, um valor predefinido de 1.|
|OsVersion|Opcional. Especifica a versão do SO convidado que vão ser executadas em instâncias de função no serviço em nuvem. Para obter mais informações sobre as versões de SO convidado, consulte [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Pode especificar que o SO convidado devem ser atualizados automaticamente para a versão mais recente. Para tal, defina o valor da `osVersion` atributo para `*`. Quando definido como `*`, as instâncias de função são implementadas utilizando a versão mais recente do SO convidado para a família de SO especificada e serão automaticamente atualizadas quando são lançadas novas versões do SO convidado.<br /><br /> Para especificar manualmente uma versão específica, utilize o `Configuration String` da tabela no **futuro, atual e versões de SO convidado transição** secção [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md) .<br /><br /> O valor predefinido para o `osVersion` atributo `*`.|
|schemaVersion|Opcional. Especifica a versão do esquema de configuração do serviço. A versão do esquema permite lado a lado de Visual Studio selecionar as ferramentas SDK corretas a utilizar para a validação de esquema se mais do que uma versão do SDK está instalada. Para obter mais informações sobre a compatibilidade de esquema e a versão, consulte [versões de SO de convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md)|

O ficheiro de configuração do serviço tem de conter um `ServiceConfiguration` elemento. O `ServiceConfiguration` elemento pode incluir qualquer número de `Role` elementos e zero ou 1 `NetworkConfiguration` elementos.