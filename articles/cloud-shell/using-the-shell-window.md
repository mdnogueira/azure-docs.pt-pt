---
title: Utilizando a janela da Shell de nuvem do Azure | Microsoft Docs
description: "Descrição geral de como utilizar a janela da Shell de nuvem do Azure."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 4eb5680c618d78e0722e1eb4a0f551f26b4dc902
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>Utilizando a janela da Shell de nuvem do Azure

Este documento explica como utilizar a janela da Shell de nuvem.

## <a name="swap-between-bash-and-powershell-environments"></a>Comutação entre ambientes Bash e PowerShell
![](media/using-the-shell-window/env-selector.png)

Utilize o Seletor de ambiente na barra de ferramentas Shell da nuvem para trocar entre ambientes Bash e PowerShell.

## <a name="restart-cloud-shell"></a>Reiniciar o Shell de nuvem
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar o Shell de nuvem irá repor o estado da máquina e todos os ficheiros não persistentes pelo seu Azure partilha de ficheiros serão perdida.

* Clique no ícone de reinício na barra de ferramentas Shell da nuvem para repor o estado da máquina.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar & maximize a janela da Shell de nuvem
![](media/using-the-shell-window/minmax.png)
* Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique no ícone de nuvem Shell novamente para mostrar.
* Clique no ícone de maximizar a definir janela para a altura máxima. Para restaurar a janela para o tamanho anterior, clique em restaurar.

## <a name="concurrent-sessions"></a>Sessões simultâneas
Shell de nuvem permite várias sessões em simultâneo em separadores de browser, permitindo a cada sessão existir como um processo de Bash separado.
Se sair com uma sessão, lembre-se de que sair da janela cada sessão como cada processo é executado independentemente embora são executadas no mesmo computador.

## <a name="copy-and-paste"></a>Copiar e colar
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela da Shell de nuvem
* Clique e arraste o limite superior da barra de ferramentas de cópia de segurança ou para baixo para redimensionar a janela da Shell de nuvem.

## <a name="scrolling-text-display"></a>Deslocamento apresentação de texto
* Desloque-se com o rato ou touchpad mover texto terminal.

## <a name="changing-the-text-size"></a>Alterar o tamanho do texto
![](media/using-the-shell-window/text-size.png)
* Clique no ícone de definições no canto superior esquerdo da janela, em seguida, coloque o cursor sobre a opção "Tamanho do texto" e selecione o tamanho do texto pretendido. A seleção irá ser persistente entre sessões.

## <a name="exit-command"></a>Comando de saída
Executar `exit` termina a sessão ativa. Este comportamento ocorre por predefinição, passados 20 minutos, sem interação do.

## <a name="next-steps"></a>Passos seguintes

[Bash no guia de introdução do Cloud Shell](quickstart.md)
[PowerShell no guia de introdução de Shell de nuvem](quickstart-powershell.md)