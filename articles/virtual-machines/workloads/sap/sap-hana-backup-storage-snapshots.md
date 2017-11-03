---
title: "Cópia de segurança de SAP HANA Azure com base nos instantâneos de armazenamento | Microsoft Docs"
description: "Existem duas principais cópia de segurança as possibilidades de SAP HANA em máquinas virtuais do Azure, este artigo abrange a cópia de segurança de SAP HANA com base nos instantâneos de armazenamento"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: f332b8ac091b75a23489ac27f15ad1fd10d24ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Cópia de segurança SAP HANA baseada em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Isto faz parte de uma série de três partes de artigos relacionados numa cópia de segurança de SAP HANA. [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações de introdução, e [SAP HANA Backup do Azure no nível de ficheiro](sap-hana-backup-file-level.md) abrange a opção de cópia de segurança baseada em ficheiros.

Quando utilizar uma funcionalidade de cópia de segurança de VM para um sistema de instância única tudo-em-um demonstração, um deve considerar a fazer uma cópia de segurança VM em vez de gerir cópias de segurança HANA ao nível do SO. É uma alternativa para criar instantâneos do blob do Azure para criar cópias de discos virtuais individuais, que estão anexados a uma máquina virtual, e manter os ficheiros de dados HANA. Mas um ponto de crítico consistência de aplicações quando criar um instantâneo de cópia de segurança ou de disco da VM, enquanto o sistema está a funcionar e em execução. Consulte _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA tem uma funcionalidade que suporta estes tipos de instantâneos de armazenamento.

## <a name="sap-hana-snapshots"></a>Instantâneos de SAP HANA

Não há uma funcionalidade de SAP HANA que suporte um instantâneo de armazenamento. No entanto, a partir de Dezembro de 2016, não há nenhuma restrição para sistemas de contentor único. Configurações de contentor multi-inquilino não suportam este tipo de instantâneo de base de dados (consulte [criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funciona da seguinte forma:

- Preparar para um instantâneo de armazenamento ao iniciar o instantâneo de SAP HANA
- Executar o instantâneo de armazenamento (por exemplo, o instantâneo de Blobs do Azure)
- Confirme o instantâneo de SAP HANA

![Nesta captura de ecrã mostra que um instantâneo de dados SAP HANA pode ser criado através de uma instrução SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Nesta captura de ecrã mostra que um instantâneo de dados SAP HANA pode ser criado através de uma instrução SQL.

![O instantâneo, em seguida, também aparece no catálogo de cópias de segurança no SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

O instantâneo, em seguida, também é apresentado no catálogo de cópias de segurança no Studio do SAP HANA.

![No disco, o instantâneo aparece no diretório de dados SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

No disco, o instantâneo aparece no diretório de dados SAP HANA.

Um tem para se certificar de que a consistência do sistema de ficheiros também é garantida antes de executar o instantâneo de armazenamento enquanto SAP HANA está no modo de preparação de instantâneo. Consulte _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md).

Depois do instantâneo de armazenamento é concluído, é fundamental para confirmar o instantâneo de SAP HANA. Há uma instrução de SQL correspondente para ser executada: INSTANTÂNEO de fecho de dados de cópia de segurança (consulte [dados FECHAR INSTANTÂNEO instrução BACKUP (cópia de segurança e recuperação)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Confirme o instantâneo HANA. Devido a &quot;cópia ao escrever,&quot; SAP HANA poderá necessitar de espaço em disco adicional no instantâneo-preparar modo, e não é possível iniciar novas cópias de segurança até é confirmado o instantâneo de SAP HANA.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Cópia de segurança de HANA VM através do serviço de cópia de segurança do Azure

A partir de Dezembro de 2016, o agente de cópia de segurança do serviço de cópia de segurança do Azure não está disponível para VMs com Linux. Para tornar a utilização de cópia de segurança do Azure no nível do diretório do ficheiro, um seria copiar ficheiros de cópia de segurança de SAP HANA para uma VM do Windows e, em seguida, utilize o agente de cópia de segurança. Caso contrário, apenas uma VM com Linux cópia de segurança completa é possível através do serviço de cópia de segurança do Azure. Consulte [descrição geral das funcionalidades na cópia de segurança do Azure](../../../backup/backup-introduction-to-azure-backup.md) para saber mais.

O serviço de cópia de segurança do Azure oferece uma opção para criar cópias de segurança e restaurar uma VM. Podem encontrar mais informações sobre este serviço e como funciona o artigo [planear a infraestrutura de cópia de segurança de VM no Azure](../../../backup/backup-azure-vms-introduction.md).

Existem duas considerações importantes, de acordo com esse artigo:

_&quot;Para máquinas virtuais do Linux, apenas as cópias de segurança consistente com ficheiros são possíveis, uma vez que o Linux não tem uma plataforma equivalente para VSS.&quot;_

_&quot;Aplicações necessita de implementar os seus próprios &quot;correção segurança&quot; mecanismo nos dados restaurados.&quot;_

Por conseguinte, um tem de certificar-se de que SAP HANA está num estado consistente no disco quando inicia a cópia de segurança. Consulte _instantâneos de SAP HANA_ descrito anteriormente no documento. Mas não existe um potencial problema quando SAP HANA permanece neste modo de preparação do instantâneo. Consulte [criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para obter mais informações.

Esse artigo Estados:

_&quot;Recomenda-se vivamente para confirmar ou abandonar um instantâneo de armazenamento logo que possível, depois de terem sido criadas. Enquanto o instantâneo de armazenamento está a ser preparado ou tiver sido criada, os dados de instantâneos relevantes está fixa. Enquanto os dados de instantâneos relevantes permanecem congelados, podem ainda ser feitas alterações na base de dados. Essas alterações não fará com que os dados de instantâneos relevantes fixas ser alterada. Em vez disso, as alterações são escritas posições na área de dados que são separadas do instantâneo de armazenamento. As alterações também são escritas no registo. No entanto, mais tempo os dados relevantes para o instantâneo são mantidos congelados, mais pode crescer, o volume de dados.&quot;_

Cópia de segurança do Azure encarrega-se de que a consistência do sistema de ficheiros através de extensões de VM do Azure. Estas extensões não estão disponível autónoma e funcionarão apenas em combinação com o serviço de cópia de segurança do Azure. Contudo, ainda é um requisito para gerir um instantâneo de SAP HANA para garantir a consistência da aplicação.

Cópia de segurança do Azure tem duas fases principais:

- Criar instantâneo
- Transferência de dados para o Cofre

Por isso, um foi confirmar o instantâneo de SAP HANA depois de concluída a fase de serviço de cópia de segurança do Azure de um instantâneo. Poderá demorar alguns minutos para ver no portal do Azure.

![Esta ilustração mostra a parte da lista de tarefas de cópia de segurança de um serviço de cópia de segurança do Azure](media/sap-hana-backup-storage-snapshots/image014.png)

Esta ilustração mostra a parte da lista de tarefas de cópia de segurança de um serviço de cópia de segurança do Azure, que foi utilizado para fazer cópias de segurança a HANA VM de teste.

![Para mostrar os detalhes da tarefa, clique na tarefa de cópia de segurança no portal do Azure](media/sap-hana-backup-storage-snapshots/image015.png)

Para mostrar os detalhes da tarefa, clique na tarefa de cópia de segurança no portal do Azure. Aqui, um pode ver duas fases. Poderá demorar alguns minutos até que mostra a fase de instantâneo como concluído. A maioria do tempo é gasto na fase de transferência de dados.

## <a name="hana-vm-backup-automation-via-azure-backup-service"></a>Automatização de cópia de segurança de HANA VM através do serviço de cópia de segurança do Azure

Um foi de confirmar manualmente o instantâneo de SAP HANA assim que a fase de instantâneo de cópia de segurança do Azure é concluída, conforme descrito anteriormente, mas é útil considerar automatização porque um administrador não pode monitorizar a lista de tarefa de cópia de segurança no portal do Azure.

Eis uma explicação de como pode ser conseguido através de cmdlets do Azure PowerShell.

![Um serviço de cópia de segurança do Azure foi criado com o nome hana-cópia de segurança-Cofre](media/sap-hana-backup-storage-snapshots/image016.png)

Um serviço de cópia de segurança do Azure foi criado com o nome &quot;Cofre de cópia de segurança hana.&quot; O comando de PS **Get AzureRmRecoveryServicesVault-nome do Cofre de cópia de segurança hana** obtém o objeto correspondente. Este objeto, em seguida, é utilizado para definir o contexto de cópia de segurança, como mostrado na figura seguinte.

![Um pode verificar a tarefa de cópia de segurança atualmente em curso](media/sap-hana-backup-storage-snapshots/image017.png)

Depois de definir o contexto correto, um pode verificar a tarefa de cópia de segurança atualmente em curso e, em seguida, procure os detalhes da tarefa. A lista de subtarefa mostra se a fase de instantâneo da tarefa de cópia de segurança do Azure já está a ser concluída:

```
$ars = Get-AzureRmRecoveryServicesVault -Name hana-backup-vault
Set-AzureRmRecoveryServicesVaultContext -Vault $ars
$jid = Get-AzureRmRecoveryServicesBackupJob -Status InProgress | select -ExpandProperty jobid
Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
```

![Consultar o valor num ciclo até que transforma para concluído](media/sap-hana-backup-storage-snapshots/image018.png)

Assim que os detalhes da tarefa são armazenados numa variável, é simplesmente PS a sintaxe para obter a entrada de matriz primeiro e obter o valor de estado. Para concluir o script de automatização, consultar o valor num ciclo até que transforma &quot;concluído.&quot;

```
$st = Get-AzureRmRecoveryServicesBackupJobDetails -Jobid $jid | select -ExpandProperty subtasks
$st[0] | select -ExpandProperty status
```

## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Restaurar a chave de licença HANA e VM através do serviço de cópia de segurança do Azure

O serviço de cópia de segurança do Azure foi concebido para criar uma nova VM durante o restauro. Não há nenhum plano agora para fazer uma &quot;direta&quot; restauro de uma VM do Azure existente.

![Esta ilustração mostra a opção de restauro do serviço do Azure no portal do Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Esta ilustração mostra a opção de restauro do serviço do Azure no portal do Azure. Um pode escolher entre a criação de uma VM durante o restauro ou restaurar os discos. Depois de restaurar os discos, é ainda necessário criar uma nova VM em cima-lo. Sempre que uma nova VM é criada no Azure a exclusivo altera o ID de VM (consulte [Accessing e utilizar ID exclusivo de VM do Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Esta ilustração mostra o ID exclusivo da VM do Azure antes e após o restauro através do serviço de cópia de segurança do Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Esta ilustração mostra o ID exclusivo da VM do Azure antes e após o restauro através do serviço de cópia de segurança do Azure. A chave de hardware SAP, que é utilizada para SAP licenciamento, está a utilizar este ID de VM exclusivo. Como consequence, uma nova licença SAP tem de ser instalado após o restauro de uma VM.

Uma nova funcionalidade de cópia de segurança do Azure foi apresentada no modo de pré-visualização durante a criação deste guia de cópia de segurança. Permite que um restauro de nível de ficheiro com base no instantâneo VM que foi executado para a VM cópia de segurança. Isto evita a necessidade de implementar uma nova VM e, por conseguinte, o ID de VM exclusivo permanece igual e não é necessária nenhuma nova chave de licença de SAP HANA. Obter mais documentação sobre esta funcionalidade será fornecida depois foi totalmente testada.

Cópia de segurança do Azure, eventualmente, irá permitir cópia de segurança de discos virtuais do Azure individuais, e os ficheiros e diretórios a partir de dentro da VM. Das principais vantagens da cópia de segurança do Azure é a gestão de todas as cópias de segurança, guardar o cliente de ter para fazê-lo. Se for necessário um restauro, cópia de segurança do Azure irá selecionar a cópia de segurança correta a utilizar.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Cópia de segurança de SAP HANA VM através de instantâneos de disco manual

Em vez de utilizar o serviço de cópia de segurança do Azure, uma conseguiu configurar uma solução de cópia de segurança individuais através da criação de instantâneos do blob do Azure VHDs manualmente através do PowerShell. Consulte [utilizar instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) para obter uma descrição dos passos.

Fornece uma maior flexibilidade mas não resolver os problemas explicados anteriormente neste documento:

- Um ainda tem de se certificar de que o SAP HANA está num estado consistente
- Não é possível substituir o disco do SO, mesmo se a VM é desalocada devido a um erro a indicar que existe uma concessão. Só funciona depois de eliminar a VM, o que poderia levar a um novo ID de VM exclusivo e a necessidade de instalar uma nova licença SAP.

![É possível restaurar os discos de dados de uma VM do Azure](media/sap-hana-backup-storage-snapshots/image021.png)

É possível restaurar os discos de dados de uma VM do Azure, evitando problemas de obter um novo ID exclusivo do VM e, por conseguinte, invalidados a licença do SAP:

- Para o teste, dois discos de dados do Azure foram ligados a uma VM e RAID de software foi definido por cima-los 
- Foi confirmada que SAP HANA estava num estado consistente através da funcionalidade de instantâneos de SAP HANA
- Parar de sistema de ficheiros (consulte _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md))
- Instantâneos do blob foram executados a partir de ambos os discos de dados
- Libertar poder por do sistema de ficheiros
- Confirmação de instantâneo de SAP HANA
- Para restaurar os discos de dados, a VM foi encerrada e os dois discos desligados
- Depois de anular a exposição de discos, foram substituídos com os instantâneos do blob anteriores
- Em seguida, os discos virtuais restaurados foram anexados novamente à VM
- Depois de iniciar a VM, tudo sobre o software RAID trabalhado ajustar e foi definido de volta para o tempo de instantâneos do blob
- HANA foi definido de volta para o instantâneo HANA

Se foi possível encerrar SAP HANA antes dos instantâneos do blob, o procedimento será menos complexo. Nesse caso, um foi ignorar o instantâneo HANA e, se mais nada se passa no sistema, também ignorar o congelar de sistema de ficheiros. Foram adicionada complexidade entra imagem quando é necessário fazer instantâneos enquanto tudo está online. Consulte _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passos seguintes
* [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre começar a utilizar.
* [Cópia de segurança de SAP HANA com base no nível de ficheiro](sap-hana-backup-file-level.md) abrange a opção de cópia de segurança baseada em ficheiros.
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grande), consulte o artigo [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
