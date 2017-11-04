---
title: "Atualização da aplicação: serialização de dados | Microsoft Docs"
description: "Melhores práticas para a serialização de dados e a forma como afeta a atualizações graduais de aplicação."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: a5f36366-a2ab-4ae3-bb08-bc2f9533bc5a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 6aa3ac7842df4657fca7f6b4264e1c6fe52dc0c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-data-serialization-affects-an-application-upgrade"></a>Como a serialização de dados afeta uma atualização da aplicação
Num [a atualização da aplicação](service-fabric-application-upgrade.md), a atualização é aplicada a um subconjunto de nós, um domínio de atualização a uma hora. Durante este processo, alguns domínios de atualização são na versão mais recente da aplicação e alguns domínios de atualização são na versão anterior da aplicação. Durante a implementação, a nova versão da aplicação tem de ser possível ler a versão anterior dos seus dados e a versão antiga da aplicação tem de ser possível ler a nova versão dos seus dados. Se o formato de dados não é compatível com versões anteriores e reencaminhar, a atualização poderá falhar ou worse, os dados podem ser perdidos ou corrompidos. Este artigo descreve o que constitui o formato de dados e oferece melhores práticas para se certificar de que os dados estão a reencaminhar e com versões anteriores compatível.

## <a name="what-makes-up-your-data-format"></a>O que constitui o formato de dados?
No Azure Service Fabric, os dados que é persistente e replicados provém das classes de c#. Para aplicações que utilizam [coleções fiável](service-fabric-reliable-services-reliable-collections.md), que dados são os objetos na dicionários fiáveis e filas. Para aplicações que utilizam [Reliable Actors](service-fabric-reliable-actors-introduction.md), que é o estado de cópia de segurança para os atores. Estas classes c# tem de ser serializáveis para persistência e replicação. Por conseguinte, o formato dos dados é definido pelos campos e propriedades que estão a serializada, bem como a forma como estas são serializadas. Por exemplo, num `IReliableDictionary<int, MyClass>` os dados são um serializados `int` e um serializados `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Código de alterações que resultam numa alteração de formato de dados
Uma vez que o formato dos dados é determinado por classes de c#, as alterações para as classes que podem causar uma alteração de formato de dados. Deve ter cuidado para garantir que uma atualização sem interrupção pode processar a alteração de formato de dados. Exemplos que podem fazer com que as alterações de formato de dados:

* Adicionar ou remover propriedades ou campos
* Mudar o nome de campos ou propriedades
* Alterar os tipos de campos ou propriedades
* A alteração do nome de classe ou espaço de nomes

### <a name="data-contract-as-the-default-serializer"></a>Contrato de dados como o serializador de predefinido
O serializador é geralmente responsável pela leitura dos dados e anular a serialização para a versão atual, mesmo se os dados estão no mais antigo ou *mais recente* versão. O serializador de predefinição é o [serializador de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx), que tem regras de controlo de versões bem definidos. Coleções fiáveis permitir que o serializador ser substituído, mas Reliable Actors atualmente não compatíveis. O serializador de dados desempenha um papel importante para ativar atualizações graduais. O serializador de contrato de dados é o serializador que recomendamos para aplicações de Service Fabric.

## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Como o formato de dados afeta uma atualização sem interrupção
Durante uma atualização sem interrupção, existem dois cenários principais onde o serializador poderá encontrar mais antigo ou *mais recente* versão dos dados:

1. Depois de um nó é atualizado e inicia a cópia de segurança, o serializador novo irá carregar os dados que foi persistente para disco pela versão antiga.
2. Durante a atualização sem interrupção, o cluster irá conter uma combinação das versões antigas e novas do seu código. Uma vez que as réplicas podem ser colocadas em domínios de atualização diferentes e réplicas enviam dados entre si, a versão nova e/ou antiga dos seus dados possam ser detetada pela versão nova e/ou antigo do seu serializador.

> [!NOTE]
> A "versão novo" e "versão antiga" Consulte aqui a versão do seu código que está em execução. O serializador"novo" refere-se o código de serializador que está a executar a nova versão da aplicação. Os dados"novo" refere-se para a classe do c# serializada da nova versão da aplicação.
> 
> 

Das duas versões do formato de código e os dados têm de ser direta e com versões anteriores compatível. Se não forem compatíveis, a atualização sem interrupção poderá falhar ou os dados poderão perder-se. A atualização sem interrupção pode falhar porque o código ou serializador pode acionar a exceções ou uma falha quando encontra a outra versão. Dados poderão perder-se se, por exemplo, foi adicionada uma nova propriedade mas o serializador antigo elimina-lo durante a desserialização.

Contrato de dados é a solução recomendada para se certificar de que os seus dados são compatíveis. Tem regras de controlo de versões definida especificamente para adicionar, remover e alteração dos campos. Também tem suporte para lidar com campos desconhecidos, hooking para o processo de serialização e a anulação da serialização e lidar com a herança de classe. Para obter mais informações, consulte [através de contrato de dados](https://msdn.microsoft.com/library/ms733127.aspx).

## <a name="next-steps"></a>Passos seguintes
[Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Saiba como utilizar a funcionalidade avançada ao atualizar a sua aplicação ao referir-se para [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [resolução de problemas de atualizações de aplicação ](service-fabric-application-upgrade-troubleshooting.md).

