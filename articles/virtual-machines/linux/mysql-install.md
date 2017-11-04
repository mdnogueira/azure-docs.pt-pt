---
title: Configurar o MySQL numa VM com Linux no Azure | Microsoft Docs
description: "Saiba como instalar a pilha de MySQL numa máquina virtual (Ubuntu ou VM de RedHat família de SO) do Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
ms.openlocfilehash: 0ee70bda954cf0a193d43b5b47702e7b2c37844d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-install-mysql-on-azure"></a>Como instalar o MySQL no Azure
Neste artigo, ficará a saber como instalar e configurar o MySQL numa máquina virtual do Azure com o Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>Instalar o MySQL na sua máquina virtual
> [!NOTE]
> Já tem de ter uma máquina virtual de Microsoft Azure com o Linux para concluir este tutorial. Consulte o [tutorial da VM do Linux do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para criar e configurar uma VM com Linux com `mysqlnode` como o nome da VM e `azureuser` como utilizador antes de continuar.
> 
> 

Neste caso, utilize 3306 porta como a porta de MySQL.  

Estabelecer ligação com o Linux VM criada através do putty. Se esta for a primeira vez que utiliza a VM do Linux do Azure, consulte como utilizar o putty ligar a uma VM com Linux [aqui](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Utilizaremos o pacote do repositório para instalar MySQL5.6 como um exemplo neste artigo. Na realidade, MySQL5.6 tem mais melhoramento do desempenho que MySQL5.5.  Obter mais informações [aqui](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).

### <a name="how-to-install-mysql56-on-ubuntu"></a>Como instalar MySQL5.6 no Ubuntu
Utilizaremos VM com Linux com Ubuntu a partir do Azure aqui.

* Passo 1: Instalar MySQL servidor 5.6 comutador para `root` utilizador:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Instale servidor de mysql 5.6:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    Durante a instalação, verá uma caixa de diálogo janela poping até pedir-lhe definir o MySQL palavra-passe raiz abaixo e precisa de definir a palavra-passe aqui.
  
    ![Imagem](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    Introduza a palavra-passe novamente para confirmar.

    ![Imagem](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* Passo 2: Início de sessão MySQL servidor
  
    Quando terminar a instalação do servidor do MySQL, o serviço de MySQL será iniciado automaticamente. Pode iniciar sessão no servidor de MySQL com `root` utilizador.
    Utilize o comando para início de sessão e a entrada de palavra-passe abaixo.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* Passo 3: Gerir o serviço de MySQL em execução
  
    (a) obter estado do serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) iniciar o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) parar o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) reinicie o serviço de MySQL
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Como instalar o MySQL Red Hat família de SO como CentOS, Oracle Linux
Utilizaremos VM com Linux com CentOS ou Oracle Linux aqui.

* Passo 1: Adicione o repositório de MySQL Yum comutador para `root` utilizador:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    Transfira e instale o pacote de versão MySQL:
  
            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* Passo 2: Edite abaixo ficheiro para ativar o repositório de MySQL para transferir o pacote de MySQL5.6.
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    Atualize cada valor deste ficheiro para abaixo:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* Passo 3: Instalar MySQL do repositório de MySQL MySQL instalar:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    Pacote MySQL RPM e todos os pacotes relacionados serão instalados.
* Passo 4: Gerir o serviço de MySQL em execução
  
    (a) verificar o estado do serviço do servidor MySQL:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) verifique se o servidor de porta de MySQL predefinido está em execução:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) inicie o servidor de MySQL:

           #[root@mysqlnode ~]#service mysqld start

    (d) pare o servidor de MySQL:

           #[root@mysqlnode ~]#service mysqld stop

    (e) MySQL definido para iniciar quando a cópia de arranque do sistema:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>Como instalar o MySQL no SUSE Linux
Utilizaremos VM com Linux com OpenSUSE aqui.

* Passo 1: Transferir e instalar o servidor de MySQL
  
    Mudar para `root` utilizador através do comando abaixo:  
  
           #sudo su -
  
    Transfira e instale o pacote de MySQL:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* Passo 2: Gerir o serviço de MySQL em execução
  
    (a) verificar o estado do servidor MySQL:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) verifique se a porta predefinida do servidor MySQL:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) inicie o servidor de MySQL:

           #[root@mysqlnode ~]# rcmysql start

    (d) pare o servidor de MySQL:

           #[root@mysqlnode ~]# rcmysql stop

    (e) MySQL definido para iniciar quando a cópia de arranque do sistema:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>Passo seguinte
Localizar mais informações sobre o MySQL e utilização [aqui](https://www.mysql.com/).

