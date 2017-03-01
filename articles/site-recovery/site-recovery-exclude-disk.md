---
title: "Utilizar o Azure Site Recovery para excluir discos da proteção | Microsoft Docs"
description: "Descreve como e porquê excluir o disco ou discos de VM da replicação para cenários de VMware para o Azure e de Hyper-V para o Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 1/24/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: af0d66d92ca542f415de779fb638db166ba5f26a
ms.openlocfilehash: 5e0527fb0a41d8892c9e22d6d6d2f252972e69d0


---
#<a name="exclude-disk-from-replication"></a>Excluir o disco da replicação
Este artigo descreve como excluir o disco ou discos da replicação para otimizar a largura de banda de replicação consumida ou para otimizar os recursos do lado do destino utilizados por esses discos. A funcionalidade é suportada em cenários de VMware para o Azure e de Hyper-V para o Azure.

##<a name="prerequisites"></a>Pré-requisitos

Por predefinição, todos os discos numa máquina são replicados. Se estiver a replicar do **VMware para o Azure**, para excluir um disco da replicação, o Serviço mobilidade tem de ser instalado manualmente na máquina antes de ativar a replicação.


## <a name="why-exclude-disks-from-replication"></a>Porquê excluir discos da replicação?
Muitas vezes, é necessário excluir discos da replicação porque:

1. As alterações aos dados no disco excluído não são importantes ou não têm de ser replicadas.

2. A não replicação destas alterações permite poupar recursos de armazenamento e de rede.

##<a name="what-are-the-typical-scenarios"></a>Quais são os cenários típicos?
Existem alguns exemplos específicos de alterações a dados que podem ser facilmente identificadas e que são excelentes candidatas para exclusão, como, por exemplo, qualquer escrita de ficheiro de paginação, escritas tempdb do Microsoft SQL Server, etc. Dependendo da carga de trabalho e do subsistema de armazenamento, o ficheiro de paginação pode registar um volume muito elevado de alterações. No entanto, replicar estes dados a partir do site primário para o Azure consumiria recursos de forma intensiva. Deste modo, a replicação de uma VM com um disco virtual único que tenha o SO e o ficheiro de paginação pode ser otimizada ao:

1. Dividir o disco virtual único em dois, um com o SO e outro com o ficheiro de paginação
2. Excluir o disco do ficheiro de paginação da replicação

Do mesmo modo, para o Microsoft SQL Server com tempdb e o ficheiro da base de dados do sistema no mesmo disco, a replicação pode ser otimizada ao:

1. Manter a base de dados do sistema e o tempdb em dois discos diferentes
2. Excluir o disco tempdb da replicação

##<a name="how-to-exclude-disk-from-replication"></a>Como excluir o disco da replicação?

###<a name="vmware-to-azure"></a>VMware para o Azure
Siga o fluxo de trabalho [Enable replication (Ativar a replicação)](site-recovery-vmware-to-azure.md#enable-replication) para proteger uma VM a partir do Azure Site Recovery. No quarto passo de Enable replication, há uma coluna, **DISK TO REPLICATE (DISCO A REPLICAR)**, que pode ser utilizada para excluir o disco da replicação. Por predefinição, estão selecionados todos os discos. Anule a seleção do disco que pretende excluir da replicação e conclua os passos para ativá-la. 

![Ativar a replicação](./media/site-recovery-exclude-disk/v2a-enable-replication-exclude-disk1.png)
    
    
>[!NOTE]
> 
> * Só pode excluir discos que já tenham o Serviço de mobilidade instalado. Tem de instalar manualmente o Serviço de mobilidade, porque este só é instalado através do mecanismo push depois de a replicação ser ativada.
> * Apenas os discos básicos podem ser excluídos da replicação. Não pode excluir SOs ou discos dinâmicos.
> * Após a replicação estar ativada, não pode adicionar ou remover discos para replicação. Se pretende adicionar ou excluir um disco, tem de desativar a proteção da máquina e, em seguida, reativá-la.
> * Se excluir um disco necessário para o funcionamento de uma aplicação, após a ativação pós-falha para o Azure, terá de criá-lo manualmente no Azure, para que a aplicação replicada possa ser executada. Em alternativa, pode integrar a automatização do Azure num plano de recuperação para criar o disco durante a ativação pós-falha da máquina.
> * VM do Windows: os discos que criar manualmente no Azure não poderão realizar a reativação pós-falha. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente na VM do Azure, apenas três discos em que foi realizada a ativação pós-falha realizarão a reativação pós-falha. Não pode incluir discos criados manualmente na reativação pós-falha ou na nova proteção do local para o Azure.
> * VM do Linux: os discos que criar manualmente no Azure realizarão a reativação pós-falha. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente no Azure, os cinco discos realizarão a reativação pós-falha. Não pode excluir da reativação pós-falha os discos criados manualmente.
> 

###<a name="hyper-v-to-azure"></a>Hyper-V para o Azure
Siga o fluxo de trabalho [Enable replication (Ativar a replicação)](site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication) para proteger uma VM a partir do Azure Site Recovery. No quarto passo de Enable replication, há uma coluna, **DISK TO REPLICATE (DISCO A REPLICAR)**, que pode ser utilizada para excluir discos da replicação. Por predefinição, estão selecionados para replicação todos os discos. Anule a seleção do disco que pretende excluir da replicação e conclua os passos para ativá-la. 

![Ativar a replicação](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)
    
>[!NOTE]
> 
> * Apenas os discos básicos podem ser excluídos da replicação. Não pode excluir o disco do SO e não recomendamos excluir discos dinâmicos. O ASR não consegue identificar que disco VHD é básico ou dinâmico no interior da VM do convidado.  Se todos os discos de volume dinâmico dependente não forem excluídos, o disco dinâmico protegido é tratado como um disco com falha na VM de ativação pós-falha e os dados desse disco não estarão acessíveis.    
> * Após a replicação estar ativada, não pode adicionar ou remover discos para replicação. Se pretende adicionar ou excluir um disco, tem de desativar a proteção da VM e, em seguida, reativá-la.
> * Se excluir um disco necessário para o funcionamento de uma aplicação, após a ativação pós-falha do Azure, terá de criá-lo manualmente no Azure, para que a aplicação replicada possa ser executada. Em alternativa, pode integrar a automatização do Azure num plano de recuperação para criar o disco durante a ativação pós-falha da máquina.
> * Os discos que criar manualmente no Azure não realizarão a reativação pós-falha. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente na VM do Azure, apenas três discos em que foi realizada a ativação pós falha realizarão a reativação pós-falha do Azure para o Hyper-V. Não pode incluir discos criados manualmente na reativação pós-falha ou em replicação inversa do Hyper-V para o Azure.
 


##<a name="end-to-end-scenarios-of-exclude-disks"></a>Cenários completos da exclusão de discos
Para melhor compreender a funcionalidade de exclusão de discos, consideremos dois cenários.

1. Disco tempdb do SQL Server
2. Disco Pagefile

###<a name="excluding-the-sql-server-tempdb-disk"></a>Excluir o disco tempdb do SQL Server
Consideremos uma máquina virtual do SQL Server que tem um tempdb que pode ser excluído.

Nome da VM: SalesDB; discos na VM de origem:


**Nome do Disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do SO
DB-Disk1| Disk1 | D:\ | Base de dados do sistema SQL e User Database1
DB-Disk2 (disco excluído da proteção) | Disk2 | E:\ | Ficheiros temporários
DB-Disk3 (disco excluído da proteção) | Disk3 | F:\ | Base de dados tempdb SQL (caminho d pasta (F:\MSSQL\Data\) --> aponte o caminho da pasta antes da ativação pós-falha
DB-Disk4 | Disk4 |G:\ |User Database2

Uma vez que as alterações a dados em dois discos da VM são, por natureza, temporárias, exclua “Disk2” e “Disk3” da replicação quando estiver a proteger a VM SalesDB. O Azure Site Recovery não vai replicar esses discos e, na ativação pós-falha, os mesmos não estarão presentes na VM de ativação pós-falha no Azure

Discos na VM do Azure após a ativação pós-falha:

**N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | ---
DISK0 |    C:\ | Disco do SO
Disk1 |    E:\ | Armazenamento temporário [o Azure adiciona este disco e atribui a primeira letra de unidade disponível]
Disk2 | D:\ | Base de dados do sistema SQL e User Database1
Disk3 | G:\ | User Database2

Uma vez que Disk2 e Disk3 foram excluídos da VM SalesDB, E: é a primeira letra de unidade da lista disponível. O Azure atribui E: ao volume de armazenamento temporário. Em todos os outros discos replicados, a unidade de letra permanece a mesma.

Disk3, que era o disco tempdb do SQL (caminho da pasta de tempdb F:\MSSQL\Data\) e que foi excluído da replicação, não está disponível na VM de ativação pós-falha. Como resultado, o serviço SQL está no estado “parado” e precisa do caminho F:\MSSQL\Data.

Pode criar este caminho de duas formas.

1. Adicionar um disco novo e atribuir o caminho da pasta tempdb ou
2. Utilizar o disco de armazenamento temporário existente para o caminho da pasta tempdb

####<a name="add-a-new-disk"></a>Adicionar um disco novo:

1. Aponte os caminhos de tempdb.mdf e tempdb.ldf do SQL antes da ativação pós-falha.
2. No portal do Azure, adicione um disco novo à VM de ativação pós-falha com um tamanho igual ou superior ao do disco tempdb do SQL de origem (Disk3).
3. Inicie sessão na VM do Azure. Na consola da gestão de discos (diskmgmt.msc), inicialize e formate o disco adicionado recentemente.
4. Atribua a mesma letra de unidade que era utilizada pelo disco tempdb do SQL (F:).
5. Crie a pasta tempdb no volume F: (F:\MSSQL\Data).
6. Inicie o serviço SQL a partir da consola do serviço.

####<a name="use-existing-temporary-storage-disk-for-sql-tempdb-folder-path"></a>Utilizar o disco de armazenamento temporário existente para o caminho da pasta tempdb do SQL:

1. Abra uma consola da linha de comandos
2. Execute o SQL Server no modo de recuperação a partir da consola da linha de comandos

        Net start MSSQLSERVER /f / T3608

3. Execute o sqlcmd seguinte para alterar o caminho de tempdb para o caminho novo

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;        
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO        
        ALTER DATABASE tempdb        
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');        
        GO


4. Pare o serviço Microsoft SQL Server.

        Net stop MSSQLSERVER
5. Inicie o serviço Microsoft SQL Server.

        Net start MSSQLSERVER

Veja a diretriz seguinte do Azure relativa ao disco de armazenamento temporário

* Utilizar SSDs em VMs do Azure para armazenar o ficheiro TempDB do SQL Server e Extensões do Conjunto de Memórias Intermédias
* Performance best practices for SQL Server in Azure Virtual Machines (Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure)

###<a name="failback-from-azure-to-on-premises"></a>Reativação pós-falha (do Azure para o local)
Agora, vamos compreender os discos que vão ser replicados quando realizar a ativação pós-falha do Azure para o VMware ou para o anfitrião do Hyper-V no local. Os discos que criar manualmente no Azure não serão replicados. Por exemplo, se realizar a ativação pós-falha de três discos e criar dois diretamente na VM do Azure, apenas três discos em que foi realizada a ativação pós-falha realizarão a reativação pós-falha. Não pode incluir discos criados manualmente na reativação pós-falha ou na nova proteção do local para o Azure. O disco de armazenamento temporário também não é replicado para o local.

####<a name="failback-to-original-location-recovery-olr"></a>Reativação pós-falha para a Recuperação de localização original (OLR)

No exemplo anterior, a configuração do disco da VM do Azure é:

**N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco** 
--- | --- | --- 
DISK0 | C:\ | Disco do SO
Disk1 |    E:\ | Armazenamento temporário [o Azure adiciona este disco e atribui a primeira letra de unidade disponível]
Disk2 |    D:\ | Base de dados do sistema SQL e User Database1
Disk3 |    G:\ | User Database2


####<a name="vmware-to-azure"></a>VMware para o Azure
Quando a reativação pós-falha é realizada para a localização original, a configuração do disco da VM de reativação pós-falha não tem o disco excluído. Isto significa que os discos que foram excluídos do VMware para o Azure não estarão disponíveis na VM de reativação pós-falha. 

Após a ativação pós-falha planeada do Azure para o VMware no local, os discos na VM do VMWare (Localização original) são:

**N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco** 
--- | --- | --- 
DISK0 | C:\ | Disco do SO
Disk1 |    D:\ | Base de dados do sistema SQL e User Database1
Disk2 |    G:\ | User Database2

####<a name="hyper-v-to-azure"></a>Hyper-V para o Azure
Quando a reativação pós-falha é realizada para a localização original, a configuração do disco da VM de reativação pós-falha permanece igual à configuração do disco da VM original para o Hyper-V. Isto significa que os discos que foram excluídos do site Hyper-V para o Azure estarão disponíveis na VM de reativação pós-falha.

Após a ativação pós-falha planeada do Azure para Hyper-V no local, os discos na VM Hyper-V (Localização original) são:

**Nome do Disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 |    C:\ | Disco do SO
DB-Disk1 | Disk1 | D:\ | Base de dados do sistema SQL e User Database1
BD-Disk2 (disco excluído) | Disk2 | E:\ | Ficheiros temporários
DB-Disk3 (disco excluído) | Disk3 | F:\ | Base de dados tempdb do SQL (caminho da pasta (F:\MSSQL\Data\)
DB-Disk4 | Disk4 | G:\ | User Database2


####<a name="exclude-paging-file-disk"></a>Excluir o disco do ficheiro de paginação

Consideremos uma máquina virtual que tem um disco de ficheiro de paginação que pode ser excluído.
Existem dois casos:

####<a name="case-1-pagefile-is-configured-on-the-d-drive"></a>Caso 1: o ficheiro de paginação está configurado na unidade D:
Configuração do disco:


**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do SO
DB-Disk1 (disco excluído da proteção) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Definições do ficheiro de paginação na VM de Origem:

![Ativar a replicação](./media/site-recovery-exclude-disk/pagefile-on-d-drive-sourceVM.png)
    

Após a ativação pós-falha da VM do VMware para o Azure ou do Hyper-V para o Azure, os discos na VM do Azure são:
**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do SO
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Uma vez que Disk1 (D:) foi excluído, D: é a primeira letra de unidade da lista disponível e o Azure atribui-a ao volume de armazenamento temporário.  Uma vez que D: está disponível na VM do Azure, a definição do ficheiro de paginação da VM permanece igual.

Definições do ficheiro de paginação na VM do Azure:

![Ativar a replicação](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover.png)

####<a name="case-2-pagefile-file-is-configured-on-any-other-driveother-than-d-drive"></a>Caso 2: o ficheiro pagefile está configurado em qualquer outra unidade (que não seja a unidade D:)

Configuração do disco da VM de origem:

**Nome do disco** | **N.º do disco do SO convidado** | **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | Disco do SO
DB-Disk1 (disco excluído da proteção) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Definições de pagefile na VM no Local:

![Ativar a replicação](./media/site-recovery-exclude-disk/pagefile-on-g-drive-sourceVM.png)

Após a ativação pós-falha da VM do VMware/Hyper-V para o Azure, os discos na VM do Azure são:

**Nome do disco**| **N.º do disco do SO convidado**| **Letra da unidade** | **Tipo de dados no disco**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |Disco do SO
DB-Disk1 | Disk1 | D:\ | Armazenamento temporário –> pagefile.sys
DB-Disk2 | Disk2 | E:\ | User data 1
DB-Disk3 | Disk3 | F:\ | User data 2

Uma vez que D: é a primeira letra de unidade da lista disponível, o Azure atribui-a ao volume de armazenamento temporário. Em todos os outros discos replicados, a unidade de letra permanece a mesma. Visto que o disco G: não está disponível, o sistema vai utilizar a unidade C: para o pagefile.

Definições do ficheiro de paginação na VM do Azure:

![Ativar a replicação](./media/site-recovery-exclude-disk/pagefile-on-Azure-vm-after-failover-2.png)

## <a name="next-steps"></a>Passos seguintes
Depois da implementação estar instalada e em execução, [saiba mais](site-recovery-failover.md) sobre os diferentes tipos de ativação pós-falha.



<!--HONumber=Feb17_HO3-->


