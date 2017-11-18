---
title: "Restaurar uma aplicação no Azure"
description: "Saiba como restaurar a aplicação a partir de uma cópia de segurança."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 60b55acaf687dae76e054b428b8d6584d8eb09f8
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="restore-an-app-in-azure"></a>Restaurar uma aplicação no Azure
Este artigo mostra como restaurar uma aplicação no [App Service do Azure](../app-service/app-service-web-overview.md) que criou anteriormente ter cópias de segurança (consulte [cópia de segurança sua aplicação no Azure](web-sites-backup.md)). Pode restaurar a aplicação com o respetiva ligado bases de dados a pedido para um estado anterior ou criar uma nova aplicação com base dos cópias de segurança da sua aplicação original. App Service do Azure suporta as seguintes bases de dados de cópia de segurança e restauro:
- [Base de Dados SQL](https://azure.microsoft.com/en-us/services/sql-database/)
- [Base de dados do Azure para MySQL (pré-visualização)](https://azure.microsoft.com/en-us/services/mysql)
- [Base de dados do Azure para PostgreSQL (pré-visualização)](https://azure.microsoft.com/en-us/services/postgres)
- [MySQL na aplicação](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

O restauro de cópias de segurança está disponível para aplicações em execução **padrão** e **Premium** camada. Para obter informações sobre como aumentar verticalmente a sua aplicação, consulte [aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md). **Premium** camada permite um maior número de cópias de segurança diárias para ser executada a **padrão** camada.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar uma aplicação a partir de uma cópia de segurança existente
1. No **definições** página da sua aplicação no portal do Azure, clique em **cópias de segurança** para apresentar o **cópias de segurança** página. Em seguida, clique em **restaurar**.
   
    ![Escolha o restauro agora][ChooseRestoreNow]
2. No **restaurar** página, primeiro selecione a origem de cópia de segurança.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    O **cópia de segurança da aplicação** opção mostra todas as cópias de segurança existentes da aplicação atual e facilmente pode selecionar um.
    O **armazenamento** opção permite-lhe selecionar qualquer ficheiro ZIP de cópia de segurança a partir de qualquer conta de armazenamento do Azure e o contentor na sua subscrição existente.
    Se estiver a tentar restaurar uma cópia de segurança de outra aplicação, utilize o **armazenamento** opção.
3. Em seguida, especifique o destino para o restauro da aplicação no **o destino de restauro**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Se optar por **substituir**, todos os dados existentes na sua aplicação atual apagar e substituídos. Antes de clicar em **OK**, certifique-se de que é exatamente o que pretende fazer.
   > 
   > 
   
    Pode selecionar **aplicação existente** para restaurar a cópia de segurança de aplicação para outra aplicação no mesmo grupo de resoure. Antes de utilizar esta opção, deve já criou outra aplicação no seu grupo de recursos com o espelhamento de configuração de base de dados para um definido na cópia de segurança de aplicação. Também pode criar um **novo** aplicação para restaurar o seu conteúdo.

4. Clique em **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Transferir ou eliminar uma cópia de segurança de uma conta de armazenamento
1. A partir de principal **procurar** página do portal do Azure, selecione **contas do Storage**. É apresentada uma lista das suas contas de armazenamento existente.
2. Selecione a conta de armazenamento que contém a cópia de segurança que pretende transferir ou eliminar. É apresentada a página para a conta de armazenamento.
3. Na página de conta de armazenamento, selecione o contentor que pretende
   
    ![Contentores de vista][ViewContainers]
4. Selecione o ficheiro de cópia de segurança que pretende transferir ou eliminar.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Clique em **transferir** ou **eliminar** , dependendo de que pretende efetuar.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorizar uma operação de restauro
Para ver detalhes sobre o êxito ou falha da operação de restauro de aplicação, navegue para o **registo de atividade** página no portal do Azure.  
 

Desloque para baixo para localizar a operação de restauro e clique para selecionar o pretendido.

A página de detalhes apresenta as informações disponíveis relacionadas com a operação de restauro.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar gestão de cópia de segurança com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para exemplos, consulte:

- [Exemplos da CLI do Azure](app-service-cli-samples.md)
- [Exemplos do PowerShell do Azure](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
