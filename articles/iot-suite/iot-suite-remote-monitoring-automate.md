---
title: "Detetar problemas de dispositivos na solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial mostra como utilizar as regras e ações para detetar automaticamente problemas baseadas em limiares dispositivos na solução de monitorização remota."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 173ffbdd70313ef5a0d2af2cf1c8996d2395274a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>Detetar problemas através de regras baseadas em limiares

Este tutorial mostra as capacidades do motor de regras de na solução de monitorização remota. Para apresentar estas capacidades, o tutorial utiliza um cenário na aplicação Contoso IoT.

Contoso tem uma regra que gera um alerta crítico quando o pressão comunicadas por um **Chiller** dispositivo excede 250 PSI. Como um operador, pretende identificar **Chiller** inicial de dispositivos que poderão ter sensores problemáticas procurando picos de pressão. Para identificar estes dispositivos, crie uma regra para gerar um aviso quando a pressão excede 150 PSI.

Neste tutorial, ficará a saber como:

>[!div class="checklist"]
> * Ver as regras na sua solução
> * Criar uma nova regra
> * Editar uma regra existente
> * Eliminar uma regra

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, precisa de uma instância implementada da solução de monitorização remota na sua subscrição do Azure.

Se ainda não implementado a solução de monitorização remota ainda, deve efetuar o [implementar a solução pré-configurada de monitorização remota](iot-suite-remote-monitoring-deploy.md) tutorial.

## <a name="view-the-rules-in-your-solution"></a>Ver as regras na sua solução

O **regras e ações** página na solução mostra uma lista de todas as regras de atuais:

![Página de regras e ações](media/iot-suite-remote-monitoring-automate/rulesactions.png)

Para ver apenas as regras que se aplicam ao **Chiller** dispositivos, aplicar um filtro:

![Filtrar a lista de regras](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

Pode ver mais informações sobre uma regra e editá-lo ao selecionar na lista:

![Ver detalhes da regra](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

Para desativar, ativar ou eliminar uma ou mais regras, selecione várias regras na lista:

![Selecione várias regras](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>Criar uma nova regra

Para adicionar uma nova regra que gera um aviso quando a pressão num **Chiller** dispositivo excede 150 PSI, escolha **nova regra**:

![Criar regra](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

Utilize os seguintes valores para criar a regra:

| Definição          | Valor                                 |
| ---------------- | ------------------------------------- |
| Nome             | Aviso de chiller                       |
| Origem           | **Chiller** grupo de dispositivos              |
| Campo de Acionador    | pressão                              |
| Operador de Acionador | Mais do que                          |
| Valor de Acionador    | 150                                   |
| Nível de gravidade   | Aviso                               |
| Texto de eventos de alarme | Pressão chiller excedeu 150 PSI |

Para guardar a nova regra, escolha **aplicar**.

Pode ver quando a regra é acionada no **regras e ações** página ou no **Dashboard** página.

## <a name="edit-an-existing-rule"></a>Editar uma regra existente

Para efetuar uma alteração a uma regra existente, selecione-o na lista de regras. Em seguida, no **detalhe de regra** painel escolha **modo de edição**.

![Editar regra](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>Desativar uma regra

Para desactivar temporariamente uma regra, pode desativá-lo na lista de regras. Escolha a regra para desativar e, em seguida, escolha **desativar**. O **estado** da regra na lista de alterações para indicar a regra está agora desactivado. Pode reativar o uma regra que é desativada anteriormente utilizando o mesmo procedimento.

![Desativar a regra](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

Pode ativar e desativar várias regras ao mesmo tempo, se selecionar múltiplas regras na lista.

## <a name="delete-a-rule"></a>Eliminar uma regra

Para eliminar permanentemente uma regra, escolha a regra na lista de regras e, em seguida, escolha **eliminar**.

Pode eliminar múltiplas regras ao mesmo tempo, se selecionar múltiplas regras na lista.

## <a name="next-steps"></a>Passos seguintes

Este tutorial mostrou, como para:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Ver as regras na sua solução
> * Criar uma nova regra
> * Editar uma regra existente
> * Eliminar uma regra

Agora que aprendeu como detetar problemas através de regras baseadas em limiares, os passos sugeridos são saber como:

* [Gerir e configurar os seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Resolver problemas e resolver problemas de dispositivo](./iot-suite-remote-monitoring-maintain.md).
* [Testar a sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->