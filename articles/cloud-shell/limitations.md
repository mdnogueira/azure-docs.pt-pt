---
title: "Limitações de Shell de nuvem do Azure | Microsoft Docs"
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
ms.date: 11/06/2017
ms.author: juluk
ms.openlocfilehash: 65a5c40ce0a4d0cfdc0a325476bea6e8ccebe8c6
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações da Shell de nuvem do Azure

Shell de nuvem do Azure tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão de Shell de nuvem é temporária e é reciclado depois da sessão está inativa durante 20 minutos. Shell de nuvem necessita de uma partilha de ficheiros do Azure para possível montá-la. Como resultado, a subscrição tem de poder configurar recursos de armazenamento para aceder à Shell de nuvem. Outras considerações incluem:

* Com o armazenamento montado, apenas as modificações dentro de `clouddrive` diretório são mantidas. No Bash, o `$Home` diretório também é persistente.
* Partilhas de ficheiros do Azure podem ser montadas apenas a partir do seu [atribuído região](persisting-shell-storage.md#mount-a-new-clouddrive).
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

PowerShell na Shell de nuvem do Azure (pré-visualização) pode demorar até 60 segundos para inicializar durante a pré-visualização.

### <a name="no-home-directory-persistence"></a>No $Home persistência de diretório

Os dados escritos `$Home` por qualquer aplicação (tais como: git, vim e outros) não sejam mantidas entre sessões do PowerShell. Para obter uma solução, [apresentada aqui](troubleshooting.md#powershell-resolutions).

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização do ficheiro predefinido quando a criação do disco do Azure:

Utilizar cmdlets do PowerShell, os utilizadores não é podem criar os ficheiros na unidade do Azure. Quando os utilizadores criam novos ficheiros utilizando outras ferramentas, tal como vim ou nano for apresentado, os ficheiros são guardados para a pasta de C:\Users por predefinição. 

### <a name="gui-applications-are-not-supported"></a>Não são suportadas aplicações de GUI

Se o utilizador executa um comando que iria criar uma caixa de diálogo do Windows, tais como `Connect-AzureAD` ou `Login-AzureRMAccount`, um vê uma mensagem de erro, tais como: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas de nuvem de Shell](troubleshooting.md) <br>
[Início Rápido para o Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
