---
title: "Cópia de segurança de base de dados de Analysis Services do Azure e de restauro | Microsoft Docs"
description: "Descreve como criar cópias de segurança e restaurar uma base de dados do Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: f96e72e4decd475e7859eb7f70046a277b718ac8
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="backup-and-restore"></a>Cópia de segurança e restauro

Cópia de segurança de bases de dados do modelo em tabela no Azure Analysis Services é muito as mesmas que nos serviços de Analysis Services no local. A principal diferença é onde armazenar os ficheiros de cópia de segurança. Ficheiros de cópia de segurança tem de ser guardados para um contentor num [conta do storage do Azure](../storage/common/storage-create-storage-account.md). Pode utilizar uma conta de armazenamento e um contentor que já tem ou pode ser criados quando configurar as definições de armazenamento para o servidor.

> [!NOTE]
> Criar uma conta do storage pode resultar num novo serviço sujeito a faturação. Para obter mais informações, consulte [preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

As cópias de segurança são guardadas com uma extensão de abf. Dentro da memória para modelos de tabela, dados do modelo e os metadados são armazenados. DirectQuery para modelos de tabela, apenas os metadados do modelo é armazenado. As cópias de segurança podem ser comprimidas e encriptadas, dependendo das opções que escolher. 



## <a name="configure-storage-settings"></a>Configurar as definições de armazenamento
Antes de criar cópias de segurança, terá de configurar as definições de armazenamento para o servidor.


### <a name="to-configure-storage-settings"></a>Para configurar as definições de armazenamento
1.  No portal do Azure > **definições**, clique em **cópia de segurança**.

    ![Definições de cópias de segurança](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **ativado**, em seguida, clique em **as definições de armazenamento**.

    ![Ativar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione a sua conta de armazenamento ou crie um novo.

4. Selecione um contentor ou crie um novo.

    ![Selecione o contentor](./media/analysis-services-backup/aas-backup-container.png)

5. Guarde as definições de cópia de segurança.

    ![Guardar as definições de cópia de segurança](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Cópia de segurança

### <a name="to-backup-by-using-ssms"></a>A cópia de segurança com o SSMS

1. No SSMS, faça duplo clique uma base de dados > **cópia de segurança**.

2. No **base de dados de cópia de segurança** > **ficheiro de cópia de segurança**, clique em **procurar**.

3. No **Guardar ficheiro como** caixa de diálogo, verifique o caminho de pasta e, em seguida, escreva um nome para o ficheiro de cópia de segurança. 

4. No **base de dados de cópia de segurança** caixa de diálogo, selecionadas opções.

    **Permitir que o ficheiro substituir** -Selecione esta opção para substituir os ficheiros de cópia de segurança com o mesmo nome. Se esta opção não estiver selecionada, o ficheiro que está a guardar não pode ter o mesmo nome como um ficheiro que já existe na mesma localização.

    **Aplicar compressão** -Selecione esta opção para comprimir o ficheiro de cópia de segurança. Ficheiros de cópia de segurança comprimidos poupar espaço em disco, mas necessitam de utilização da CPU ligeiramente superior. 

    **Encriptar o ficheiro de cópia de segurança** -Selecione esta opção para encriptar o ficheiro de cópia de segurança. Esta opção requer uma palavra-passe fornecido pelo utilizador para proteger o ficheiro de cópia de segurança. A palavra-passe impede a leitura dos dados de cópia de segurança de quaisquer outros meios que uma operação de restauro. Se optar por encriptar as cópias de segurança, armazene a palavra-passe numa localização segura.

5. Clique em **OK** para criar e guardar o ficheiro de cópia de segurança.


### <a name="powershell"></a>PowerShell
Utilize [ASDatabase de cópia de segurança](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) cmdlet.

## <a name="restore"></a>Restauro
Ao restaurar, tem de ser o ficheiro de cópia de segurança na conta de armazenamento que configurou para o servidor. Se precisar de mover um ficheiro de cópia de segurança a partir de uma localização no local à sua conta de armazenamento, utilize [Explorador de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou [AzCopy](../storage/common/storage-use-azcopy.md) utilitário da linha de comandos. 



> [!NOTE]
> Se estiver a restaurar a partir de um servidor no local, tem de remover todos os utilizadores de domínio de funções de modelo e adicione-a novamente para as funções como utilizadores do Active Directory do Azure.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar com SSMS

1. No SSMS, faça duplo clique uma base de dados > **restaurar**.

2. No **base de dados de cópia de segurança** caixa de diálogo, em **ficheiro de cópia de segurança**, clique em **procurar**.

3. No **localizar ficheiros de base de dados** caixa de diálogo, selecione o ficheiro que pretende restaurar.

4. No **restaurar base de dados**, selecione a base de dados.

5. Especifique as opções. Opções de segurança têm de corresponder as opções de cópia de segurança que utilizou quando efetuar cópias de segurança.


### <a name="powershell"></a>PowerShell

Utilize [restauro ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) cmdlet.


## <a name="related-information"></a>Informações relacionadas

[Contas do storage do Azure](../storage/common/storage-create-storage-account.md)  
[Elevada disponibilidade](analysis-services-bcdr.md)     
[Gerir serviços de análise do Azure](analysis-services-manage.md)
