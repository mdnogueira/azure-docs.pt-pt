---
title: "Avançadas tópicos de atualização de aplicação | Microsoft Docs"
description: "Este artigo abrange alguns tópicos avançados relativas à atualização de uma aplicação de Service Fabric."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: e29585ff-e96f-46f4-a07f-6682bbe63281
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar;chackdan
ms.openlocfilehash: 8d3b922f3d50b645ac9db2cc879a319df1262e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Atualização da aplicação de Service Fabric: avançadas tópicos
## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Adicionar ou remover serviços durante uma atualização da aplicação
Se um novo serviço for adicionado a uma aplicação já implementada, e publicada como uma atualização, é adicionado o novo serviço para a aplicação implementada.  Este tipo uma atualização não afeta a qualquer um dos serviços que já faziam parte da aplicação. No entanto, uma instância do serviço que foi adicionado tem de ser iniciada para o novo serviço ser ativos (utilizando o `New-ServiceFabricService` cmdlet).

Serviços também podem ser removidos de uma aplicação como parte de uma atualização. No entanto, todas as instâncias atuais do serviço to-be eliminados tem de ser paradas antes de continuar com a atualização (utilizando o `Remove-ServiceFabricService` cmdlet).

## <a name="manual-upgrade-mode"></a>Modo de atualização manual
> [!NOTE]
> O modo manual não monitorizado deve ser considerado apenas para uma atualização falhada ou suspensa. O modo monitorizado é o modo de atualização recomendado para aplicações de Service Fabric.
>
>

Azure Service Fabric fornece vários modos de atualização para suportar clusters de produção e de desenvolvimento. Opções de implementação escolhidas poderão ser diferentes para os diferentes ambientes.

A atualização da aplicação monitorizada graduais é a atualização mais comum para utilizar no ambiente de produção. Quando a política de atualização for especificada, o Service Fabric garante que a aplicação está em bom estada antes de continua a atualização.

 O administrador da aplicação pode utilizar o graduais aplicação modo de atualização manual a ter controlo total sobre o progresso da atualização através de vários domínios de atualização. Este modo é útil quando não é necessária uma política de avaliação do Estado de funcionamento personalizado ou complexas ou uma atualização não convencionais está a acontecer (por exemplo, a aplicação já está a ser perda de dados).

Por fim, a atualização da aplicação graduais automática é útil para ambientes de teste para fornecer um ciclo de iteração rápida durante o desenvolvimento de serviço ou de desenvolvimento.

## <a name="change-to-manual-upgrade-mode"></a>Mudar para modo de atualização manual
**Manual**– parar a atualização da aplicação, o UD atual e altere o modo de atualização manual não monitorizado. O administrador tem de chamar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar com a atualização ou acionar uma reversão iniciando uma nova atualização. Depois de introduzir a atualização para o modo Manual, esta permanece no modo Manual até que uma nova atualização é iniciada. O **GetApplicationUpgradeProgressAsync** comando devolve recursos de infraestrutura\_aplicação\_ATUALIZAR\_estado\_ROLLING\_reencaminhar\_pendente.

## <a name="upgrade-with-a-diff-package"></a>Atualizar com um pacote de diff
Uma aplicação de Service Fabric pode ser atualizada pelo aprovisionamento com um pacote de aplicação completa, autónomo. Também é possível atualizar uma aplicação utilizando um pacote de diferenciação que contém apenas os ficheiros de aplicação atualizado, o manifesto da aplicação atualizada e os ficheiros de manifesto do serviço.

Um pacote de aplicação completa contém todos os ficheiros necessários para iniciar e executar uma aplicação de Service Fabric. Um pacote de diff contém apenas os ficheiros que foram alterados entre a última aprovisionar e a atualização atual e o manifesto da aplicação completa e o serviço de ficheiros de manifesto. Qualquer referência no manifesto da aplicação ou o manifesto de serviço que não é possível encontrar o esquema de compilação é pesquisou no arquivo de imagens.

Pacotes de aplicação completa são necessários para a primeira instalação de uma aplicação para o cluster. As atualizações subsequentes podem ser um pacote de aplicação completa ou de um pacote de diferenciação.

Ocasiões quando utilizar um pacote de diff seria uma boa opção:

* Um pacote de diff é preferencial quando tiver um pacote de aplicações de grande que faça referência a vários ficheiros de manifesto do serviço e/ou vários pacotes de código, pacotes de configuração ou pacotes de dados.
* Um pacote de diff é preferencial quando tiver um sistema de implementação que gera o esquema de compilação diretamente a partir do seu processo de compilação de aplicações. Neste caso, apesar do código ainda não tiver sido alterada, assemblagens recentemente incorporadas obter uma soma de verificação diferente. Utilizar um pacote de aplicação completa seria necessários atualizar a versão em todos os pacotes de código. Utilizar um pacote de diferenciação, fornece apenas os ficheiros que foram alterados e os ficheiros de manifesto em que a versão foi alterada.

Quando uma aplicação é atualizada com o Visual Studio, o pacote de diff é publicado automaticamente. Para criar um pacote de diff manualmente, o manifesto da aplicação e os manifestos de serviço tem de ser atualizada, mas apenas os pacotes alterados devem ser incluídos no pacote de aplicação final.

Por exemplo, vamos começar com a seguinte aplicação (números de versão fornecidas para facilitar a compreensão):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Agora, vamos supor que pretendia atualizar apenas o pacote do código de service1 utilizando um pacote de diferenciação utilizando o PowerShell. Agora, a sua aplicação atualizada tem a seguinte estrutura de pasta:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Neste caso, atualize o manifesto da aplicação para 2.0.0 e o manifesto de serviço para service1 refletir a atualização de pacote do código. A pasta do seu pacote de aplicação teria a estrutura seguinte:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Passos seguintes
[Atualizar a aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta-o através de uma atualização da aplicação com o Visual Studio.

[Atualizar a sua aplicação através do Powershell](service-fabric-application-upgrade-tutorial-powershell.md) orienta-o através de uma atualização da aplicação através do PowerShell.

Controlar a forma como a aplicação atualiza utilizando [atualizar parâmetros](service-fabric-application-upgrade-parameters.md).

Faça as atualizações de aplicações compatíveis aprender a utilizar [dados serialização](service-fabric-application-upgrade-data-serialization.md).

Resolva problemas comuns nas atualizações de aplicações ao referir-se os passos descritos para [resolução de problemas de atualizações de aplicação](service-fabric-application-upgrade-troubleshooting.md).
