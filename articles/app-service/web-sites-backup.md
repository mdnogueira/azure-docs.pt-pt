---
title: "Efetuar cópia de segurança da sua aplicação no Azure"
description: "Saiba como criar cópias de segurança das suas aplicações no App Service do Azure."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 435370a8758d439a5fcce2e04efd11b4aaaf0357
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2017
---
# <a name="back-up-your-app-in-azure"></a>Efetuar cópia de segurança da sua aplicação no Azure
A funcionalidade de cópia de segurança e restauro no [App Service do Azure](app-service-web-overview.md) permite-lhe facilmente criar cópias de segurança de aplicação com base num agendamento ou manualmente. Pode restaurar a aplicação para um instantâneo de um estado anterior ao substituir a aplicação existente ou o restauro para outra aplicação. 

Para obter informações sobre o restauro de uma aplicação de cópia de segurança, consulte [restaurar uma aplicação no Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>O que obtém uma cópia de segurança
Serviço de aplicações pode fazer cópias de segurança as seguintes informações para uma conta de armazenamento do Azure e um contentor que configurou a sua aplicação para utilizar. 

* Configuração de aplicação
* Conteúdo do ficheiro
* Base de dados ligada à sua aplicação

São suportadas as seguintes soluções de base de dados com a funcionalidade cópia de segurança: 
   - [Base de Dados SQL](https://azure.microsoft.com/en-us/services/sql-database/)
   - [Base de dados do Azure para MySQL (pré-visualização)](https://azure.microsoft.com/en-us/services/mysql)
   - [Base de dados do Azure para PostgreSQL (pré-visualização)](https://azure.microsoft.com/en-us/services/postgres)
   - [MySQL na aplicação](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Cada cópia de segurança é uma cópia completa de offline da sua aplicação, não uma atualização incremental.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Requisitos e restrições
* A funcionalidade de cópia de segurança e restauro requer o plano de serviço de aplicações de ser o **padrão** camada ou **Premium** camada. Para obter mais informações sobre como dimensionar o seu plano do App Service para utilizar um escalão superior, consulte [aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md).  
  **Premium** camada permite um maior número de diariamente fazer uma cópia ups que **padrão** camada.
* Precisa de uma conta de armazenamento do Azure e um contentor na mesma subscrição que a aplicação que pretende criar cópias de segurança. Para obter mais informações sobre contas do storage do Azure, consulte o [ligações](#moreaboutstorage) no final deste artigo.
* As cópias de segurança podem ter até 10 GB de conteúdo de aplicação e da base de dados. Se o tamanho da cópia de segurança ultrapassa este limite, receberá um erro.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
1. No [portal do Azure](https://portal.azure.com), navegue até à página da aplicação, selecione **cópias de segurança**. O **cópias de segurança** é apresentada a página.
   
    ![Página de cópias de segurança][ChooseBackupsPage]
   
   > [!NOTE]
   > Se vir a mensagem seguinte, clique na mesma para atualizar o seu plano de serviço de aplicações antes de poder continuar com cópias de segurança.
   > Para obter mais informações, consulte [aumentar verticalmente a uma aplicação no Azure](web-sites-scale.md).  
   > ![Escolha a conta de armazenamento](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. No **cópia de segurança** página, clique em **configurar**
![clique em configurar](./media/web-sites-backup/ClickConfigure1.png)
3. No **configuração de cópia de segurança** página, clique em **armazenamento: não configurado** para configurar uma conta de armazenamento.
   
    ![Escolha a conta de armazenamento][ChooseStorageAccount]
4. Escolha o destino de cópia de segurança, selecionando um **conta de armazenamento** e **contentor**. A conta de armazenamento têm de pertencer à mesma subscrição que a aplicação que pretende criar cópias de segurança. Se assim o desejar, pode criar uma nova conta de armazenamento ou um novo contentor nas respetivas páginas. Quando tiver terminado, clique em **selecione**.
   
    ![Escolha a conta de armazenamento](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. No **configuração de cópia de segurança** página que é ainda deixada aberta, pode configurar **base de dados de cópia de segurança**, em seguida, selecione as bases de dados que pretende incluir nas cópias de segurança (base de dados SQL ou MySQL), em seguida, clique em **OK**.  
   
    ![Escolha a conta de armazenamento](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > Para uma base de dados for apresentada nesta lista, a cadeia de ligação tem de existir o **cadeias de ligação** secção o **definições da aplicação** página para a sua aplicação.
   > 
   > 
6. No **configuração de cópia de segurança** página, clique em **guardar**.    
7. No **cópias de segurança** página, clique em **cópia de segurança**.
   
    ![Botão de BackUpNow][BackUpNow]
   
    Verá uma mensagem de progresso durante o processo de cópia de segurança.

Assim que a conta de armazenamento e o contentor está configurado, pode iniciar uma cópia de segurança manual em qualquer altura.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Configurar cópias de segurança automatizadas
1. No **configuração de cópia de segurança** página, defina **cópia de segurança agendada** para **no**. 
   
    ![Escolha a conta de armazenamento](./media/web-sites-backup/05ScheduleBackup1.png)
2. Definir a agenda de cópia de segurança irão mostrar as opções de cópia de segurança, **Agendar cópia de segurança** para **no**, em seguida, configure a agenda de cópia de segurança conforme pretendido e clique em **OK**.
   
    ![Ativar as cópias de segurança automatizadas][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Configurar cópias de segurança parciais
Por vezes, não pretender efetuar cópias de segurança tudo na sua aplicação. Eis alguns exemplos:

* [Configurar cópias de segurança semanais](web-sites-backup.md#configure-automated-backups) da sua aplicação que contém conteúdo estático que nunca são alterados, tais como mensagens de blogue antigo ou nas imagens.
* A aplicação tiver mais de 10 GB de conteúdo (que é a quantidade máxima que pode fazer cópias de segurança a uma hora).
* Não pretender efetuar cópias de segurança os ficheiros de registo.

As cópias de segurança parciais permitem que escolha exatamente os ficheiros que pretende criar cópias de segurança.

### <a name="exclude-files-from-your-backup"></a>Excluir ficheiros da sua cópia de segurança
Suponha que tiver uma aplicação que contém ficheiros de registo e imagens estáticas que foram cópia de segurança uma vez e se não pretender alterar. Nestes casos, pode excluir esses ficheiros e pastas de que está a ser armazenados no seu cópias de segurança futuras. Para excluir pastas e ficheiros das cópias de segurança, crie um `_backup.filter` ficheiros o `D:\home\site\wwwroot` pasta da sua aplicação. Especifique a lista de ficheiros e pastas que pretende excluir neste ficheiro. 

É uma forma fácil de aceder aos seus ficheiros a utilizar o Kudu. Clique em **avançadas ferramentas -> aceda** definição para a sua aplicação web aceder à Kudu.

![Kudu através do portal][kudu-portal]

Identifique as pastas que pretende excluir das cópias de segurança.  Por exemplo, pretende filtrar os ficheiros e pastas realçada.

![Pasta de imagens][ImagesFolder]

Crie um ficheiro chamado `_backup.filter` e colocar na lista anterior no ficheiro, mas remover `D:\home`. Liste um diretório ou ficheiro por linha. Por isso, o conteúdo do ficheiro deve ser:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Carregar `_backup.filter` do ficheiro para o `D:\home\site\wwwroot\` diretório do seu site com [ftp](app-service-deploy-ftp.md) ou qualquer outro método. Se assim o desejar, pode criar o ficheiro diretamente com o Kudu `DebugConsole` e inserir o conteúdo não existe.

Executar cópias de segurança da mesma forma que normalmente iria fazê-lo, [manualmente](#create-a-manual-backup) ou [automaticamente](#configure-automated-backups). Agora, todos os ficheiros e pastas que estão especificadas na `_backup.filter` está excluído das cópias de segurança futuras agendadas ou iniciado manualmente. 

> [!NOTE]
> Restaurar cópias de segurança parciais do seu site da mesma forma que faria [restaurar uma cópia de segurança regular](web-sites-restore.md). O processo de restauro efetua a coisa adequada.
> 
> Quando uma cópia de segurança completa é restaurada, todo o conteúdo no site é substituído por que é na cópia de segurança. Se um ficheiro está no site, mas não na cópia de segurança é eliminado. Mas, quando uma cópia de segurança parcial é restaurada, qualquer conteúdo que está localizado em um dos diretórios de blacklisted ou qualquer ficheiro blacklisted, for deixado como está.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Como as cópias de segurança são armazenadas
Depois de efetuar cópias de segurança de um ou mais para a sua aplicação, as cópias de segurança são visíveis no **contentores** página da sua conta de armazenamento e a aplicação. Na conta de armazenamento, cada cópia de segurança é composta por um`.zip` ficheiro que contém os dados de cópia de segurança e um `.xml` ficheiro que contém um manifesto do `.zip` conteúdo de ficheiros. Pode deszipe e procurar estes ficheiros, se pretender aceder às suas cópias de segurança sem realmente efetuar um restauro de aplicação.

A cópia de segurança da base de dados para a aplicação é armazenada na raiz do ficheiro. zip. Para uma base de dados do SQL Server, isto é um BACPAC de ficheiro (extensão de ficheiro) e pode ser importado. Para criar uma base de dados do SQL Server com base na exportação BACPAC, consulte [importar um ficheiro de BACPAC para criar uma nova base de dados do utilizador](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Alterar qualquer um dos ficheiros na sua **websitebackups** contentor pode fazer com que a cópia de segurança para se tornarem inválida e, por conseguinte, não-que possam ser restauradas.
> 
> 

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar gestão de cópia de segurança com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

Para exemplos, consulte:

- [Exemplos da CLI do Azure](app-service-cli-samples.md)
- [Exemplos do PowerShell do Azure](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre o restauro de uma aplicação de uma cópia de segurança, consulte [restaurar uma aplicação no Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

