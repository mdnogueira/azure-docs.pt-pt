---
title: "Comparar imagens personalizadas e as fórmulas da DevTest Labs | Microsoft Docs"
description: "Saiba mais sobre as diferenças entre imagens personalizadas e fórmulas como VM bases para pode decidir que um melhor se adapta às seu ambiente."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: tarcher
ms.openlocfilehash: ff771abc26c08f0adb977c29739d2f5c91924b21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparar imagens personalizadas e as fórmulas da DevTest Labs
Ambos [imagens personalizadas](devtest-lab-create-template.md) e [fórmulas](devtest-lab-manage-formulas.md) podem ser utilizados como bases para [criar novas VMs](devtest-lab-add-vm-with-artifacts.md). No entanto, a chave distinção entre imagens personalizadas e fórmulas é que uma imagem personalizada é simplesmente uma imagem com base num VHD, enquanto uma fórmula é uma imagem com base num VHD *além* pré-configurada definições - por exemplo, o tamanho da VM, rede virtual, sub-rede e os artefactos. Estas definições pré-configuradas são configuradas com valores predefinidos que podem ser substituídos no momento da criação de VM. Este artigo explica algumas das vantagens (profissionais) e desvantagens (contras) para utilizar imagens personalizadas em comparação com as fórmulas.

## <a name="custom-image-pros-and-cons"></a>Os profissionais de imagem personalizada e contras
Imagens personalizadas proporcionam uma forma estática, imutável criar VMs a partir de um ambiente pretendido. 

**Profissionais de TI**

* VM aprovisionamento a partir de uma imagem personalizada é rápido como nada alterado depois da VM é acelerada a partir da imagem. Por outras palavras, existem não existem definições para aplicar a imagem personalizada é apenas uma imagem sem definições. 
* VMs criadas a partir de uma única imagem personalizada são idênticas.

**Contras**

* Se precisar de algum aspeto da imagem personalizada de atualização, a imagem deve ser recriada.  

## <a name="formula-pros-and-cons"></a>Os profissionais de fórmulas e contras
As fórmulas proporcionam uma forma dinâmica para criar as VMs a partir de pretendido/definições de configuração.

**Profissionais de TI**

* As alterações no ambiente podem ser capturadas no momento através de artefactos. Por exemplo, se pretender uma VM instalada com os bits do pipeline a versão mais recentes ou inscrever o código mais recente do seu repositório, pode simplesmente especificou um artefacto que implementa o bits mais recente ou enlists o código mais recente na fórmula juntamente com uma base de destino imagem. Sempre que esta fórmula é utilizada para criar as VMs, o bits/código mais recente são implementados/inscrita para a VM. 
* As fórmulas podem definir as definições predefinidas que imagens personalizadas não é possível fornecer - tais como definições de rede virtual e tamanhos de VM. 
* As definições guardadas numa fórmula são apresentadas como valores predefinidos, mas podem ser modificadas quando é criada a VM. 

**Contras**

* Criar uma VM a partir de uma fórmula pode demorar mais tempo a criar uma VM a partir de uma imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos seguintes
- [DevTest Labs FAQ](devtest-lab-faq.md)