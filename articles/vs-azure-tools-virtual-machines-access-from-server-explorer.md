---
title: "Aceder a máquinas virtuais do Azure no Explorador de servidores | Microsoft Docs"
description: "Obter uma descrição geral de como ver criar e gerir máquinas virtuais do Azure (VMs) no Explorador de servidores no Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: eb3afde6-ba90-4308-9ac1-3cc29da4ede0
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/31/2017
ms.author: kraigb
ms.openlocfilehash: 75dcc603327b50718b279f3ce055663ec0bc2596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Aceder a máquinas virtuais do Azure no Explorador de servidores
Ao utilizar o Explorador de servidores no Visual Studio, pode apresentar informações sobre as máquinas virtuais alojadas pelo Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Aceder a máquinas virtuais no Explorador de servidores
Se tiver máquinas virtuais alojadas pelo Azure, pode aceder-os no Explorador de servidores. Tem primeiro de iniciar sessão sua subscrição do Azure para ver os seus serviços móveis. Para iniciar sessão, abra o menu de atalho para o nó do Azure no Explorador de servidores e escolha **ligar ao Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obter informações sobre as máquinas virtuais
1. No Explorador de nuvem, escolha uma máquina virtual e, em seguida, escolha a chave de F4 para mostrar a janela de propriedades.
   
    A tabela seguinte mostra as propriedades que estão disponíveis, mas estão todos os só de leitura. Para alterá-los, utilize o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome DNS |O URL com o endereço Internet da máquina virtual. |
   | Ambiente |Para uma máquina virtual, o valor desta propriedade é sempre de produção. |
   | Nome |O nome da máquina virtual. |
   | Tamanho |O tamanho da máquina virtual, o que reflete a quantidade de memória e espaço em disco que está disponível. Para obter mais informações, consulte [tamanhos de Máquina Virtual](https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs). |
   | Estado |Os valores incluem início, introdução, parar, parado e ao obter o estado. Se for apresentada a obter o estado, o estado atual é desconhecido. Os valores para esta propriedade é diferente dos valores que são utilizados no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). |
   | SubscriptionID |O ID de subscrição para a sua conta do Azure. Pode mostrar estas informações no [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) visualizando as propriedades de uma subscrição. |
2. Escolha um nó de ponto final e, em seguida, ver o **propriedades** janela.
3. A tabela seguinte descreve as propriedades disponíveis de pontos finais, mas são só de leitura. Para adicionar ou editar os pontos finais para uma máquina virtual, utilize o [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome |Um identificador para o ponto final. |
   | Porta privada |A porta para acesso à rede interna para a aplicação. |
   | Protocolo |O protocolo de camada de transporte para este ponto final utilizado pelo, TCP ou UDP. |
   | Porta Pública |A porta que é utilizada para acesso público à sua aplicação. |

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como utilizar funções do Azure no Visual Studio, consulte [utilizando o ambiente de trabalho remoto com as funções do Azure](vs-azure-tools-remote-desktop-roles.md).

