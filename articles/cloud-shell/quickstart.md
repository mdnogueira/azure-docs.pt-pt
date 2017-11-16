---
title: "Bash no guia de introdução de Shell de nuvem do Azure | Microsoft Docs"
description: "Início rápido para Bash na Shell de nuvem"
services: 
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
ms.openlocfilehash: 4ed83b28fe5f0f1e9626115390f8697dfb2c8ead
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Início rápido para Bash na Shell de nuvem do Azure

Este documento fornece detalhes sobre como utilizar Bash na Shell de nuvem do Azure no [portal do Azure](https://ms.portal.azure.com/).

> [!NOTE]
> A [PowerShell na Shell de nuvem do Azure](quickstart-powershell.md) início rápido também está disponível.

## <a name="start-cloud-shell"></a>Iniciar a Shell de nuvem
1. Iniciar **nuvem Shell** da parte superior da navegação do portal do Azure <br>
![](media/quickstart/shell-icon.png)
2. Selecione uma subscrição para criar uma conta de armazenamento e partilha de ficheiros do Microsoft Azure
3. Selecione "Armazenamento criar"

> [!TIP]
> São automaticamente autenticado para o Azure CLI 2.0 em cada sesssion.

### <a name="select-the-bash-environment"></a>Selecione o ambiente de Bash
1. Selecione a ambiente de lista pendente do lado esquerdo da janela da shell <br>
![](media/quickstart/env-selector.png)
2. Selecione Bash

### <a name="set-your-subscription"></a>Definir a sua subscrição
1. Subscrições da lista que tem acesso: <br>
`az account list`
2. Defina a sua subscrição preferencial: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> A subscrição irá ser memorizada para futuras sessões utilizando `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos no WestUS com o nome "MyRG": <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie uma VM com Ubuntu no seu novo grupo de recursos. O 2.0 CLI do Azure irá criar chaves SSH e configurar a VM com os mesmos. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> As chaves públicas e privadas utilizadas para autenticar a sua VM são colocadas em `/User/.ssh/id_rsa` e `/User/.ssh/id_rsa.pub` pelo Azure CLI 2.0 por predefinição. A pasta. SSH é persistente na imagem de 5 GB a partilha ficheiros do Azure ligado.

O nome de utilizador nesta VM será o seu nome de utilizador utilizada na Shell de nuvem ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH para a VM com Linux
1. Procure o nome da VM na barra de pesquisa de portal do Azure
2. Clique em "Ligar" e execute:`ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Depois de estabelecer a ligação SSH, deverá ver a linha de comandos de boas-vindas da Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpeza 
Elimine o grupo de recursos e quaisquer recursos dentro da mesma: <br>
Execute `az group delete -n MyRG`

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre ficheiros persistentes para Bash na Shell de nuvem](persisting-shell-storage.md) <br>
[Saiba mais sobre a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre armazenamento de ficheiros do Azure](../storage/files/storage-files-introduction.md) <br>