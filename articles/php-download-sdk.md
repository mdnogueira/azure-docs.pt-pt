---
title: Transfira o Azure SDK para PHP
description: Saiba como transferir e instalar o Azure SDK para PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: 
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: fd3d28b133ef8e646f5c2f1c1127f654daa61b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP
## <a name="overview"></a>Descrição geral
O Azure SDK para PHP inclui os componentes que lhe permitem desenvolver, implementar e gerir aplicações do PHP do Azure. Especificamente, o Azure SDK para PHP inclui o seguinte:

* **As bibliotecas de cliente do PHP do Azure**. Estas bibliotecas de classes facultar uma interface para aceder às funcionalidades do Azure, como os serviços de gestão de dados e serviços em nuvem.  
* **A Interface de linha de comandos do Azure para Mac, Linux e Windows (CLI do Azure)**. Este é um conjunto de comandos para implementar e gerir serviços do Azure, tais como Web sites do Azure e Virtual Machines do Azure. O trabalho CLI do Azure em qualquer plataforma, incluindo Mac, Linux e Windows.
* **O Azure PowerShell (apenas Windows)**. Este é um conjunto de cmdlets do PowerShell para implementar e gerir serviços do Azure, tais como serviços em nuvem e máquinas virtuais.
* **Os emuladores do Azure (apenas Windows)**. Os armazenamento e computação emuladores são emuladores locais dos serviços de nuvem e de serviços de gestão de dados que lhe permitem testar uma aplicação localmente. O Azure emuladores são executados no Windows apenas.

As secções abaixo descrevem como transferir e instalar os componentes descritos acima.

As instruções neste tópico partem do princípio de que tem [PHP] [ install-php] instalado.

> [!NOTE]
> Tem de ter o PHP 5.5 ou superior para utilizar as bibliotecas de cliente do PHP do Azure.
> 
> 

## <a name="php-client-libraries-for-azure"></a>Bibliotecas de cliente PHP para Azure
As bibliotecas de cliente do PHP do Azure fornecem uma interface para aceder a funcionalidades do Azure, como os serviços de gestão de dados e serviços em nuvem, de qualquer sistema operativo. Estas bibliotecas podem ser instaladas através do compositor.

Para obter informações sobre como utilizar as bibliotecas de cliente do PHP do Azure, consulte [como utilizar o serviço Blob][blob-service], [como utilizar o serviço tabela] [ table-service] e [como utilizar o serviço fila][queue-service].

### <a name="install-via-composer"></a>Instalar através do compositor
1. [Instalar o Git][install-git].

    > [AZURE.NOTE] No Windows, também terá de adicionar o executável de Git para a variável de ambiente PATH.

1. Crie um ficheiro denominado **Composer** na raiz do projeto e adicione o seguinte código:
   
        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }
2. Transferir  **[composer.phar] [ composer-phar]**  na raiz do projeto.
3. Abra uma linha de comandos e execute este na raiz do projeto
   
        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>O Azure PowerShell e emuladores do Azure
O Azure PowerShell é um conjunto de cmdlets do PowerShell para implementar e gerir serviços do Azure (por exemplo, serviços em nuvem e máquinas virtuais). O Azure emuladores são emuladores dos serviços de nuvem e de serviços de gestão de dados que lhe permitem testar uma aplicação localmente. Estes componentes são suportados apenas o Windows.

A forma recomendada para instalar o Azure PowerShell e o Azure emuladores consiste em utilizar o [instalador de plataforma Web Microsoft][download-wpi]. Tenha em atenção que também pode optar por instalar outros componentes de desenvolvimento, tais como o PHP, SQL Server, o Drivers Microsoft para SQL Server para o PHP e o WebMatrix.

Para obter informações sobre como utilizar o Azure PowerShell, consulte [como utilizar o Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>CLI do Azure
A CLI do Azure é um conjunto de comandos para implementar e gerir serviços do Azure, tais como Web sites do Azure e Virtual Machines do Azure. Para obter informações sobre como instalar a CLI do Azure, consulte [instalar a CLI do Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para programadores do PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
