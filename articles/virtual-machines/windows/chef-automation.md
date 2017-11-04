---
title: "Implementação de máquina virtual do Azure com Chef | Microsoft Docs"
description: "Saiba como utilizar Chef para efetuar a implementação da máquina de virtual automatizada e a configuração no Microsoft Azure"
services: virtual-machines-windows
documentationcenter: 
author: diegoviso
manager: timlt
tags: azure-service-management,azure-resource-manager
editor: 
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: diviso
ms.openlocfilehash: b6db0fbb4e0de896994954974ddcc39daad9c125
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizar a implementação de máquinas virtuais do Azure com o Chef
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Chef é uma ótima ferramenta para entrega de automatização e pretender configurações de estado.

Com a nossa versão mais recente da api de nuvem, Chef fornece integração totalmente integrada com o Azure, que lhe confere a capacidade de aprovisionar e implementar os Estados de configuração através de um único comando.

Neste artigo, posso irá mostrar-lhe como configurar o ambiente de Chef para aprovisionar máquinas virtuais do Azure e guiá-lo através de criar uma política ou "Manual" e, em seguida, implementar este manual para uma máquina virtual do Azure.

Vamos começar!

## <a name="chef-basics"></a>Noções básicas de chef
Antes de começar, posso sugerimos que reveja os conceitos básicos do Chef. Há uma grande material <a href="http://www.chef.io/chef" target="_blank">aqui</a> e recomenda se tiver uma leitura rápida antes de tentar esta explicação passo a passo. No entanto, posso será recap as noções básicas antes Vamos começar.

O diagrama seguinte ilustra uma arquitetura de alto nível Chef.

![][2]

Chef tem três componentes principais de arquitetura: servidor Chef, Chef cliente (nó) e Chef estação de trabalho.

O servidor de Chef é nossa ponto de gestão e existem duas opções para o servidor de Chef: uma solução alojada ou uma solução no local. Vamos utilizar uma solução alojada.

O cliente Chef (nó) é o agente que se encontra em servidores que está a gerir.

A estação de trabalho Chef é nossa estação de trabalho do administrador onde iremos criar nossas políticas e executar nosso comandos de gestão. Iremos executar o **knife** comando da estação de trabalho Chef para gerir a nossa infraestrutura.

Também é o conceito de "Cookbooks" e "Receitas". Estes são eficazmente as políticas que definir e aplicar aos nossos servidores.

## <a name="preparing-the-workstation"></a>A preparar a estação de trabalho
Em primeiro lugar, permite prep a estação de trabalho. Estou a utilizar uma estação de trabalho do Windows padrão. É necessário criar um diretório para armazenar a nossa cookbooks e ficheiros de configuração.

Em primeiro lugar, crie um diretório denominado C:\chef.

Em seguida, crie um diretório segundo chamado c:\chef\cookbooks.

Agora é necessário transferir o nosso ficheiro de definições do Azure para que Chef possa comunicar com a nossa subscrição do Azure.

<!--Download your publish settings from [here](https://manage.windowsazure.com/publishsettings/).-->
Transferir a publicar as definições utilizando o PowerShell Azure [Get-AzurePublishSettingsFile](https://docs.microsoft.com/en-us/powershell/module/azure/get-azurepublishsettingsfile?view=azuresmps-4.0.0) comando. 

Guarde o ficheiro de definições de publicação na C:\chef.

## <a name="creating-a-managed-chef-account"></a>Criar uma conta de Chef gerida
Inscreva-se uma conta de Chef alojada [aqui](https://manage.chef.io/signup).

Durante o processo de inscrição, será pedido para criar uma nova organização.

![][3]

Quando é criado a sua organização, transfira o starter kit.

![][4]

> [!NOTE]
> Receberá uma mensagem de aviso, que serão repostas as chaves, há problema para continuar, não temos de nenhuma infraestrutura existente como ainda configurada.
> 
> 

Este ficheiro de zip do kit de arranque contém os ficheiros de configuração da organização e as chaves.

## <a name="configuring-the-chef-workstation"></a>Configurar a estação de trabalho Chef
Extraia o conteúdo de chef-starter.zip para C:\chef.

Copiar todos os ficheiros em repo do starter\chef chef\.chef c:\chef no diretório.

O diretório deve agora ter um aspeto semelhante ao seguinte exemplo.

![][5]

Agora, deve ter quatro ficheiros, incluindo o ficheiro de publicação do Azure na raiz da c:\chef.

Os ficheiros PEM contêm sua organização e chaves privadas de administração para a comunicação enquanto o ficheiro de knife.rb contém a configuração de knife. Precisamos de editar o ficheiro knife.rb.

Abra o ficheiro no seu editor escolhidas e modificar "cookbook_path" removendo a /... do caminho para aparece conforme mostrado seguinte.

    cookbook_path  ["#{current_dir}/cookbooks"]

Também adicione a seguinte linha ao refletir o nome do seu Azure publicar o ficheiro de definições.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

O ficheiro knife.rb deve agora ter um aspeto semelhante ao seguinte exemplo.

![][6]

Estas linhas irão garantir que referencia o diretório de cookbooks sob c:\chef\cookbooks Knife e também utiliza o nosso ficheiro de definições de publicação do Azure durante as operações do Azure.

## <a name="installing-the-chef-development-kit"></a>Instalar o Kit de desenvolvimento Chef
Seguinte [transferir e instalar](http://downloads.getchef.com/chef-dk/windows) o ChefDK (Chef Development Kit) para configurar a sua estação de trabalho Chef.

![][7]

Instale a localização predefinida da c:\opscode. Esta instalação irá demorar cerca de 10 minutos.

Confirme a que sua variável de caminho contém entradas para C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se não forem existir, certifique-se de que adicionar estes caminhos!

*TENHA EM ATENÇÃO DE QUE A ORDEM DO CAMINHO É IMPORTANTE!* Se os caminhos de opscode não estão na ordem correta, terá de problemas.

Reinicie a estação de trabalho antes de continuar.

Em seguida, iremos irá instalar a extensão de Knife Azure. Esta opção fornece Knife com o "Azure Plug-in".

Execute o seguinte comando.

    chef gem install knife-azure ––pre

> [!NOTE]
> O argumento de pre-garante que está a receber mais recente versão RC do plug-in de Azure Knife que fornece acesso ao conjunto de APIs mais recente.
> 
> 

É provável que também irá ser instalado um número de dependências ao mesmo tempo.

![][8]

Para garantir que tudo está configurado corretamente, execute o seguinte comando.

    knife azure image list

Se está tudo configurado corretamente, verá uma lista de deslocamento de imagens do Azure disponíveis através do.

Parabéns! A estação de trabalho está configurada!

## <a name="creating-a-cookbook"></a>Criar um Cookbook
Um manual é utilizado pelo Chef para definir um conjunto de comandos que pretende executar no seu cliente gerido. Criar um Cookbook é simples e utilizamos o **chef gerar cookbook** comando para gerar o nosso modelo Cookbook. Posso irá chamar meu servidor de web Cookbook como gostaria de uma política que implementa automaticamente o IIS.

No seu diretório C:\Chef execute o seguinte comando.

    chef generate cookbook webserver

Esta ação irá gerar um conjunto de ficheiros no diretório de C:\Chef\cookbooks\webserver. Agora é preciso definir o conjunto de comandos que Gostaríamos nosso cliente Chef a executar no nosso máquina virtual gerida.

Os comandos são armazenados no default.rb ficheiro. Neste ficheiro, posso irá definir um conjunto de comandos que instala o IIS, inicia o IIS e copia um ficheiro de modelo para a pasta de wwwroot.

Modificar o ficheiro C:\chef\cookbooks\webserver\recipes\default.rb e adicione as seguintes linhas.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Quando tiver terminado, guarde o ficheiro.

## <a name="creating-a-template"></a>Criar um modelo
Como foi mencionado anteriormente, é necessário gerar um ficheiro de modelo que será utilizado como a nossa página default.html.

Execute o seguinte comando para gerar o modelo.

    chef generate template webserver Default.htm

Agora, navegue para o ficheiro de C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Edite o ficheiro ao adicionar algum código "Olá mundo" HTML simple e, em seguida, guarde o ficheiro.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Carregar o Cookbook para o servidor de Chef
Neste passo, vamos são colocar uma cópia manual que foi criado no nosso computador local e carregá-lo para o servidor de alojado Chef. Assim que seja carregado, o Cookbook será apresentado sob o **política** separador.

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Implementar uma máquina virtual com o Knife Azure
Iremos agora implementar uma máquina virtual do Azure e aplicar a Cookbook "Webserver", que irá instalar o nosso IIS predefinido e do serviço web página web.

Para tal, utilize o **knife azure servidor criar** comando.

Estou exemplo do comando apresentada a seguir.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Os parâmetros são facilmente compreensíveis. Substitua as variáveis específicas e executar.

> [!NOTE]
> Através da linha de comandos, posso estou também automatizar meu regras de filtro de rede do ponto final utilizando o parâmetro – tcp-pontos finais. Tiver abrir a cópia de segurança as portas 80 e 3389 para fornecer acesso a minha página web e a sessão do RDP.
> 
> 

Depois de executar o comando, aceda ao portal do Azure e irá ver o seu computador começar a aprovisionar.

![][13]

A linha de comandos apresentada a seguir.

![][10]

Assim que a implementação estiver concluída, iremos deverá conseguir ligar ao serviço web através da porta 80, iremos tinha abrir a porta quando é aprovisionado a máquina virtual com o comando Knife Azure. Dado que esta máquina virtual está a máquina virtual apenas no meu serviço de nuvem, irá ligá-lo com o url do serviço em nuvem.

![][11]

Como pode ver, posso obteve criativos com o meu código HTML.

Não se esqueça de que também pode ligar através de uma sessão do RDP do portal do Azure através de porta 3389.

Posso hope que isto foi útil! Aceda e começar a sua infraestrutura como journey de código com o Azure hoje em dia!

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png


<!--Link references-->
