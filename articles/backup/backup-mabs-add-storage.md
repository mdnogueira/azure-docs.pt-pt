---
title: "Utilizar o armazenamento de cópia de segurança moderno com o servidor de cópia de segurança do Azure v2 | Microsoft Docs"
description: "Saiba mais sobre as novas funcionalidades no servidor de cópia de segurança do Azure v2. Este artigo descreve como atualizar a instalação do servidor de cópia de segurança."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Adicionar armazenamento a v2 do servidor de cópia de segurança do Azure

V2 de servidor do Backup do Azure é fornecido com o System Center 2016 proteção Manager moderna cópia de segurança do armazenamento de dados. Armazenamento de cópia de segurança moderna oferece poupança de armazenamento de 50 por cento, cópias de segurança que são armazenamento três vezes, mais rápido e eficiente. Também proporciona armazenamento com suporte para a carga de trabalho. 

> [!NOTE]
> Para utilizar o armazenamento de cópia de segurança moderna, tem de executar v2 do servidor de cópia de segurança no Windows Server 2016. Se executar o servidor de cópia de segurança v2 numa versão anterior do Windows Server, servidor de cópia de segurança do Azure não é possível tirar partido do armazenamento de cópia de segurança moderna. Em vez disso, o que protege cargas de trabalho como acontece com o servidor de cópia de segurança v1. Para obter mais informações, consulte a versão do servidor de cópia de segurança [matriz proteção](backup-mabs-protection-matrix.md).

## <a name="volumes-in-backup-server-v2"></a>Volumes no servidor de cópia de segurança v2

Cópia de segurança servidor v2 aceita volumes de armazenamento. Quando adiciona um volume, o servidor de cópia de segurança formata o volume para o ficheiro de sistema Resiliente (ReFS), que necessita de armazenamento de cópia de segurança moderna. Para adicionar um volume e expandi-lo mais tarde se for necessário, sugerimos que utilize este fluxo de trabalho:

1.  Configure o servidor de cópia de segurança v2 numa VM.
2.  Crie um volume num disco virtual num agrupamento de armazenamento:
    1.  Adicionar um disco ao agrupamento de armazenamento e criar um disco virtual com o esquema simples.
    2.  Adicione quaisquer discos adicionais e expandir o disco virtual.
    3.  Crie volumes no disco virtual.
3.  Adicione os volumes ao servidor de cópia de segurança.
4.  Configure o armazenamento com suporte para a carga de trabalho.

## <a name="create-a-volume-for-modern-backup-storage"></a>Criar um volume de armazenamento de cópia de segurança moderna

Com o servidor de cópia de segurança v2 volumes como armazenamento de disco pode ajudar a manter o controlo sobre o armazenamento. Um volume pode ser um único disco. No entanto, se pretender expandir o armazenamento no futuro, crie um volume fora de um disco que criou, utilizando os espaços de armazenamento. Isto pode ajudar a se pretender expandir o volume de armazenamento de cópia de segurança. Esta secção oferece melhores práticas para criar um volume com esta configuração.

1. No Gestor de servidor, selecione **File and Storage Services** > **Volumes** > **agrupamentos de armazenamento**. Em **discos físicos**, selecione **novo agrupamento de armazenamento**. 

    ![Criar um novo agrupamento de armazenamento](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. No **tarefas** caixa pendente, selecione **novo disco Virtual**.

    ![Adicionar um disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Selecione o agrupamento de armazenamento e, em seguida, selecione **adicionar disco físico**.

    ![Adicionar um disco físico](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Selecione o disco físico e, em seguida, selecione **expandir disco Virtual**.

    ![Expandir o disco virtual](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Selecione o disco virtual e, em seguida, selecione **Novo Volume**.

    ![Crie um novo volume](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. No **selecionar o servidor e disco** caixa de diálogo, selecione o servidor e o novo disco. Em seguida, selecione **seguinte**.

    ![Selecione o servidor e disco](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Adicionar a volumes ao armazenamento de disco do servidor de cópia de segurança

Para adicionar um volume ao servidor de cópia de segurança, o **gestão** painel, voltar a analisar o armazenamento e, em seguida, selecione **adicionar**. É apresentada uma lista de todos os volumes disponíveis para ser adicionado para armazenamento de servidor de cópia de segurança. Depois de volumes disponíveis são adicionadas à lista de volumes selecionados, pode conceder-lhes um nome amigável para o ajudar a geri-los. Para formatar estes volumes refs pelo servidor de cópia de segurança pode utilizar as vantagens do armazenamento de cópia de segurança moderna, selecione **OK**.

![Adicionar a Volumes disponíveis](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Configurar o armazenamento com suporte para a carga de trabalho

Com o armazenamento com suporte para a carga de trabalho, pode selecionar os volumes que armazenam preferentially determinados tipos de cargas de trabalho. Por exemplo, pode definir volumes dispendiosas que suportam um número elevado de operações de entrada/saída por segundo (IOPS) para armazenar apenas as cargas de trabalho que necessitam de cópias de segurança frequentes, elevado volume. Um exemplo é o SQL Server com registos de transações. Outras cargas de trabalho que são uma cópia de segurança com menos frequência, como VMs, podem ser efetuada em volumes de baixo custo.

### <a name="update-dpmdiskstorage"></a>Atualização DPMDiskStorage

Pode configurar o armazenamento com suporte para a carga de trabalho utilizando o cmdlet do PowerShell Update-DPMDiskStorage, que atualiza as propriedades de um volume no agrupamento de armazenamento num servidor do Data Protection Manager.

Sintaxe:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
A seguinte captura de ecrã mostra o cmdlet Update-DPMDiskStorage na janela do PowerShell.

![O comando de atualização DPMDiskStorage na janela do PowerShell](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

As alterações efetuadas utilizando o PowerShell são refletidas na consola do administrador do servidor de cópia de segurança.

![Discos e volumes na consola do administrador](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>Passos seguintes
Depois de instalar o servidor de cópia de segurança, saiba como preparar o servidor ou começar a proteger uma carga de trabalho.

- [Preparar as cargas de trabalho do servidor de cópia de segurança](backup-azure-microsoft-azure-backup.md)
- [Utilizar o servidor de cópia de segurança para fazer cópias de segurança de um servidor VMware](backup-azure-backup-server-vmware.md)
- [Utilizar o servidor de cópia de segurança para criar cópias de segurança do SQL Server](backup-azure-sql-mabs.md)

