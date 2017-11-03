---
title: "Implementar LAMP numa máquina virtual com Linux no Azure | Microsoft Docs"
description: Tutorial - instale a pilha LAMP numa VM com Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 08/03/2017
ms.author: danlep
ms.openlocfilehash: c00e6a190633348411f47490808739d570cafd69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-lamp-web-server-on-an-azure-vm"></a>Instalar um servidor de web LAMP numa VM do Azure
Este artigo explica como implementar um servidor web Apache, o MySQL e o PHP (pilha LAMP) numa VM com Ubuntu no Azure. Se preferir o servidor de web NGINX, consulte o [pilha LEMP](tutorial-lemp-stack.md) tutorial. Para ver o servidor LAMP em ação, opcionalmente, pode instalar e configurar um site WordPress. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu (o ' L' na pilha de LAMP)
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, MySQL e PHP
> * Certifique-se a instalação e configuração
> * Instalar o WordPress no servidor LAMP


Para obter mais informações sobre a pilha LAMP, incluindo as recomendações para um ambiente de produção, consulte o [Ubuntu documentação](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Instalar o Apache, MySQL e PHP

Execute o seguinte comando para atualizar as origens de pacote Ubuntu e instalar Apache, MySQL e PHP. Tenha em atenção o acento circunflexo (^) no final do comando.


```bash
sudo apt update && sudo apt install lamp-server^
```



Lhe for pedido para instalar os pacotes e outras dependências. Quando lhe for pedido, definir uma palavra-passe de raiz para MySQL e, em seguida, [Enter] para continuar. Siga as instruções restantes. Este processo instala as extensões PHP necessárias mínimas necessárias para utilizar o PHP com MySQL. 

![Página de palavra-passe de raiz de MySQL][1]

## <a name="verify-installation-and-configuration"></a>Certifique-se a instalação e configuração


### <a name="apache"></a>Apache

Verifica a versão do Apache com o seguinte comando:
```bash
apache2 -v
```

Com Apache instalado e a porta 80 aberto à sua VM, o servidor web agora pode ser acedido a partir da internet. Para ver a página predefinida do Apache2 Ubuntu, abra um browser e introduza o endereço IP público da VM. Utilize o endereço IP público que utilizou para SSH para a VM:

![Página do Apache predefinida][3]


### <a name="mysql"></a>MySQL

Verificar a versão do MySQL com o seguinte comando (tenha em atenção o capital `V` parâmetro):

```bash
mysql -V
```

Recomendamos a executar o script seguinte para ajudar a proteger a instalação do MySQL:

```bash
mysql_secure_installation
```

Introduza a palavra-passe de raiz do MySQL e configurar as definições de segurança para o seu ambiente.

Se pretender criar uma base de dados MySQL, adicionar utilizadores ou alterar as definições de configuração, no início de sessão MySQL:

```bash
mysql -u root -p
```

Quando terminar, sair da linha de comandos da mysql, escrevendo `\q`.

### <a name="php"></a>PHP

Verifique a versão do PHP com o seguinte comando:

```bash
php -v
```

Se pretender testar adicional, crie uma página de informações PHP rápida para ver num browser. O comando seguinte cria a página de informações do PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Agora pode verificar a página de informações do PHP que criou. Abra um browser e aceda a `http://yourPublicIPAddress/info.php`. Substitua o endereço IP público da sua VM. Deve ser semelhante a esta imagem.

![Página de informações do PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementado um servidor LAMP no Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu
> * Abrir a porta 80 para o tráfego da Web
> * Instalar o Apache, MySQL e PHP
> * Certifique-se a instalação e configuração
> * Instalar o WordPress no servidor LAMP

Avançar para o próximo tutorial para saber como proteger servidores web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor web com SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png