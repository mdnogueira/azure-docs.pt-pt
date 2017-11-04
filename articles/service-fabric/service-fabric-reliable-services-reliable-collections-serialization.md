---
title: "Fiável serialização do objeto de coleção no Service Fabric do Azure | Microsoft Docs"
description: "Serialização de objeto de coleções de fiável de recursos de infraestrutura de serviço do Azure"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: c14794b71ce7340d9e90a56d781c712e247ded06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Fiável serialização do objeto de coleção no Azure Service Fabric
As coleções fiável replicam e manter os seus itens para garantir que são duráveis em falhas de máquina e falhas de energia.
Para replicar e para manter os itens, fiável coleções tem de serializá-los.

Fiável coleções obter o serializador adequado para um determinado tipo de Gestor de estado fiável.
Gestor de estado de fiável contém serializers incorporadas e permite serializers personalizados ser registado para um determinado tipo.

## <a name="built-in-serializers"></a>Serializers incorporadas

O Gestor de estado fiável inclui serializador incorporado para alguns tipos comuns, para que podem ser serializados de forma eficiente por predefinição. Para outros tipos, Gestor de estado fiável retrocede para utilizar o [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Serializers incorporadas são mais eficientes, uma vez que já estão habituados não é possível alterar os respetivos tipos e não é necessário incluir informações sobre o tipo, como o respetivo nome de tipo.

Gestor de estado de fiável tem serializador incorporado para tipos seguintes: 
- GUID
- bool
- Bytes
- SByte
- Byte]
- char
- Cadeia
- Decimal
- duplo
- Número de vírgula flutuante
- Int
- UInt
- longa
- ulong
- curto
- ushort

## <a name="custom-serialization"></a>Serialização personalizada

Serializers personalizados são frequentemente utilizadas para melhorar o desempenho ou para encriptar os dados através da transmissão e no disco. Entre outras razões serializers personalizados são geralmente mais eficientes do que o serializador genérico, uma vez que não precisam de informações sobre o tipo de serialização. 

[IReliableStateManager.TryAddStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) é utilizada para registar um serializador personalizado para o tipo especificado T. Este registo deverá ocorrer na construção de StatefulServiceBase para garantir que, antes de inicia a recuperação, todas as coleções fiável têm acesso para o serializador relevante para ler os dados persistentes anteriores.

```C#
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Serializers personalizados são indicados precedência sobre serializers incorporadas. Por exemplo, quando é registado um serializador personalizado para int, é utilizado para serializar números inteiros em vez do serializador incorporado para int.

### <a name="how-to-implement-a-custom-serializer"></a>Como implementar um serializador personalizado

Um serializador personalizado tem de implementar o [IStateSerializer<T> ](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1) interface.

> [!NOTE]
> IStateSerializer<T> inclui uma sobrecarga escrita e leitura que leva uma T adicional chamado valor base. Esta API está para serialização diferencial. Atualmente não está exposta a funcionalidade de serialização diferencial. Por conseguinte, estes dois sobrecargas não são denominadas até serialização diferencial está exposta e ativada.

Segue-se um tipo personalizado de exemplo chamado OrderKey contém quatro propriedades

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Segue-se uma implementação de exemplo do IStateSerializer<OrderKey>.
Tenha em atenção que a leitura e escrita sobrecargas que utilizam no baseValue, chamar os seus respetiva sobrecarga para reencaminhamentos de compatibilidade.

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Upgradability
Num [a atualização da aplicação](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização a uma hora. Durante este processo, alguns domínios de atualização serão na versão mais recente da aplicação e alguns domínios de atualização serão na versão anterior da aplicação. Durante a implementação, a nova versão da aplicação tem de ser possível ler a versão anterior dos seus dados e a versão antiga da aplicação tem de ser possível ler a nova versão dos seus dados. Se o formato de dados não é compatível com versões anteriores e reencaminhar, a atualização poderá falhar ou worse, os dados podem ser perdidos ou corrompidos.

Se estiver a utilizar o serializador incorporado, não dispõe de preocupar com a compatibilidade.
No entanto, se estiver a utilizar um serializador personalizado ou o DataContractSerializer, os dados têm de ser infinitamente efeitos e forwards compatível.
Por outras palavras, cada versão do serializador tem de ser capazes de serializar e anular a serialização qualquer versão do tipo.

Os utilizadores de contrato de dados devem seguir as regras de controlo de versões definida especificamente para adicionar, remover e alteração dos campos. Contrato de dados também tem suporte para lidar com campos desconhecidos, hooking para o processo de serialização e a anulação da serialização e lidar com a herança de classe. Para obter mais informações, consulte [através de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

Utilizadores de serializador personalizado devem cumprir as diretrizes do serializador estão a utilizar para se certificar de que é efeitos e reencaminha compatível.
Uma forma comum de oferecer suporte a todas as versões é adicionar informações de tamanho no início e apenas adicionar propriedades opcionais.
Desta forma pode ler cada versão como muito pode e ir ao longo da parte restante do fluxo de.

## <a name="next-steps"></a>Passos seguintes
  * [Serialização e a atualização](service-fabric-application-upgrade-data-serialization.md)
  * [Referência para programadores para coleções fiável](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.
  * [Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.
  * Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).
  * Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [tópicos avançados](service-fabric-application-upgrade-advanced.md).
  * Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [resolução de problemas de atualizações de aplicação](service-fabric-application-upgrade-troubleshooting.md).
