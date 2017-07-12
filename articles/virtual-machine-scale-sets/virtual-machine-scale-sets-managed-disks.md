---
title: "Utilizar os Managed Disks com Conjuntos de Dimensionamento de Máquinas Virtuais do Azure | Microsoft Docs"
description: "Saiba por que e como utilizar discos geridos com conjuntos de dimensionamento de máquinas virtuais"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: f9cc157f25e70a8154614b659fb7e59b7fd06ed1
ms.contentlocale: pt-pt
ms.lasthandoff: 06/01/2017


---
<a id="azure-vm-scale-sets-and-managed-disks" class="xliff"></a>

# Conjuntos de dimensionamento de VMs do Azure e discos geridos

Agora, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure suportam máquinas virtuais com discos geridos. Utilizar discos geridos com conjuntos de dimensionamento apresenta várias vantagens, incluindo:

* Já não precisa de criar previamente e gerir contas de armazenamento para armazenar os discos de SO das VMs do conjunto de dimensionamento.

* Pode anexar discos de dados geridos ao conjunto de dimensionamento.

* Com um disco gerido, um conjunto de dimensionamento pode ter uma capacidade tão elevada como 1000 VMs, se se basear numa imagem de plataforma, ou 100 VMs, se se basear numa imagem personalizada.

<a id="get-started" class="xliff"></a>

## Introdução

Uma forma simples de começar a utilizar os conjuntos de dimensionamento de discos geridos consiste em implementar um conjunto a partir do portal do Azure. Para obter mais informações, consulte [este artigo](./virtual-machine-scale-sets-portal-create.md). Outra forma simples de começar é utilizar a [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) para implementar um conjunto de dimensionamento. O exemplo seguinte mostra como criar um conjunto de dimensionamento baseado em Ubuntu com 10 VMs, cada uma com um disco de dados de 50 GB e 100 GB:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Em alternativa, pode consultar o [Repositório do GitHub de Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates) para aceder a pastas que contenham `vmss` para ver exemplos pré-criados de modelos que implementam conjuntos de dimensionamento. Para saber que modelos já estão a utilizar discos geridos, veja [esta lista](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md).

<a id="api-versions" class="xliff"></a>

## Versões da API

Os conjuntos de escala com discos geridos requerem uma versão de APi Microsoft.Compute de `2016-04-30-preview` ou posterior. Os conjuntos de dimensionamento com discos não geridos continuarão a funcionar como atualmente, mesmo em novas versões de API com suporte para disco gerido. No entanto, os conjuntos de dimensionamento com discos não geridos não obterão os benefícios dos discos geridos, mesmo nestas novas versões de API.

<a id="next-steps" class="xliff"></a>

## Passos seguintes

Para obter mais informações gerais sobre discos geridos, veja [este artigo](../storage/storage-managed-disks-overview.md).

Para ver como converter um modelo do Resource Manager para aprovisionar conjuntos de dimensionamento com discos geridos, veja [este artigo](./virtual-machine-scale-sets-convert-template-to-md.md). As mesmas modificações feitas aos modelos do Resource Manager aplicam-se também à API REST do Azure.

Para saber mais sobre como utilizar discos de dados geridos com conjuntos de dimensionamento, veja [este artigo](./virtual-machine-scale-sets-attached-disks.md).

Para começar a trabalhar com conjuntos de dimensionamento de grande escala, veja [este artigo](./virtual-machine-scale-sets-placement-groups.md).



