---
title: "Como utilizar o armazenamento do Azure para cópia de segurança do SQL Server e restaurar | Microsoft Docs"
description: "Saiba como efetuar cópias de segurança do SQL Server para o armazenamento do Azure. Explica as vantagens da cópia de segurança das bases de dados do SQL Server para o armazenamento do Azure."
services: virtual-machines-windows
documentationcenter: 
author: MikeRayMSFT
manager: jhubbard
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: d3df6b25fe524c500cf1a1333ac136e8a29d1484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilizar o armazenamento do Azure para cópia de segurança do SQL Server e de restauro
## <a name="overview"></a>Descrição geral
A partir do SQL Server 2012 SP1 CU2, pode escrever agora cópias de segurança do SQL Server diretamente para o serviço de armazenamento de Blobs do Azure. Pode utilizar esta funcionalidade para até a criar e restaurar a partir do serviço Blob do Azure com uma base de dados do SQL Server no local ou uma base de dados do SQL Server numa máquina virtual do Azure. Cópia de segurança para a nuvem oferece vantagens de disponibilidade, ilimitado georreplicação armazenamento fora das instalações e facilidade de migração de dados para e da nuvem. Pode emitir instruções de cópia de segurança ou RESTAURO utilizando Transact-SQL ou SMO.

SQL Server 2016 apresenta novas funcionalidades; Pode utilizar [instantâneo de ficheiro de cópia de segurança](http://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança quase instantânea e restauros incredibly rápidos.

Este tópico explica por que razão poderá optar por utilizar o armazenamento do Azure para cópias de segurança do SQL Server e, em seguida, descreve os componentes envolvidos. Pode utilizar os recursos fornecidos no final do artigo para aceder a instruções e informações adicionais para começar a utilizar este serviço com as cópias de segurança do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Vantagens de utilizar o serviço Blob do Azure para cópias de segurança do SQL Server
Existem alguns desafios que enfrentam quando efetuar cópias de segurança do SQL Server. Estes desafios incluem a gestão de armazenamento, o risco de falha de armazenamento, acesso a armazenamento fora das instalações e a configuração de hardware. Muitas destes desafios são resolvidas utilizando o serviço de arquivo de Blob do Azure para cópias de segurança do SQL Server. Considere as seguintes vantagens:

* **Facilidade de utilização**: armazenar as cópias de segurança em blobs do Azure pode ser um conveniente, flexível e fácil de aceder a opção fora das instalações. Criar armazenamento fora das instalações, para as cópias de segurança do SQL Server podem ser tão fácil como modificar as scripts/tarefas existentes para utilizar o **cópia de segurança para URL** sintaxe. Armazenamento fora das instalações, normalmente, deve ser suficiente na localização de base de dados de produção para impedir que um desastre único que pode afetar as localizações da base de dados fora das instalações e de produção. Ao optar por [georreplicação replicar de blobs do Azure](../../../storage/common/storage-redundancy.md), ter uma camada adicional de proteção em caso de desastre que pode afetar a região de toda.
* **Arquivo de cópia de segurança**: serviço o Blob Storage do Azure oferece uma alternativa melhor para a opção de banda, muitas vezes, utilizados para arquivar as cópias de segurança. Armazenamento em banda pode necessitar transportes física para uma instalação fora das instalações e medidas para proteger o suporte de dados. Armazenar as cópias de segurança no armazenamento de Blobs do Azure fornece uma instantâneas, elevada disponibilidade e a opção de arquivar um durável.
* **Gerido hardware**: não há nenhuma sobrecarga da gestão de hardware com serviços do Azure. Serviços do Azure gerir o hardware e fornecem georreplicação para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: ao ativar a cópia de segurança direta para blobs do Azure, tem acesso ao armazenamento virtualmente ilimitados. Em alternativa, fazer uma cópia até um disco de máquina virtual do Azure tem limites com base no tamanho da máquina. Não há um limite para o número de discos que pode anexar a uma máquina virtual do Azure para cópias de segurança. Este limite é 16 discos para uma instância extra grande e menos instâncias mais pequeno.
* **Disponibilidade de cópia de segurança**: cópias de segurança armazenadas em blobs do Azure estão disponíveis a partir de qualquer lugar e em qualquer altura e podem facilmente ser acedidas para restauros para um servidor de SQL no local ou outro SQL Server em execução numa máquina Virtual do Azure, sem a necessidade de base de dados expor/anular exposição ou transferir e anexar o VHD.
* **Custo**: paga apenas o serviço que é utilizado. Pode ser económico como uma opção de arquivo fora das instalações de cópia de segurança. Consulte o [Calculadora de preços do Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços")e o [preços do Azure artigo](http://go.microsoft.com/fwlink/?LinkId=277059 "preços artigo") para obter mais informações.
* **Instantâneos de armazenamento**: quando os ficheiros de base de dados são armazenados num blob do Azure e estiver a utilizar o SQL Server 2016, pode utilizar [instantâneo de ficheiro de cópia de segurança](http://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança quase instantânea e restauros incredibly rápidos.

Para obter mais detalhes, consulte [cópia de segurança do SQL Server e de restauro com o serviço de armazenamento de Blobs do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

As duas secções seguintes apresentam o serviço de armazenamento de Blobs do Azure, incluindo os componentes necessários do SQL Server. É importante compreender os componentes e os respetivos interação para utilizar a cópia de segurança e restaurar a partir do serviço de armazenamento de Blobs do Azure com êxito.

## <a name="azure-blob-storage-service-components"></a>Componentes do serviço de armazenamento de Blobs do Azure
Os seguintes componentes do Azure são utilizados quando efetuar cópias de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **Conta de armazenamento** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para aceder a um serviço de armazenamento de Blobs do Azure, primeiro de criar uma conta de armazenamento do Azure. Para mais informações sobre o serviço de armazenamento de Blobs do Azure, consulte [como utilizar o serviço de armazenamento de Blobs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contentor** |Um contentor fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de Blobs. Escrever um SQL Server cópia de segurança para um serviço Blob do Azure, tem de ter, pelo menos, o contentor de raiz que criou. |
| **Blob** |Um ficheiro de qualquer tipo e tamanho. Os BLOBs são endereçáveis utilizando o seguinte formato de URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Para mais informações sobre os Blobs de páginas, consulte [compreender blocos e Blobs de páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes de servidor do SQL Server
Os seguintes componentes do SQL Server são utilizados quando a cópia de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **URL** |Um URL especifica um Uniform Resource Identifier (URI) para um único ficheiro de cópia de segurança. O URL é utilizado para fornecer a localização e o nome do ficheiro de cópia de segurança do SQL Server. O URL tem de apontar para um blob real, não apenas um contentor. Se o blob não existe, é criado. Se um blob existente for especificado, cópia de segurança falha, a menos que o > a opção WITH FORMAT está especificada. Segue-se um exemplo de URL tem de especificar o comando de cópia de segurança: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS é recomendado, mas não é necessária. |
| **Credencial** |As informações necessárias para estabelecer ligação e autenticar para o serviço de armazenamento de Blobs do Azure são armazenadas como uma credencial.  Por ordem para o SQL Server escrever as cópias de segurança para um Blob do Azure ou o restauro a partir do mesmo, tem de ser criada uma credencial do SQL Server. Para obter mais informações, consulte [credencial do servidor SQL](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Se optar por copiar e carregar um ficheiro de cópia de segurança para o serviço de armazenamento de Blobs do Azure, tem de utilizar um tipo de blob de página como a opção de armazenamento se estiver a planear utilizar este ficheiro para operações de restauro. RESTAURO a partir de um tipo de blob de bloco irá falhar com um erro.
> 
> 

## <a name="next-steps"></a>Passos seguintes
1. Crie uma conta do Azure se ainda não tiver um. Se estiver a avaliar o Azure, considere o [avaliação gratuita](https://azure.microsoft.com/free/).
2. Em seguida, avance através de um dos seguintes tutoriais guiá-lo através da criação de uma conta de armazenamento e efetuar um restauro.
   
   * **SQL Server 2014**: [Tutorial: serviço de armazenamento de BLOBs de cópia de segurança do SQL Server 2014 e restauro para o Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Tutorial: utilizando o serviço de armazenamento de Blobs do Microsoft Azure com bases de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Reveja a documentação adicional começadas [cópia de segurança do SQL Server e de restauro com o serviço de armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se tiver quaisquer problemas, consulte o tópico [cópia de segurança do SQL Server para o URL de melhores práticas e resolução de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para outro servidor de SQL de cópia de segurança e opções de restauro, consulte [cópia de segurança e restaurar para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

