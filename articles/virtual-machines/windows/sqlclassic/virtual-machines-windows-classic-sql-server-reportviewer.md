---
title: Utilize um Web Site ReportViewer | Microsoft Docs
description: "Este tópico descreve como criar um site Web do Microsoft Azure com o controlo ReportViewer do Visual Studio que apresenta um relatório armazenado numa máquina de Virtual do Microsoft Azure."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: asaxton
ms.openlocfilehash: c4f7c829e6fe3890342bd973185e679dd3ea2df5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Utilizar o ReportViewer num Web Site Alojado no Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Pode criar um site Web do Microsoft Azure com o controlo ReportViewer do Visual Studio que apresenta um relatório armazenado numa máquina de Virtual do Microsoft Azure. O controlo ReportViewer é numa aplicação Web que criar utilizando o modelo de aplicação ASP.NET Web.

> [!IMPORTANT]
> Os modelos de aplicação de Web de MVC do ASP.NET não suportam o controlo ReportViewer.

Para incorporar ReportViewer do Microsoft Azure Web site, tem de concluir as seguintes tarefas.

* **Adicionar** assemblagens para o pacote de implementação
* **Configurar** autenticação e autorização
* **Publicar** a aplicação ASP.NET Web no Azure

## <a name="prerequisites"></a>Pré-requisitos
Reveja a secção "geral recomendação e melhores práticas" no [SQL Server Business Intelligence em Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Controlos de ReportViewer são fornecidos com o Visual Studio, Standard Edition ou superior. Se estiver a utilizar o programador Web Express Edition, tem de instalar o [o tempo de execução do MICROSOFT REPORT VIEWER 2012](https://www.microsoft.com/download/details.aspx?id=35747) utilizar as funcionalidades de tempo de execução ReportViewer.
> 
> ReportViewer configurado no modo de processamento local não é suportada no Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>A adição de assemblagens para o pacote de implementação
Ao alojar ASP.NET aplicações no local, as assemblagens ReportViewer normalmente instaladas diretamente na cache de assemblagem global (GAC) do servidor do IIS durante a instalação do Visual Studio e podem ser acedidas diretamente pela aplicação. No entanto, ao alojar a sua aplicação ASP.NET na nuvem, Microsoft Azure não permite a nada ser instalada na GAC, pelo que tem de se certificar que as assemblagens ReportViewer estão disponíveis localmente para a sua aplicação. Pode fazê-lo ao adicionar referências aos mesmos no seu projeto e configurá-los para ser copiado localmente.

No modo de processamento remoto, o controlo ReportViewer utiliza as assemblagens seguintes:

* **Microsoft.ReportViewer.WebForms.dll**: contém o código de ReportViewer, tem de utilizar ReportViewer na sua página. Uma referência para esta assemblagem é adicionada ao seu projeto quando remover um controlo ReportViewer para uma página ASP.NET no seu projeto.
* **Microsoft.ReportViewer.Common.dll**: contém classes utilizadas pelo controlo ReportViewer em tempo de execução. Não é automaticamente adicionado ao seu projeto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para adicionar uma referência a Microsoft.ReportViewer.Common
* Clique com o botão direito do seu projeto **referências** nó e selecione **Adicionar referência**, selecione a assemblagem no separador de .NET e clique em **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para tornar as assemblagens localmente acessível pela sua aplicação ASP.NET
1. No **referências** pasta, clique em Microsoft.ReportViewer.Common assemblagem para que as respetivas propriedades aparecem no painel de propriedades.
2. No painel de propriedades, defina **Cópia Local** como True.
3. Repita os passos 1 e 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obter o pacote de idiomas do ReportViewer
1. Instalar o pacote redistribuível do Microsoft Report Viewer 2012 Runtime adequado de [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Selecione o idioma na lista pendente e a página redirecionada para a página de centro de transferências correspondente.
3. Clique em **transferir** para iniciar a transferência de ReportViewerLP.exe.
4. Depois de transferir ReportViewerLP.exe, clique em **executar** a instalação imediatamente, ou clique em **guardar** para guardá-lo para o seu computador. Se clicar em **guardar**, lembre-se o nome da pasta onde guardar o ficheiro.
5. Localize a pasta onde guardou o ficheiro. Clique com o botão direito ReportViewerLP.exe, clique em **executar como administrador**e, em seguida, clique em **Sim**.
6. Depois de executar ReportViewerLP.exe, irá ver o c:\windows\assembly tem os ficheiros de recursos **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar para o controlo ReportViewer localizado
1. Transfira e instale o pacote redistribuível do Microsoft Report Viewer 2012 Runtime ao seguir as instruções especificadas acima.
2. Criar <language> pasta no projeto e copie a assemblagem de recursos associados ficheiros não existe. Os ficheiros de assemblagem de recursos a ser copiado são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os ficheiros de assemblagem de recursos e, no painel de propriedades, defina **copiar para o diretório de saída** para "**Copiar sempre**".
3. Defina o idioma & UICulture para o projeto web. Para obter mais informações sobre como definir o idioma e cultura da IU para uma página ASP.NET Web, consulte [como: definir o idioma e cultura da IU para globalização de página Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurar a autenticação e autorização
ReportViewer tem de utilizar as credenciais corretas para autenticar com o servidor de relatórios e as credenciais tem de ser autorizadas pelo servidor de relatórios para aceder aos relatórios que pretende. Para informações sobre a autenticação, consulte o documento técnico [relatório do Reporting Services controlo do Visualizador e servidores de relatórios do Microsoft Azure baseada em máquina virtual](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar a aplicação ASP.NET Web no Azure
Para obter instruções sobre como publicar uma aplicação ASP.NET Web para o Azure, consulte [como: migrar e publicar uma aplicação Web no Azure a partir do Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [introdução às Web Apps e ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Se o comando Adicionar projeto de implementação do Azure ou adicionar projeto de serviço de nuvem do Azure não aparecer no menu de atalho no Explorador de soluções, poderá ter de alterar o framework de destino para o projeto para .NET Framework 4.
> 
> Os dois comandos fornecem essencialmente a mesma funcionalidade. Um ou o outro comando será apresentado o menu de atalho consoante a versão do Microsoft Azure SDK que instalou.
> 
> 

## <a name="resources"></a>Recursos
[Relatórios da Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence em Máquinas Virtuais do Azure](../classic/ps-sql-bi.md)

[Utilizar o PowerShell para Criar uma VM do Azure com um Servidor de Relatórios no Modo Nativo](../classic/ps-sql-report.md)

