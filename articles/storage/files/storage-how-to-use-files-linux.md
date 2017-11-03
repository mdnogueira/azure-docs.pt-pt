---
title: Utilizar os ficheiros do Azure com o Linux | Microsoft Docs
description: "Saiba como montar uma partilha de ficheiros do Azure através de SMB no Linux."
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 192680efe07368666c5a9d037549c7686189d0b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-files-with-linux"></a>Utilizar os ficheiros do Azure com o Linux
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. Partilhas de ficheiros do Azure podem ser montadas nas distribuições do Linux utilizando o [cifs utils pacote](https://wiki.samba.org/index.php/LinuxCIFS_utils) do [projeto Samba](https://www.samba.org/). Este artigo mostra duas formas de montar uma partilha de ficheiros do Azure: a pedido com o `mount` comando e de arranque através da criação de uma entrada no `/etc/fstab`.

> [!NOTE]  
> Para montar uma partilha de ficheiros de Azure fora do Azure está alojado, tal como no local ou numa região diferente do Azure, SO de região tem de suportar a funcionalidade de encriptação do SMB 3.0. Funcionalidade de encriptação para SMB 3.0 para Linux foi introduzida no 4.11 kernel. Esta funcionalidade permite a montagem do Azure da partilha de ficheiros no local ou uma região do Azure diferente. No momento da publicação, esta funcionalidade foi backported para Ubuntu 16.04 e acima.


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Partilhar Prerequisities para montar um ficheiro do Azure com o Linux e o pacote de cifs utils
* **Escolha uma distribuição de Linux que pode ter o pacote de cifs utils instalado**: a Microsoft recomenda as distribuições de Linux seguintes na galeria da imagem do Azure:

    * Ubuntu Server 14.04 +
    * RHEL 7 +
    * CentOS 7 +
    * Debian 8
    * openSUSE 13.2 +
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**O pacote de cifs utils está instalado**: cifs-utils pode ser instalado utilizando o Gestor de pacotes de distribuição de Linux da sua preferência. 

    No **Ubuntu** e **baseado em Debian** distribuições, utilize o `apt-get` Gestor de pacotes:

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    No **RHEL** e **CentOS**, utilize o `yum` Gestor de pacotes:

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    No **openSUSE**, utilize o `zypper` Gestor de pacotes:

    ```
    sudo zypper install samba*
    ```

    No outras distribuições, utilize o Gestor de pacotes apropriada ou [compilar a partir da origem](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Opte por utilizar as permissões do diretório/ficheiro da partilha montada**: os exemplos abaixo, utilizamos 0777, para permitir leitura, escrita e de executar permissões a todos os utilizadores. Pode ser substituído com outros [chmod permissões](https://en.wikipedia.org/wiki/Chmod) conforme pretendido. 

* **Nome da Conta de Armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa do nome da conta de armazenamento.

* **Chave da Conta de Armazenamento**: para montar uma partilha de Ficheiros do Azure, precisa da chave de armazenamento primária (ou secundária). Atualmente, não são suportadas chaves SAS para a montagem.

* **Certifique-se de que a porta 445 está aberta**: SMB comunica através da porta TCP 445 - verificar ver se a firewall não está a bloquear TCP portas 445 do computador cliente.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Montar a ficheiro do Azure partilha a pedido com`mount`
1. **[Instalar o pacote de cifs utils para a distribuição de Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: Isto pode ser efetuado em qualquer local no sistema de ficheiros.

    ```
    mkdir mymountpoint
    ```

3. **Utilize o comando de montagem para montar a partilha de ficheiros do Azure**: não se esqueça de substituir `<storage-account-name>`, `<share-name>`, e `<storage-account-key>` com as informações corretas.

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Quando tiver terminado a utilizar a partilha de ficheiros do Azure, pode utilizar `sudo umount ./mymountpoint` desmontar a partilha.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Criar um ponto de montagem persistente para a partilha de ficheiros do Azure com o`/etc/fstab`
1. **[Instalar o pacote de cifs utils para a distribuição de Linux](#install-cifs-utils)**.

2. **Crie uma pasta para o ponto de montagem**: Isto pode ser efetuado em qualquer local no sistema de ficheiros, mas tem de ter em atenção o caminho absoluto da pasta. O exemplo seguinte cria uma pasta na raiz.

    ```
    sudo mkdir /mymountpoint
    ```

3. **Utilize o seguinte comando para acrescentar a linha seguinte ao `/etc/fstab`** : não se esqueça de substituir `<storage-account-name>`, `<share-name>`, e `<storage-account-key>` com as informações corretas.

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=2.1,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> Pode utilizar `sudo mount -a` para montar a partilha de ficheiros do Azure após a edição `/etc/fstab` em vez de ser reiniciado.

## <a name="feedback"></a>Comentários
Utilizadores do Linux, Queremos ouvi da!

Os ficheiros do Azure para o grupo de utilizadores do Linux fornece um fórum para partilhar os seus comentários à medida que avalia e que adotar o armazenamento de ficheiros no Linux. E-mail [Azure ficheiros Linux utilizadores](mailto:azurefileslinuxusers@microsoft.com) para aderir a grupo dos utilizadores.

## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.
* [File Service REST API reference (Referência da API REST do Serviço do Ficheiros)](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [Como utilizar o AzCopy com armazenamento do Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilizar a CLI do Azure com o storage do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [FAQ](../storage-files-faq.md)
* [Resolução de problemas](storage-troubleshoot-linux-file-connection-problems.md)
