---
title: "Aplicação de web Django numa VM do Azure do Windows Server | Microsoft Docs"
description: "Saiba como alojar um Web site baseado em Django no Azure através de uma VM do Windows Server 2012 R2 Datacenter com o modelo de implementação clássica."
services: virtual-machines-windows
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-service-management
ms.assetid: e36484d1-afbf-47f5-b755-5e65397dc1c3
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.openlocfilehash: 01fa162d41e03e29f3b6f0ca128e7cc49aa91abb
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="django-hello-world-web-app-on-a-windows-server-vm"></a>Aplicação de web Django Olá, mundo numa VM do Windows

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager e o modelo de implementação clássica](../../../resource-manager-deployment-model.md). Este artigo descreve o modelo de implementação clássica. Recomendamos que as implementações mais novas utilizem o modelo do Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Este tutorial mostra como alojar um Web site baseado em Django no Windows Server em Azure Virtual Machines. O tutorial, partimos sem experiência anterior com o Azure. Quando concluir o tutorial, pode ter uma aplicação Django com base em cópia de segurança e em execução na nuvem.

Aprenda a:

* Configure uma máquina virtual do Azure para alojar o Django. Embora este tutorial explica como fazê-lo **do Windows Server**, para fazer o mesmo para uma VM com Linux alojados no Azure.
* Crie uma nova aplicação Django no Windows.

O tutorial mostra como criar uma aplicação de web básica Olá, mundo. A aplicação está alojada numa máquina virtual do Azure.

A seguinte captura de ecrã mostra a aplicação concluída:

![Uma janela do browser apresenta a página Olá, mundo no Azure][1]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>Criar e configurar uma máquina virtual do Azure para alojar o Django

1. Para criar uma máquina virtual do Azure com a distribuição do Windows Server 2012 R2 Datacenter, consulte [criar uma máquina virtual com o Windows no portal do Azure](tutorial.md).
2. Definir o Azure para direcionar porta 80 o tráfego da web para a porta 80 na máquina virtual:
   
   1. No portal do Azure, aceda ao dashboard e selecione a máquina virtual recentemente criada.
   2. Clique em **Pontos finais** e, em seguida, clique em **Adicionar**.

     ![Adicionar um ponto final](./media/python-django-web-app/django-helloworld-add-endpoint-new-portal.png)

   3. No **adicionar ponto final** página, para **nome**, introduza **HTTP**. Defina o TCP pública e privada portas para **80**.

     ![Introduza o nome e defina portas públicas e privadas](./media/python-django-web-app/django-helloworld-add-endpoint-set-ports-new-portal.png)

   4. Clique em **OK**.
     
3. No dashboard, selecione a VM. Para utilizar o protocolo RDP (Remote Desktop Protocol) para iniciar sessão remotamente na máquina virtual do Azure criada recentemente, clique em **Connect**.  

> [!IMPORTANT] 
> As instruções seguintes partem do princípio de que tem sessão iniciada para a máquina virtual corretamente. Eles também partem do princípio de que está a emitir comandos na máquina virtual e não no computador local.

## <a id="setup"></a>Instalar o Python, Django e WFastCGI
> [!NOTE]
> Para transferir utilizando o Internet Explorer, poderá ter de configurar o Internet Explorer **configuração de segurança avançada** definições. Para tal, clique em **iniciar** > **ferramentas administrativas** > **Gestor de servidor** > **deservidorLocal**. Clique em **configuração de segurança avançada do IE**e, em seguida, selecione **desativar**.

1. Instalar as versões mais recentes do Python 2.7 ou 3.4 Python do [python.org][python.org].
2. Instale os pacotes de wfastcgi e django através do pip.
   
    Para Python 2.7, utilize o seguinte comando:
   
        c:\python27\scripts\pip install wfastcgi
        c:\python27\scripts\pip install django
   
    Para Python 3.4, utilize o seguinte comando:
   
        c:\python34\scripts\pip install wfastcgi
        c:\python34\scripts\pip install django

## <a name="install-iis-with-fastcgi"></a>Instalar o IIS com FastCGI
* Instale serviços de informação Internet (IIS) com suporte de FastCGI. Esta operação poderá demorar vários minutos a executar.
   
        start /wait %windir%\System32\PkgMgr.exe /iu:IIS-WebServerRole;IIS-WebServer;IIS-CommonHttpFeatures;IIS-StaticContent;IIS-DefaultDocument;IIS-DirectoryBrowsing;IIS-HttpErrors;IIS-HealthAndDiagnostics;IIS-HttpLogging;IIS-LoggingLibraries;IIS-RequestMonitor;IIS-Security;IIS-RequestFiltering;IIS-HttpCompressionStatic;IIS-WebServerManagementTools;IIS-ManagementConsole;WAS-WindowsActivationService;WAS-ProcessModel;WAS-NetFxEnvironment;WAS-ConfigurationAPI;IIS-CGI

## <a name="create-a-new-django-application"></a>Criar uma nova aplicação Django
1. Na C:\inetpub\wwwroot, para criar um novo projeto do Django, introduza o seguinte comando:
   
   Para Python 2.7, utilize o seguinte comando:
   
       C:\Python27\Scripts\django-admin.exe startproject helloworld
   
   Para Python 3.4, utilize o seguinte comando:
   
       C:\Python34\Scripts\django-admin.exe startproject helloworld
   
   ![O resultado do comando New-AzureService](./media/python-django-web-app/django-helloworld-cmd-new-azure-service.png)
2. O `django-admin` comandos gera uma estrutura básica para Web sites baseados em Django:
   
   * `helloworld\manage.py`ajuda-o a alojar o início e paragem que aloja o Web site baseado em Django.
   * `helloworld\helloworld\settings.py`tem Django definições para a sua aplicação.
   * `helloworld\helloworld\urls.py`tem o código de mapeamento entre cada URL e a vista.
3. No diretório C:\inetpub\wwwroot\helloworld\helloworld, crie um novo ficheiro designado views.py. Este ficheiro tem a vista que compõe a página de "Olá mundo". No seu editor de código, introduza os seguintes comandos:
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. Substitua o conteúdo do ficheiro urls.py com os seguintes comandos:
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-iis"></a>Configurar o IIS
1. No ficheiro applicationHost. config global, desbloquear a secção de processadores.  Isto permite que o ficheiro Web. config utilizar o processador de Python. Adicione este comando:
   
        %windir%\system32\inetsrv\appcmd unlock config -section:system.webServer/handlers
2. Ative WFastCGI. Esta ação adiciona uma aplicação ao ficheiro applicationHost. config global, que se refere-se a sua interpretador Python executável e o script de wfastcgi.py.
   
    No Python 2.7:
   
        C:\python27\scripts\wfastcgi-enable
   
    No Python 3.4:
   
        C:\python34\scripts\wfastcgi-enable
3. C:\inetpub\wwwroot\helloworld, crie um ficheiro Web. config. O valor da `scriptProcessor` atributo deve corresponder a saída do passo anterior. Para obter mais informações sobre a definição de wfastcgi, consulte [pypi wfastcgi][wfastcgi].
   
   No Python 2.7:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python27\python.exe|C:\Python27\Lib\site-packages\wfastcgi.pyc" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
   
   No Python 3.4:
   
        <configuration>
          <appSettings>
            <add key="WSGI_HANDLER" value="django.core.handlers.wsgi.WSGIHandler()" />
            <add key="PYTHONPATH" value="C:\inetpub\wwwroot\helloworld" />
            <add key="DJANGO_SETTINGS_MODULE" value="helloworld.settings" />
          </appSettings>
          <system.webServer>
            <handlers>
                <add name="Python FastCGI" path="*" verb="*" modules="FastCgiModule" scriptProcessor="C:\Python34\python.exe|C:\Python34\Lib\site-packages\wfastcgi.py" resourceType="Unspecified" />
            </handlers>
          </system.webServer>
        </configuration>
4. Atualize a localização do Web site predefinido do IIS para apontar para a pasta do projeto Django:
   
        %windir%\system32\inetsrv\appcmd set vdir "Default Web Site/" -physicalPath:"C:\inetpub\wwwroot\helloworld"
5. Carregar a página Web no browser.

![Uma janela do browser apresenta a página Olá, mundo no Azure][1]

## <a name="shut-down-your-azure-virtual-machine"></a>Encerre a máquina virtual do Azure
Quando tiver terminado com este tutorial, recomendamos que encerre ou remova a VM do Azure que criou para o tutorial. Esta ação liberta recursos para outros tutoriais e pode evitar incorrer em custos de utilização do Azure.

[1]: ./media/python-django-web-app/django-helloworld-browser-azure.png

[port80]: ./media/python-django-web-app/django-helloworld-port80.png

[Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[python.org]: https://www.python.org/downloads/
[wfastcgi]: https://pypi.python.org/pypi/wfastcgi
