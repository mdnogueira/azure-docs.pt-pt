---
title: "Serviços em nuvem a opções para a depuração do Azure | Microsoft Docs"
description: "Depuração cloud services do Azure"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 196a39a75f8a3151c277f0b2a7944e6886192171
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Aprender as várias formas para depurar um serviço em nuvem do Azure
Este artigo fornece ligações para as várias formas para depurar um serviço em nuvem do Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>A depuração de um serviço em nuvem do Azure no Visual Studio
Pode poupar tempo e dinheiro através da utilização do Azure computação emulador para depurar o seu serviço em nuvem num computador local. Por depuração localmente um serviço antes de implementá-lo, pode melhorar a fiabilidade e desempenho sem pagar tempo de computação. No entanto, alguns erros podem ocorrer apenas quando executar um serviço em nuvem no Azure. Erros só ocorrerão quando executar um serviço em nuvem no Azure podem ser debugged ao ativar a depuração remota quando publicar o seu serviço e, em seguida, anexar o depurador para uma instância de função. Para obter mais informações, consulte [depurar o seu serviço em nuvem no seu computador local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-azure-diagnostics"></a>Utilizar o diagnóstico do Azure 
Pode utilizar o diagnóstico do Azure para registar informações detalhadas a partir do código em execução dentro de funções, se as funções estão em execução no ambiente de desenvolvimento ou no Azure. Para obter mais informações, consulte [ativar diagnósticos do Azure na Cloud Services do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

## <a name="using-intellitrace"></a>Utilizar o IntelliTrace 
Se estiver a utilizar o Visual Studio Enterprise para escrever funções direcionados .NET Framework 4.5, pode ativar o IntelliTrace no momento em que implementar um serviço em nuvem do Azure a partir do Visual Studio. IntelliTrace fornece um registo que pode utilizar com o Visual Studio para depurar a aplicação, como se estivesse em execução no Azure. Para obter mais informações, consulte [depuração um serviço de nuvem publicada com IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Depuração remota 
Pode ativar a depuração remota no seus serviços em nuvem no momento ao implementar o serviço em nuvem do Visual Studio. Se optar por ativar a depuração remota para uma implementação, os serviços de depuração remotos estão instalados nas máquinas virtuais que são executados cada instância de função. Estes serviços - como `msvsmon.exe` - não afetam o desempenho ou resultar em custos adicionais. Para obter mais informações, consulte [depurar um serviço em nuvem no Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Passos seguintes
- [A depuração de um serviço em nuvem do Azure ou a VM no Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md) -saiba os detalhes sobre como depurar cloud services do Azure.