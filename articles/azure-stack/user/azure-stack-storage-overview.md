---
title: "Introdução ao storage do Azure pilha"
description: Saiba mais sobre armazenamento de pilha do Azure
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Introdução ao storage do Azure pilha

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

## <a name="overview"></a>Descrição geral
Armazenamento de pilha do Azure é um conjunto de serviços de armazenamento na nuvem, incluindo os Blobs, tabelas e filas que são consistentes com os serviços de armazenamento do Azure.

## <a name="azure-stack-storage-services"></a>Serviços de pilha de armazenamento do Azure
Armazenamento de pilha do Azure fornece os seguintes três serviços:

* **Armazenamento de Blobs** 

    O blob storage armazena dados de objetos não estruturados. Um blob pode ser qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro multimédia ou o instalador da aplicação.
* **O Table Storage** 

    O Table storage armazena conjuntos de dados estruturados. O Table Storage é um arquivo de dados do atributo chave NoSQL, que permite um rápido desenvolvimento e um acesso rápido às grandes quantidades de dados.
* **Armazenamento de filas** 

    Armazenamento de filas fornece mensagens fiáveis para processamento de fluxo de trabalho e para a comunicação entre os componentes dos serviços em nuvem.

Uma conta de armazenamento de pilha do Azure é uma conta segura que dá-lhe aceder aos serviços pilha do armazenamento do Azure. A conta do Storage fornece o espaço de nomes exclusivo para os seus recursos de armazenamento. O diagrama seguinte mostra as relações entre os recursos de armazenamento de pilha do Azure numa conta do storage:

![Descrição geral de pilha de armazenamento do Azure](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>Armazenamento de blobs

Para que os utilizadores com uma grande quantidade de dados de objetos não estruturados na nuvem, o Blob storage oferece uma solução eficaz e dimensionável. Pode utilizar o Blob Storage para armazenar conteúdos, tais como:

* Documentos
* Dados de rede social, tais como fotografias, vídeos, música e blogues
* Cópias de segurança de ficheiros, computadores, bases de dados e dispositivos
* Imagens e texto para Web Apps
* Dados de configuração para aplicações em nuvem
* Macrodados, tais como registos e outros grandes conjuntos de dados

Cada blob está organizado num contentor. Os contentores também fornecem uma forma útil de atribuir políticas de segurança a grupos de objetos. Uma conta do storage pode conter qualquer número de contentores e um contentor pode conter qualquer número de blobs, até ao limite da conta de armazenamento.

O blob storage oferece três tipos de blobs: 
* **Blobs de blocos** 

    Os blobs de blocos estão otimizados para transmitir em fluxo e armazenar os objetos da nuvem e são uma boa opção para armazenar documentos, ficheiros multimédia, cópias de segurança, etc.
* **Blobs de acréscimo** 

    Os blobs de acréscimo são semelhantes aos blobs de blocos, mas estão otimizados para as operações de acréscimo. Um blob de acréscimo apenas pode ser atualizado adicionando um novo bloco ao final. Os blobs de acréscimo são uma boa opção para cenários como o registo, onde novos dados têm de ser escritos apenas no final do blob.
* **Blobs de páginas** 

    Os blobs de página estão otimizados para representar discos IaaS e suportar aleatório escreve que é até 1 TB de tamanho. Uma máquina virtual do Azure pilha ligado IaaS disco é um VHD armazenado como um blob de página.


### <a name="table-storage"></a>Table Storage
As aplicações modernas exigem frequentemente arquivos de dados com maior escalabilidade e flexibilidade do que as gerações anteriores do software necessário. O Table Storage oferece um armazenamento de elevada disponibilidade e extremamente dimensionável para que a aplicação possa ser automaticamente dimensionada para satisfazer o pedido do utilizador. O Armazenamento de tabelas é um arquivo de chaves/atributos NoSQL da Microsoft – tem um design sem esquemas, o que o diferencia das tradicionais bases de dados relacionais. Com um arquivo de dados sem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O Table Storage é fácil de utilizar para que os programadores possam criar aplicações rapidamente.

O Table Storage é um arquivo de atributo chave, o que significa que cada valor numa tabela é armazenado com um nome de propriedade escrito. O nome da propriedade pode ser utilizado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e os respetivos valores compõem uma entidade. Uma vez que o Table Storage não tem esquemas, duas entidades na mesma tabela podem conter diferentes coleções de propriedades e essas propriedades podem ser de diferentes tipos.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Para as aplicações baseadas na Internet de hoje, as bases de dados NoSQL, tal como uma oferta do Table Storage, oferecem uma alternativa popular às bases de dados relacionais tradicionais.

Uma conta do storage pode conter qualquer número de tabelas e uma tabela pode conter qualquer número de entidades, até ao limite de capacidade da conta de armazenamento.

### <a name="queue-storage"></a>Armazenamento de filas
Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas oferece uma solução de mensagens fiável para uma comunicação assíncrona entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Uma conta do storage pode conter qualquer número de filas e uma fila pode conter qualquer número de mensagens, até ao limite de capacidade da conta de armazenamento. As mensagens individuais podem ter até 64 KB de tamanho.

## <a name="next-steps"></a>Passos seguintes
* [Armazenamento do Azure consistente: diferenças e considerações](azure-stack-acs-differences.md)

* Para saber mais sobre o Storage do Azure, consulte [introdução ao Storage do Microsoft Azure](../../storage/common/storage-introduction.md)

