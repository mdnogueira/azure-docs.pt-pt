---
title: "Implementar LEMP numa máquina virtual com Linux no Azure | Microsoft Docs"
description: Tutorial - instale a pilha LEMP numa VM com Linux no Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: c77cd0148a7e3e7b99e90e29bc1499dae8f95028
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="install-a-lemp-web-server-on-an-azure-vm"></a>Instalar um servidor de web LEMP numa VM do Azure
Este artigo explica como implementar um servidor web NGINX, o MySQL e o PHP (pilha LEMP) numa VM com Ubuntu no Azure. A pilha LEMP é uma alternativa ao populares [pilha LAMP](tutorial-lamp-stack.md), que também pode instalar no Azure. Para ver o servidor LEMP em ação, opcionalmente, pode instalar e configurar um site WordPress. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu (o ' L' na pilha de LEMP)
> * Abrir a porta 80 para o tráfego da Web
> * Instalar NGINX, MySQL e PHP
> * Certifique-se a instalação e configuração
> * Instalar o WordPress no servidor LEMP


Esta configuração é para testes rápidos ou prova de conceito.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Instalar NGINX, MySQL e PHP

Execute o seguinte comando para atualizar as origens de pacote Ubuntu e instalar NGINX, MySQL e PHP. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Lhe for pedido para instalar os pacotes e outras dependências. Quando lhe for pedido, definir uma palavra-passe de raiz para MySQL e, em seguida, [Enter] para continuar. Siga as instruções restantes. Este processo instala as extensões PHP necessárias mínimas necessárias para utilizar o PHP com MySQL. 

![Página de palavra-passe de raiz de MySQL][1]

## <a name="verify-installation-and-configuration"></a>Certifique-se a instalação e configuração


### <a name="nginx"></a>NGINX

Verifica a versão do NGINX com o seguinte comando:
```bash
nginx -v
```

Com NGINX instalado e a porta 80 aberto à sua VM, o servidor web agora pode ser acedido a partir da internet. Para ver a página de boas-vindas NGINX, abra um browser e introduza o endereço IP público da VM. Utilize o endereço IP público que utilizou para SSH para a VM:

![Página predefinida NGINX][3]


### <a name="mysql"></a>MySQL

Verificar a versão do MySQL com o seguinte comando (tenha em atenção o capital `V` parâmetro):

```bash
mysql -V
```

Para ajudar a proteger a instalação do MySQL, execute o `mysql_secure_installation` script. Se estiver apenas a configurar um servidor temporário, pode ignorar este passo. 

```bash
mysql_secure_installation
```

Introduza uma palavra-passe de raiz para MySQL e configurar as definições de segurança para o seu ambiente.

Se pretender experimentar funcionalidades MySQL (criar uma base de dados MySQL, adicionar utilizadores ou alterar as definições de configuração), no início de sessão MySQL. Este passo não é necessário para concluir este tutorial. 


```bash
mysql -u root -p
```

Quando terminar, sair da linha de comandos da mysql, escrevendo `\q`.

### <a name="php"></a>PHP

Verifique a versão do PHP com o seguinte comando:

```bash
php -v
```

Configure NGINX para utilizar o Gestor de processo FastCGI PHP (PHP FPM). Execute os seguintes comandos para cópia de segurança do ficheiro de configuração do bloco de servidor NGINX original e, em seguida, edite o ficheiro original num editor à sua escolha:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

No editor, substitua os conteúdos de `/etc/nginx/sites-available/default` com o seguinte. Veja os comentários para explicação sobre as definições. Substituir o endereço IP público da sua VM para *yourPublicIPAddress*e deixe as restantes definições. Em seguida, guarde o ficheiro.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Verifique a configuração de NGINX erros de sintaxe:

```bash
sudo nginx -t
```

Se a sintaxe correta, reinicie NGINX com o seguinte comando:

```bash
sudo service nginx restart
```

Se pretender testar adicional, crie uma página de informações PHP rápida para ver num browser. O comando seguinte cria a página de informações do PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Agora pode verificar a página de informações do PHP que criou. Abra um browser e aceda a `http://yourPublicIPAddress/info.php`. Substitua o endereço IP público da sua VM. Deve ser semelhante a esta imagem.

![Página de informações do PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementado um servidor LEMP no Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM com Ubuntu
> * Abrir a porta 80 para o tráfego da Web
> * Instalar NGINX, MySQL e PHP
> * Certifique-se a instalação e configuração
> * Instalar o WordPress na pilha LEMP

Avançar para o próximo tutorial para saber como proteger servidores web com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor web com SSL](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
