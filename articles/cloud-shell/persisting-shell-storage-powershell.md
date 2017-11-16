---
title: "Manter os ficheiros no PowerShell na Shell de nuvem do Azure (pré-visualização) | Microsoft Docs"
description: "Instruções sobre como o Shell de nuvem do Azure persistir ficheiros."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 66d0e20d670e49cdfe64614d1fc6f5739fde6155
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Como funciona o PowerShell na Shell de nuvem do Azure (pré-visualização)
PowerShell na Shell de nuvem (pré-visualização) persiste ficheiros através do método seguinte: 
* Montar a partilha de ficheiro especificado como `clouddrive` no seu `$Home` diretório para interação direta de partilha de ficheiros.

## <a name="list-cloud-drive-file-shares"></a>Partilhas de ficheiros de unidade na nuvem de lista
O `Get-CloudDrive` comando obtém as informações de partilha de ficheiros atualmente montadas pela unidade de nuvem na Shell de nuvem. <br>
![Get-CloudDrive em execução](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Desmonte a unidade na nuvem
Pode desmontar uma partilha de ficheiros que está montada para nuvem Shell em qualquer altura. Se a partilha de ficheiros tiver sido removida, será solicitado para criar e montar uma nova partilha de ficheiros na próxima sessão.

O `Dismount-CloudDrive` comando unmounts uma partilha de ficheiros a partir da conta de armazenamento atual. Desmontar a unidade de nuvem termina a sessão atual. Será solicitado ao utilizador para criar e montar uma nova partilha de ficheiros durante a sessão seguinte.
![Dismount-CloudDrive em execução](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Passos seguintes
[Início rápido para o PowerShell](quickstart-powershell.md) <br>
[Saiba mais sobre o File storage do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Saiba mais sobre as etiquetas de armazenamento](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>