---
title: "Instalar as tarefas de bases de dados elásticas | Microsoft Docs"
description: "Percorrer a instalação da funcionalidade tarefa elástico."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: cbe0aa2b-17e3-4b6f-a16f-6ebc1f5a66af
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 7c741deba180af56414b0711de94ba110aeea9b8
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="installing-elastic-database-jobs-overview"></a>Descrição geral de tarefas de bases de dados elásticas instalar
[**As tarefas de base de dados elásticas** ](sql-database-elastic-jobs-overview.md) podem ser instalados através do PowerShell ou através de Portal.You clássico do Azure pode obter acesso ao criar e gerir tarefas utilizando a API de PowerShell apenas se instalar o pacote do PowerShell. Além disso, as APIs do PowerShell proporcionar significativamente mais funcionalidade que o portal, neste ponto no tempo.

Se já tiver instalado **tarefas de bases de dados elásticas** através do Portal existente de um **conjunto elástico**, a pré-visualização mais recente do Powershell inclui scripts para atualizar a instalação existente. É altamente recomendado para atualizar a instalação para a versão mais recente **tarefas de bases de dados elásticas** componentes para tirar o máximo partido das novas funcionalidades de expostas através das APIs do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma versão de avaliação gratuita, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Instalar a versão mais recente com o [instalador de plataforma Web](http://go.microsoft.com/fwlink/p/?linkid=320376). Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* [Utilitário de linha de comandos do NuGet](https://nuget.org/nuget.exe) é utilizado para instalar o pacote de tarefas de bases de dados elásticas. Para obter mais informações, consulte http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Transferir e importar o pacote de PowerShell de tarefas de base de dados elástica
1. Inicie a janela de comando do PowerShell do Microsoft Azure e navegue para o diretório onde transferiu o utilitário de linha de comandos do NuGet (nuget.exe).
2. Transferir e importar **tarefas de bases de dados elásticas** pacote para o diretório atual com o seguinte comando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    O **tarefas de bases de dados elásticas** são colocados ficheiros no diretório local numa pasta denominada **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x** onde *x.x.xxxx.x* reflete o número de versão. Os cmdlets do PowerShell (incluindo clientes necessários .dlls) estão localizados no **tools\ElasticDatabaseJobs** subdiretório e os scripts do PowerShell para instalar, atualizar e desinstalar também residem os **ferramentas** subdiretório.
3. Navegue para o subdiretório de ferramentas na pasta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x escrevendo cd ferramentas, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Execute o script de.\InstallElasticDatabaseJobsCmdlets.ps1 para copiar o diretório de ElasticDatabaseJobs para $home\Documents\WindowsPowerShell\Modules. Isto também importará automaticamente o módulo para utilização, por exemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Instalar os componentes de tarefas de bases de dados elásticas com o PowerShell
1. Inicie uma janela de comando do PowerShell do Microsoft Azure e navegue para o subdiretório de \tools sob a pasta de Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: escreva cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Executar o script do PowerShell de.\InstallElasticDatabaseJobs.ps1 e fornecer valores para os pedido variáveis. Este script irá criar componentes descritos na [componentes e preços das tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md#components-and-pricing) juntamente com a configurar o serviço de nuvem do Azure para utilizar adequadamente os componentes dependentes.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Quando executar este comando uma janela abre solicitando um **nome de utilizador** e **palavra-passe**. Não se trata das suas credenciais do Azure, introduza o nome de utilizador e palavra-passe que serão as credenciais de administrador que pretende criar para o novo servidor.

Os parâmetros fornecidos nesta invocação de exemplo podem ser modificados para as definições desejadas. O seguinte fornece mais informações sobre o comportamento de cada um dos parâmetros:

<table style="width:100%">
  <tr>
    <th>Parâmetro</th>
    <th>Descrição</th>
  </tr>

<tr>
    <td>resourceGroupName</td>
    <td>Fornece o nome do grupo de recursos do Azure criado para conter os componentes do Azure criados recentemente. Este parâmetro assume como "__ElasticDatabaseJob". Não se recomenda alterar este valor.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Fornece a localização do Azure a utilizar para os componentes do Azure criados recentemente. Este parâmetro assume-se para a localização EUA Central.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar. Este parâmetro assume como 1. Um número mais elevado de trabalhadores pode ser utilizado para aumentar horizontalmente o serviço e para fornecer elevada disponibilidade. Recomenda-se para utilizar "2" para implementações que necessitam de elevada disponibilidade do serviço.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para utilização no âmbito do serviço de nuvem. Este parâmetro assume como A0. Valores de parâmetros de A0/A1/A2/A3 são aceites que fazer com que a função de trabalho utilizar um tamanho de extra pequena/pequena/média/grande, respetivamente. FO obter mais informações sobre os tamanhos de função de trabalho, consulte [componentes e preços das tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Fornece o objetivo de nível de serviço para uma edição Standard. Este parâmetro assume como S0. Os valores de parâmetros de S0/S1/S2/S3/S4/S6/S9/S12 são aceites que fazer com que a base de dados do SQL do Azure utilizar o respetivo SLO. Para obter mais informações sobre os SLOs de base de dados do SQL Server, consulte [componentes e preços das tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Fornece o nome de utilizador de administrador para o servidor da SQL Database do Azure criado recentemente. Quando não especificado, irá abrir uma janela de credenciais do PowerShell para solicitar as credenciais.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Fornece a palavra-passe de administrador para o servidor da SQL Database do Azure criado recentemente. Quando não fornecido, irá abrir uma janela de credenciais do PowerShell para solicitar as credenciais.</td>
</tr>
</table>

Para os sistemas que ter grande número de tarefas em execução em paralelo em relação a um grande número de bases de dados de destino, é recomendado para especificar parâmetros, tais como: - ServiceWorkerCount 2 - ServiceVmSize A2 - SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Atualizar uma instalação de componentes do tarefas bases de dados elásticas existente com o PowerShell
**As tarefas de base de dados elásticas** podem ser atualizadas dentro de uma instalação existente de escala e elevada disponibilidade. Este processo permite que as atualizações futuras de código do serviço sem ter de remover e recriar a base de dados de controlo. Este processo também pode ser utilizado a mesma versão para modificar o tamanho da VM de serviço ou a contagem de trabalho do servidor.

Para atualizar o tamanho da VM de uma instalação, execute o seguinte script com parâmetros atualizados para os valores da sua preferência.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parâmetro</th>
  <th>Descrição</th>
</tr>

  <tr>
    <td>resourceGroupName</td>
    <td>Identifica o nome do grupo de recursos do Azure utilizado quando os componentes de tarefa de base de dados elástica inicialmente foram instalados. Este parâmetro assume como "__ElasticDatabaseJob". Uma vez que não se recomenda alterar este valor, não deve tem de especificar este parâmetro.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Fornece o número de trabalhadores de serviço para instalar.  Este parâmetro assume como 1.  Um número mais elevado de trabalhadores pode ser utilizado para aumentar horizontalmente o serviço e para fornecer elevada disponibilidade.  Recomenda-se para utilizar "2" para implementações que necessitam de elevada disponibilidade do serviço.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Fornece o tamanho da VM para utilização no âmbito do serviço de nuvem. Este parâmetro assume como A0. Valores de parâmetros de A0/A1/A2/A3 são aceites que fazer com que a função de trabalho utilizar um tamanho de extra pequena/pequena/média/grande, respetivamente. FO obter mais informações sobre os tamanhos de função de trabalho, consulte [componentes e preços das tarefas de bases de dados elásticas](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Instalar os componentes de tarefas de bases de dados elásticas com o Portal
Assim que tiver [criado um conjunto elástico](sql-database-elastic-pool-manage-portal.md), pode instalar **tarefas de bases de dados elásticas** componentes para ativar a execução das tarefas administrativas em relação a cada base de dados no agrupamento elástico. Ao contrário quando utilizar o **tarefas de bases de dados elásticas** das APIs do PowerShell, a interface do portal é atualmente restrito para executar apenas em relação a um conjunto existente.

**Tempo estimado para concluir:** 10 minutos.

1. Da vista de dashboard do conjunto elástico através de [Portal do Azure](https://portal.azure.com/#) , clique em **criar tarefa**.
2. Se estiver a criar uma tarefa pela primeira vez, tem de instalar **tarefas de bases de dados elásticas** clicando **termos de pré-visualização**.
3. Aceite os termos clicando na caixa de verificação.
4. Na vista "Instalar os serviços de", clique em **CREDENCIAIS de tarefa**.
   
    ![Instalar os serviços][1]
5. Escreva um nome de utilizador e palavra-passe para um administrador da base de dados. Como parte da instalação, é criado um novo servidor da SQL Database do Azure. Dentro deste novo servidor, uma nova base de dados, conhecido como a base de dados de controlo, é criado e utilizado para conter os dados de metadados para as tarefas de bases de dados elásticas. O nome de utilizador e palavra-passe criada aqui são utilizados para fins de iniciar sessão na base de dados de controlo. Uma credencial separada é utilizada para a execução do script contra as bases de dados dentro do conjunto.
   
    ![Criar o nome de utilizador e palavra-passe][2]
6. Clique no botão OK. Os componentes são criados por si dentro de alguns minutos numa nova [grupo de recursos](../azure-resource-manager/resource-group-overview.md). O novo grupo de recursos está afixado painel início, conforme mostrado abaixo. Tarefas de base de dados elásticas, depois de criada (serviço em nuvem, base de dados SQL, Service Bus e armazenamento) são criadas no grupo.
   
    ![grupo de recursos no painel de início][3]
7. Se tentar criar ou gerir uma tarefa, enquanto as tarefas de base de dados elástica está a instalar, ao fornecer **credenciais** , verá a mensagem seguinte.
   
    ![Implementação ainda em curso][4]

Se for necessário desinstalar, elimine o grupo de recursos. Consulte [como desinstalar os componentes de tarefa da base de dados elástica](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Passos seguintes
Certifique-se de uma credencial com os direitos adequados para a execução do script é criada em cada base de dados no grupo, para obter mais informações, consulte [proteger a sua base de dados do SQL Server](sql-database-manage-logins.md).
Consulte [criar e gerir tarefas de uma base de dados elástica](sql-database-elastic-jobs-create-and-manage.md) para começar a utilizar.

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
