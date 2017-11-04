---
title: Migrar uma base de dados do SQL Server para o SQL Server numa VM | Microsoft Docs
description: "Saiba mais sobre como migrar uma base de dados de utilizador no local para o SQL Server numa máquina virtual do Azure."
services: virtual-machines-windows
documentationcenter: 
author: sabotta
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: carlasab
ms.openlocfilehash: 68767534298783083a441aa295611914d0df9db0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure

Existem vários métodos para migrar uma base de dados de utilizador de SQL Server no local para o SQL Server numa VM do Azure. Este artigo brevemente abordar vários métodos e recomendamos o melhor método para vários cenários.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Quais são os métodos de migração primário?
Os métodos de migração principais são:

* Efetuar cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança para a máquina virtual do Azure
* Efetuar uma cópia de segurança para URL e o restauro na máquina virtual do Azure do URL
* Desanexar e, em seguida, copie os ficheiros de dados e de registo para o armazenamento de Blobs do Azure e, em seguida, anexar ao SQL Server na VM do Azure a partir do URL
* Converter máquina física no local para o Hyper-V VHD, carregar para o Blob storage do Azure e, em seguida, implementar nova VM utilizando carregado VHD
* Unidade de disco rígido incorporadas com o serviço de importação/exportação do Windows
* Se tiver uma implementação de AlwaysOn no local, utilize o [Adicionar Assistente do Azure réplica](../classic/sql-onprem-availability.md) para criar uma réplica no Azure e, em seguida, a ativação pós-falha, os utilizadores a apontar para a instância de base de dados do Azure
* Utilizar o SQL Server [replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) para configurar a instância do servidor SQL do Azure como um subscritor e, em seguida, desative a replicação, os utilizadores a apontar para a instância de base de dados do Azure

> [!TIP]
> Também pode utilizar estas mesmas técnicas de mover bases de dados entre VMs de SQL Server no Azure. Por exemplo, não há nenhuma forma suportada para atualizar uma VM de imagem de galeria do SQL Server a partir de uma versão/edição para outro. Neste caso, deve criar uma nova VM do SQL Server com a nova versão/edição e, em seguida, utilize um das técnicas de migração neste artigo para mover as bases de dados. 

## <a name="choosing-your-migration-method"></a>Escolher o método de migração
Para um desempenho de transferência de dados ideal, migre os ficheiros de base de dados na VM do Azure utilizando um ficheiro de cópia de segurança comprimido.

Para minimizar o período de indisponibilidade durante o processo de migração de base de dados, utilize a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível utilizar os métodos acima, migre manualmente a base de dados. Utilizar este método, será normalmente começa com uma cópia de segurança de base de dados, seguida de uma cópia da cópia de segurança da base de dados no Azure e, em seguida, efetue um restauro de base de dados. Pode também copia os ficheiros de base de dados-se no Azure e, em seguida, anexe-os. Existem vários métodos que pode realizar este processo manual da migração de uma base de dados para uma VM do Azure.

> [!NOTE]
> Quando atualizar para SQL Server 2014 ou SQL Server 2016 a partir de versões anteriores do SQL Server, deve considerar se forem necessárias alterações. Recomendamos que abordar todas as dependências de funcionalidades não suportadas pela nova versão do SQL Server como parte do seu projeto de migração. Para obter mais informações sobre os cenários e edições suportadas, consulte [atualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela seguinte apresenta uma lista de cada um dos métodos primário migração e descreve quando a utilização de cada método é mais adequada.

| Método | Versão da base de dados de origem | Versão da base de dados de destino | Restrição de tamanho de cópia de segurança da base de dados de origem | Notas |
| --- | --- | --- | --- | --- |
| [Efetuar cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança para a máquina virtual do Azure](#backup-and-restore) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Esta é uma técnica muito simple e testada bem para mover as bases de dados entre as máquinas. |
| [Efetuar uma cópia de segurança para URL e o restauro na máquina virtual do Azure do URL](#backup-to-url-and-restore) |CU2 do SQL Server 2012 SP1 ou superior |CU2 do SQL Server 2012 SP1 ou superior |< 12.8 TB para SQL Server 2016, caso contrário, < 1 TB | Este método é apenas outra forma de mover o ficheiro de cópia de segurança para a VM com o storage do Azure. |
| [Desanexar e, em seguida, copie os ficheiros de dados e de registo para o armazenamento de Blobs do Azure e, em seguida, ligue a SQL Server na máquina virtual do Azure do URL](#detach-and-attach-from-url) |SQL Server 2005 ou posterior |SQL Server 2014 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize este método quando pretender [armazenar estes ficheiros utilizando o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexe-os para o SQL Server em execução numa VM do Azure, particularmente com bases de dados muito grande |
| [Converter máquina no local em VHDs de Hyper-V, carregar para o Blob storage do Azure e, em seguida, implementar uma nova máquina virtual utilizando o VHD foi carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Quando utilizar [colocar a sua própria licença do SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), quando migrar uma base de dados que irá executar uma versão mais antiga do SQL Server ou após a migração em conjunto de bases de dados de utilizador e de sistema como parte da migração da base de dados depende de outros bases de dados de utilizador e/ou bases de dados do sistema. |
| [Unidade de disco rígido incorporadas com o serviço de importação/exportação do Windows](#ship-hard-drive) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize o [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) quando o método de cópia manual é demasiado lento, tal como com bases de dados muito grande |
| [Utilize o Assistente de réplica do Azure para adicionar](../classic/sql-onprem-availability.md) |SQL Server 2012 ou superior |SQL Server 2012 ou superior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimiza o tempo de inatividade, utilize-a quando tiver uma implementação do AlwaysOn no local |
| [Utilizar a replicação transacional do SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Utilize quando necessita de forma a minimizar o período de indisponibilidade e não dispuser de uma implementação do AlwaysOn no local |

## <a name="backup-and-restore"></a>Cópia de segurança e restauro
Cópia de segurança da base de dados com compressão, copie a cópia de segurança para a VM e, em seguida, restaure a base de dados. Se o ficheiro de cópia de segurança for superior a 1 TB, tem de o stripe porque o tamanho máximo de um disco da VM é 1 TB. Utilize os seguintes passos gerais para migrar uma base de dados do utilizador ao utilizar este método manual:

1. Efetue uma cópia de segurança completa da base de dados para uma localização no local.
2. Criar ou carregar uma máquina virtual com a versão do SQL Server pretendido.
3. A configuração com base nos seus requisitos de conectividade. Consulte [ligar a uma Máquina Virtual do SQL Server no Azure (Gestor de recursos)](virtual-machines-windows-sql-connect.md).
4. Copie os ficheiros de cópia de segurança para a VM com o ambiente de trabalho remoto, o Explorador do Windows ou o comando de cópia de uma linha de comandos.

## <a name="backup-to-url-and-restore"></a>Cópia de segurança e restauro do URL
Em vez de cópia de segurança num ficheiro local, pode utilizar o [cópia de segurança para URL](https://msdn.microsoft.com/library/dn435916.aspx) e restaure-a partir do URL para a VM. Com o SQL Server 2016, repartidos conjuntos de cópias de segurança são suportados, são recomendados para um desempenho e necessário para exceder os limites de tamanho por BLOBs. Para bases de dados muito grandes, a utilização do [serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) é recomendada.

## <a name="detach-and-attach-from-url"></a>Desligue e anexar a partir do URL
Desligue os ficheiros de registo e base de dados e transfere-os para [Blob storage do Azure](https://msdn.microsoft.com/library/dn385720.aspx). Em seguida, anexe a base de dados a partir do URL na sua VM do Azure. Utilize esta opção se pretender que os ficheiros de base de dados física residam no Blob storage. Isto poderá ser útil para bases de dados muito grandes. Utilize os seguintes passos gerais para migrar uma base de dados do utilizador ao utilizar este método manual:

1. Desligar os ficheiros de base de dados na instância de base de dados no local.
2. Copie os ficheiros de base de dados desanexada para armazenamento de Blobs do Azure utilizando o [utilitário de linha de comandos do AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Ligar os ficheiros de base de dados do URL do Azure a instância do SQL Server na VM do Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter em VM e carregar para URL e implementar como nova VM
Utilize este método para migrar todas as system e o utilizador bases de dados numa instância do SQL Server no local para a máquina virtual do Azure. Utilize os seguintes passos gerais para migrar uma instância completa do SQL Server através deste método manual:

1. Converter máquinas físicas ou virtuais de Hyper-V VHDs utilizando [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. Carregar ficheiros VHD para o Storage do Azure utilizando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implemente uma nova máquina virtual utilizando o VHD foi carregado.

> [!NOTE]
> Para migrar uma aplicação completa, considere a utilização de [do Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Unidade de disco rígido incorporadas
Utilize o [método do serviço de importação/exportação do Windows](../../../storage/common/storage-import-export-service.md) a transferência de grandes quantidades de dados de ficheiro para o Blob storage do Azure em situações em que carregamento através da rede é prohibitively dispendiosas ou não for viável. Com este serviço, enviar uma ou mais unidades de disco rígido que contém os dados para um centro de dados do Azure, onde os dados serão carregados para a sua conta de armazenamento.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a execução do SQL Server em Azure Virtual Machines, consulte [SQL Server em Virtual Machines do Azure descrição-geral](virtual-machines-windows-sql-server-iaas-overview.md).

Para obter instruções sobre como criar uma Máquina Virtual do Azure SQL Server a partir de uma imagem capturada, consulte [sugestões & truques em 'clonagem' máquinas de virtuais do SQL do Azure das imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blogue do engenheiros do CSS SQL Server.

