---
title: "Utilizar a extensão CustomScript uma VM com Linux | Microsoft Docs"
description: "Saiba como utilizar a extensão CustomScript para implementar aplicações em computadores virtuais Linux no Azure criado com o modelo de implementação clássica."
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: guybo
ms.openlocfilehash: cb1fc9a44dc9e57d9cc9f1c546ad937d67e63c2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Implementar uma aplicação LAMP utilizando a Extensão Azure CustomScript para Linux
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre a implementação de uma pilha LAMP utilizando o modelo do Resource Manager, consulte [aqui](../tutorial-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O Microsoft Azure a extensão CustomScript para Linux fornece uma forma de personalizar as máquinas virtuais (VMs) ao executar código arbitrário escrito em qualquer linguagem de scripts suportada pela VM (por exemplo, Python e Bash). Isto fornece uma forma muito flexível para automatizar a implementação de aplicação para várias máquinas.

Pode implementar a extensão CustomScript no portal do Azure, do Windows PowerShell ou a Interface de linha de comandos do Azure (CLI do Azure).

Neste artigo, que vamos utilizar a CLI do Azure para implementar uma aplicação LAMP simples para uma VM com Ubuntu criadas com o modelo de implementação clássica.

## <a name="prerequisites"></a>Pré-requisitos
Neste exemplo, primeiro crie duas VMs do Azure com Ubuntu 14.04 ou posterior. As VMs são denominadas *script vm* e *lamp vm*. Utilize nomes exclusivos quando criar as VMs. Um é utilizado para executar os comandos da CLI e um é utilizado para implementar a aplicação LAMP.

Também precisa de uma conta de armazenamento do Azure e uma chave para aceder à mesma (pode obter esta do portal do Azure).

Se precisar de obter ajuda na criação de VMs com Linux no Azure, consulte a [criar com uma Máquina Virtual a executar Linux](createportal.md).

Os comandos de instalação partem do princípio de Ubuntu, mas pode adaptar a instalação de qualquer distro suportada do Linux.

A VM de vm de script tem de ter a CLI do Azure instalado, com uma ligação de trabalho para o Azure. Para obter ajuda com esta opção, consulte [instale e Configure a Interface de linha de comandos do Azure](../../../cli-install-nodejs.md).

## <a name="upload-a-script"></a>Carregar um script
A extensão CustomScript iremos utilizar para executar um script numa VM remota para instalar a pilha LAMP e criar uma página PHP. Para poder aceder ao script a partir de qualquer lugar, irá carregá-lo como um blob do Azure.

### <a name="script-overview"></a>Descrição geral de script
O exemplo de script instala uma pilha LAMP Ubuntu (incluindo como configurar uma instalação automática de MySQL), escreve um ficheiro simples do PHP e inicia o Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Carregar o script
Guarde o script como um ficheiro de texto, por exemplo *install_lamp.sh*e, em seguida, carregue-o ao Storage do Azure. Pode fazê-facilmente com a CLI do Azure. O exemplo seguinte carrega o ficheiro para um contentor de armazenamento com o nome "scripts". Se não existir o contentor terá de criá-la primeiro.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Também crie um ficheiro JSON que descreve como transferir o script do armazenamento do Azure. Guarde-o como *public_config.json* (substituir "mystorage" com o nome da conta de armazenamento):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Implementar a extensão
Agora pode utilizar o seguinte comando para implementar a extensão CustomScript do Linux para a VM remota utilizando a CLI do Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

O comando anterior transfere e executa o *install_lamp.sh* script na VM chamada *lamp vm*.

Uma vez que a aplicação inclui um servidor web, lembre-se de abrir uma porta de escuta de HTTP na VM remota com o comando seguinte.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Monitorização e resolução de problemas
Pode verificar na forma de script personalizado é executada ao observar o ficheiro de registo na VM remoto. SSH para *lamp vm* e tail o ficheiro de registo com o comando seguinte.

    cd /var/log/azure/customscript
    tail -f handler.log

Depois de executar a extensão CustomScript, pode navegar para a página PHP que criou para obter informações. A página do PHP para o exemplo neste artigo é *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Recursos adicionais
Pode utilizar os mesmos passos básicos para implementar aplicações mais complexas. Neste exemplo, o script de instalação foi guardado como um blob no Storage do Azure público. Uma opção mais segura seria armazenar o script de instalação como um blob seguro com um [assinatura de acesso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Recursos adicionais para a CLI do Azure, Linux e a extensão CustomScript estão listados em seguida.

[Automatizar tarefas de personalização de VM com Linux com a extensão CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensões de Linux do Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)