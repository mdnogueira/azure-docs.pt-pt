---
title: "Implementar a solução de monitorização remota - Azure | Microsoft Docs"
description: "Este tutorial mostra como aprovisionar a solução pré-configurada de monitorização remota do azureiotsuite.com."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: b16990d41b691c13b01d61aa0cde7d14e533e440
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Implementar a solução pré-configurada de monitorização remota

Este tutorial mostra-lhe como aprovisionar a solução pré-configurada de monitorização remota. Implementar a solução de azureiotsuite.com. Também pode implementar a solução utilizando a CLI do AZURE, para saber mais sobre esta opção, consulte [implementar uma solução pré-configurada na linha de comandos](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implementar a solução pré-configurada
> * Iniciar sessão para a solução pré-configurada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição ativa do Azure.

Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Implementar a solução pré-configurada

Antes de implementar a solução pré-configurada à sua subscrição do Azure, tem de escolher algumas opções de configuração:

1. Inicie sessão no [azureiotsuite.com](https://www.azureiotsuite.com) utilizando o Azure credenciais de contas e clique em  **+**  para criar uma solução.

1. Clique em **Selecionar** no mosaico **Monitorização remota**.

1. No **solução de monitorização remota criar** página, introduza um **nome da solução** para solução pré-configurada de monitorização remota.

1. Selecione um **básico** ou **padrão** implementação. Se estiver deplying a solução para saber como funciona ou para executar uma demonstração, escolha o **básico** opção para minimizar os custos.

1. Escolha o **Java** ou **.NET** como o idioma. Todos os micro-serviços estão disponíveis como implementações de Java ou .NET.

1. Reveja o **detalhes de solução** painel para obter mais informações sobre as opções de configuração.

1. Selecione a **Subscrição** e a **Região** que quer utilizar para aprovisionar a solução.

1. Clique em **Criar Solução** para iniciar o processo de aprovisionamento. Este processo ação demora vários minutos para ser executado.

Para informações de resolução de problemas, consulte [o que fazer quando ocorre uma falha de uma implementação](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) no repositório GitHub.

## <a name="sign-in-to-the-preconfigured-solution"></a>Iniciar sessão para a solução pré-configurada

Quando o processo de aprovisionamento estiver concluído, pode iniciar sessão sua solução pré-configurada de monitorização remota.

1. No **aprovisionado soluções** página, escolha a sua nova solução de monitorização remota.

1. Pode ver informações sobre a sua solução de monitorização remota, no painel que aparece. Escolha **dashboard da solução** para ligar à sua solução de monitorização remota.

    > [!NOTE]
    > Pode eliminar a solução de monitorização remota neste painel quando tiver terminado com o mesmo.

1. Apresenta o dashboard de solução de monitorização remota no seu browser.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Configurar a solução pré-configurada
> * Implementar a solução pré-configurada
> * Iniciar sessão para a solução pré-configurada

Agora que implementou a solução de monitorização remota, o passo seguinte consiste em [explorar as funcionalidades do dashboard de solução](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->