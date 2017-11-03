---
title: "Limitações de Shell de nuvem (pré-visualização) do Azure | Microsoft Docs"
description: "Descrição geral das limitações da Shell de nuvem do Azure"
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 92c8e4c205043f6c5c2925d9197270fb720969a3
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações da Shell de nuvem do Azure

Shell de nuvem do Azure tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão de Shell de nuvem é temporária e é reciclado depois da sessão está inativa durante 20 minutos. Shell de nuvem necessita de uma partilha de ficheiros para possível montá-la. Como resultado, a subscrição tem de poder configurar recursos de armazenamento para aceder à Shell de nuvem. Outras considerações incluem:

* Com o armazenamento montado, apenas as modificações dentro de `clouddrive` diretório são mantidas. No Bash, o `$Home` diretório também é persistente.
* Partilhas de ficheiros podem ser montadas apenas a partir do seu [atribuído região](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Na Bash, execute `env` para localizar a sua região definida como `ACC_LOCATION`.
* Ficheiros do Azure suporta armazenamento apenas localmente redundante e contas de armazenamento georredundante.

### <a name="browser-support"></a>Suporte de browsers

Shell de nuvem suporta as versões mais recentes do Microsoft Edge, o Microsoft Internet Explorer, o Google Chrome, o Mozilla Firefox e o Apple Safari. Não é suportado o Safari no modo privado.

### <a name="copy-and-paste"></a>Copiar e colar

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Para um determinado utilizador, apenas uma shell pode estar ativa

Os utilizadores podem apenas iniciar um tipo de shell de cada vez, ou **Bash** ou **PowerShell**. No entanto, pode ter várias instâncias de Bash ou o PowerShell está a executar em simultâneo. Trocar entre Bash ou PowerShell causas Shell da nuvem para reiniciar, que termina a sessão existente.

### <a name="usage-limits"></a>Limites de utilização

Shell de nuvem destina-se em casos de utilização interativo. Como resultado, as sessões não interativa de execução longa são terminadas sem aviso.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="user-permissions"></a>Permissões de utilizador

As permissões estão definidas como regulares utilizadores sem acesso de sudo. Qualquer instalação fora da sua `$Home` não é continuado diretório.
Embora determinados comandos dentro de `clouddrive` diretório, tal como `git clone`, não dispõe de permissões adequadas, o `$Home` diretório tem permissões.

### <a name="editing-bashrc"></a>Editar .bashrc

Tome atenção quando editar .bashrc, se o fizer, pode provocar erros inesperados na Shell de nuvem.

### <a name="bashhistory"></a>.bash_history

O histórico dos comandos bash pode ser inconsistente devido a interrupção de sessão da Shell de nuvem ou sessões em simultâneo.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="slow-startup-time"></a>Tempo de arranque lenta

PowerShell na Shell de nuvem do Azure pode demorar até 60 segundos para inicializar durante a pré-visualização.

### <a name="no-home-directory-persistence"></a>No $Home persistência de diretório

Os dados escritos `$Home` por qualquer aplicação (tais como: git, vim e outros) não sejam mantidas entre sessões do PowerShell. Para obter uma solução, [apresentada aqui](troubleshooting.md#powershell-resolutions).

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas de nuvem de Shell](troubleshooting.md) <br>
[Início rápido para Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
