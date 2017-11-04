---
title: Decidir quando deve utilizar Blobs do Azure, ficheiros do Azure ou discos do Azure
description: Saiba mais sobre as diferentes formas para armazenar e aceder a dados no Azure para o ajudar a que decidir qual tecnologia para utilizar.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 9e8808a50e86e40af4991a6054a55ef57f744aae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidir quando deve utilizar Blobs do Azure, ficheiros do Azure ou discos do Azure

Microsoft Azure fornece várias funcionalidades no armazenamento do Azure para armazenar e aceder aos seus dados na nuvem. Este artigo abrange os ficheiros do Azure, Blobs e discos e foi concebido para ajudar a escolher entre estas funcionalidades.

## <a name="scenarios"></a>Cenários

A tabela seguinte compara os ficheiros, Blobs e discos e mostra os cenários de exemplo adequadas para cada.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** | Fornece uma interface SMB, bibliotecas de cliente e um [REST interface](/rest/api/storageservices/file-service-rest-api) que permita o acesso a partir de qualquer lugar para ficheiros armazenados. | Pretende "de comparação de precisão e deslocar" uma aplicação na nuvem que já utiliza o sistema de ficheiros nativo APIs para partilhar dados entre ele e outras aplicações em execução no Azure.<br/><br/>Pretende armazenar o desenvolvimento e depuração ferramentas que precisam de ser acedido a partir de várias máquinas virtuais. |
| **Blobs do Azure** | Fornece bibliotecas de cliente e um [REST interface](/rest/api/storageservices/blob-service-rest-api) que permite que os dados não estruturados ser armazenadas e aceder a uma grande escala em blobs de blocos. | Pretende que a aplicação para suportar a transmissão em fluxo e cenários de acesso aleatório.<br/><br/>Pretender ser capaz de aceder a dados de aplicação a partir de qualquer lugar. |
| **Discos do Azure** | Fornece bibliotecas de cliente e um [REST interface](/rest/api/compute/manageddisks/disks/disks-rest-api) que permite que os dados de forma permanente ser armazenadas e acedido a partir de um disco rígido virtual ligado. | Pretende de comparação de precisão e deslocar as aplicações que utilizam o sistema de ficheiros nativo APIs para ler e escrever dados nos discos persistentes.<br/><br/>Pretende armazenar os dados que não são necessários para ser acedido a partir de fora da máquina virtual para que o disco está ligado. |

## <a name="comparison-files-and-blobs"></a>Comparação: Ficheiros e os Blobs

A tabela seguinte compara os ficheiros do Azure com Blobs do Azure.  
  
||||  
|-|-|-|  
|**Atributo**|**Blobs do Azure**|**Ficheiros do Azure**|  
|Opções de durabilidade|LRS, ZRS, GRS (e RA-GRS para maior disponibilidade)|LRS, GRS|  
|Acessibilidade|APIs REST|APIs REST<br /><br /> O SMB 2.1 e o SMB 3.0 (sistema de ficheiros padrão APIs)|  
|Conectividade|APIs REST em todo o mundo|APIs REST - em todo o mundo<br /><br /> O SMB 2.1 – numa região<br /><br /> O SMB 3.0 – em todo o mundo|  
|Pontos Finais|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Diretórios|Espaço de nomes simples|Objetos de diretório verdadeiro|  
|Sensibilidade maiúsculas e minúsculas de nomes|Sensíveis a maiúsculas e minúsculas|Às maiúsculas e minúsculas, mas preservam maiúsculas|  
|Capacidade|Contentores do até 500 TB|Partilhas de ficheiros de 5 TB|  
|Débito|Até 60 MB/s por BLOBs de blocos|Até 60 MB/s por partilha|  
|Tamanho do objeto|Blob de bloco/GB até 200|Até 1 TB/ficheiro|  
|Capacidade cobrada|Com base em bytes escritos|Com base no tamanho de ficheiro|  
|Bibliotecas de cliente|Vários idiomas|Vários idiomas|  
  
## <a name="comparison-files-and-disks"></a>Comparação: Ficheiros e discos

Ficheiros do Azure complementam os discos do Azure. Um disco só é possível ligar ao Azure máquinas virtuais cada vez. Discos são os VHDs-format armazenados em blobs de páginas no armazenamento do Azure e são utilizados pela máquina virtual para armazenar dados duráveis. Partilhas de ficheiros nos ficheiros do Azure podem ser acedidas da mesma forma como o disco local é acedido (utilizando APIs do sistema de ficheiros nativo) e podem ser partilhadas entre várias máquinas virtuais.  
 
A tabela seguinte compara os ficheiros do Azure com discos do Azure.  
 
||||  
|-|-|-|  
|**Atributo**|**Discos do Azure**|**Ficheiros do Azure**|  
|Âmbito|Exclusivas para uma única máquina virtual|Acesso partilhado entre várias máquinas virtuais|  
|Instantâneos e de cópia|Sim|Não|  
|Configuração|Ligado durante o arranque da máquina virtual|Ligado depois da máquina virtual foi iniciada|  
|Autenticação|Incorporado|Configurar com utilização net|  
|Limpeza|Automática|Manual|  
|Acesso através de REST|Não não possível aceder a ficheiros dentro do VHD|Armazenado numa partilha de ficheiros podem ser acedidos|  
|Tamanho máx.|Discos de 4 TB|Ficheiro de 5 TB de partilha de ficheiros e 1 TB dentro da partilha|  
|IOps de 8KB máx.|500 IOps|1000 IOps|  
|Débito|Até 60 MB/s por disco|Até 60 MB/s por partilha de ficheiros|  

## <a name="next-steps"></a>Passos seguintes

Ao tomar decisões sobre a forma como os dados são armazenados e aceder, deve também considerar os custos envolvidos. Para obter mais informações, consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Algumas funcionalidades do SMB não são aplicáveis para a nuvem. Para obter mais informações, consulte [funcionalidades não são suportadas pelo serviço de ficheiros do Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Para obter mais informações acerca dos discos, consulte [gestão de discos e imagens](../../virtual-machines/windows/about-disks-and-vhds.md) e [como anexar um disco de dados para uma Máquina Virtual do Windows](../../virtual-machines/windows/classic/attach-disk.md).
