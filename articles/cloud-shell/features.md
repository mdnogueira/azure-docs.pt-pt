---
title: Bash em funcionalidades de Shell de nuvem do Azure | Microsoft Docs
description: "Descrição geral das funcionalidades de Bash na Shell de nuvem do Azure"
services: Azure
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
ms.date: 11/15/2017
ms.author: juluk
ms.openlocfilehash: 79c3f376c2800e2ce9123c31af6e1aa2aacfcf00
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Funcionalidades e ferramentas para Bash na Shell de nuvem do Azure

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> As funcionalidades e ferramentas no [PowerShell](features-powershell.md) também está disponível.

Bash na Shell de nuvem em execução no `Ubuntu 16.04 LTS`.

## <a name="features"></a>Funcionalidades

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

Bash na Shell de nuvem e em segurança os automaticamente autentica o acesso de conta para o 2.0 CLI do Azure.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH para máquinas virtuais do Linux do Azure

Criar uma VM com Linux a partir do Azure CLI 2.0 pode criar uma chave SSH de predefinido e colocá-lo no seu `$Home` diretório. Colocar SSH chaves nos `$Home` permite direcionar ligações SSH para VMs do Azure com Linux diretamente a partir da Shell de nuvem. As chaves são guardadas na acc_<user>.img numa partilha de ficheiros, utilize as melhores práticas quando utilizar ou partilha de acesso à partilha de ficheiros ou chaves.

### <a name="home-persistence-across-sessions"></a>Persistência $Home entre sessões

Para manter os ficheiros entre sessões, Shell da nuvem explica como anexar uma partilha de ficheiros do Azure na primeira execução.
Depois de concluída, Shell da nuvem ligará automaticamente o armazenamento (montado como `$Home\clouddrive`) para sessões de todas as futuras.
Além disso, no Bash na Shell de nuvem a `$Home` diretório é mantido como um .img na partilha de ficheiros do Azure.
Ficheiros fora do `$Home` e o estado da máquina não são mantidas entre sessões.

[Saiba mais sobre ficheiros persistentes na Bash na Shell de nuvem.](persisting-shell-storage.md)

## <a name="tools"></a>Ferramentas

|Categoria   |Nome   |
|---|---|
|Ferramentas do Linux            |Bash<br> partilhar<br> tmux<br> aprofundar<br>               |
|Ferramentas do Azure            |[CLI do Azure 2.0](https://github.com/Azure/azure-cli) e [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AZCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br> [blobxfer](https://github.com/Azure/blobxfer#blobxfer) |
|Editores de texto           |VIM<br> Nano<br> emacs       |
|Controlo de código fonte         |Git                    |
|Ferramentas de compilação            |Certifique-<br> maven<br> npm<br> PIP         |
|Contentores             |[CLI do docker](https://github.com/docker/cli)/[Docker máquina](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DE DC/SO](https://github.com/dcos/dcos-cli)         |
|Bases de Dados              |Cliente de MySQL<br> Cliente PostgreSql<br> [SQLCMD utilitário](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [MSSQL scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |iPython cliente<br> [Nuvem Foundry CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)|

## <a name="language-support"></a>Suporte de idiomas

|Idioma   |Versão   |
|---|---|
|.NET       |2.0.0       |
|Ir         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (predefinição)|

## <a name="next-steps"></a>Passos seguintes
[Bash no guia de introdução de Shell de nuvem](quickstart.md) <br>
[Saiba mais sobre a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/)