---
title: Utilizar chaves SSH com o Windows para VMs com Linux | Microsoft Docs
description: "Saiba como gerar e utilizar chaves SSH num computador Windows para ligar a uma máquina virtual do Linux no Azure."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: 66837a3a153cda041f5351c52c8ccb1f8ccfea50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar o SSH chaves com Windows no Azure
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

Ao ligar a máquinas de virtuais (VMs) com Linux no Azure, deve utilizar [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography) para fornecer uma forma mais segura para iniciar sessão na VM com Linux. Este processo envolve uma troca de chaves pública e privada utilizando o comando de secure shell (SSH) para autenticar si em vez de um nome de utilizador e palavra-passe. As palavras-passe estão vulneráveis a ataques, especialmente em VMs de acesso à Internet, tais como servidores web de força bruta. Este artigo fornece uma descrição geral de chaves SSH e como gerar as chaves adequadas num computador Windows.

## <a name="overview-of-ssh-and-keys"></a>Descrição geral de SSH e chaves
Em segurança pode iniciar sessão na VM com Linux através da utilização de chaves públicas e privadas:

* O **chave pública** é colocada na VM com Linux ou qualquer outro serviço que pretende utilizar com a criptografia de chave pública.
* O **chave privada** é o que lhe está presente na VM com Linux quando iniciar sessão, para verificar a sua identidade. Proteja esta chave privada. Não a partilhe.

Estas chaves públicas e privadas podem ser utilizados em várias VMs e serviços. Não é necessário um par de chaves para cada VM ou serviço que pretende aceder. Para obter uma descrição mais detalhada, consulte [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography).

SSH é um protocolo de ligação encriptada que permite que os inícios de sessão seguros através de ligações não segura. É o protocolo de ligação predefinido para VMs com Linux alojados no Azure. Apesar de SSH próprio fornece uma ligação encriptada, utilizar as palavras-passe com ligações SSH ainda deixa a VM vulneráveis a força bruta ataques ou deteção de palavras-passe. Um método mais seguro e preferencial de ligação a uma VM com o SSH é utilizar estas chaves públicas e privadas, também conhecido como as chaves SSH.

Se não pretender utilizar chaves SSH, pode ainda iniciar sessão as VMs do Linux utilizando uma palavra-passe. Se a VM não está exposta à Internet, a utilização de palavras-passe pode ser suficiente. No entanto, ainda tem de gerir as palavras-passe para cada VM com Linux e manter políticas de palavra-passe bom estado de funcionamento e práticas, como o comprimento mínimo da palavra-passe e a atualizar regularmente-los. A utilização de chaves SSH reduz a complexidade da gestão de credenciais individuais através de várias VMs.

## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes SSH
Ligar a e gerir VMs com Linux no Azure com um **cliente SSH**. Computadores com o Windows não têm, normalmente, um cliente SSH instalado. A atualização do Windows 10 aniversário da adicionados Bash para Windows e o mais recente do Windows 10 criadores Update fornece atualizações adicionais. Este subsistema Windows para Linux permite-lhe executar e aceder utilitários, tais como um cliente SSH nativamente dentro de uma shell de deteção. Pode seguir qualquer um os docs do Linux, em seguida, tais como [como gerar pares de chaves SSH para Linux](mac-create-ssh-keys.md). Bash para Windows está ainda em desenvolvimento e é considerado uma versão beta. Para mais informações sobre Bash para Windows, consulte [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se pretender utilizar algo que não sejam Bash para Windows, comuns SSH do Windows que pode instalar forem incluídos clientes nos seguintes pacotes:

* [Git para Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>Os ficheiros de chaves necessita criar?
O Azure requer, pelo menos, 2048 bits, **ssh-rsa** formatado chaves públicas e privadas. Se estiver a gerir os recursos do Azure utilizando o modelo de implementação clássica, terá também de gerar um PEM (`.pem` ficheiro).

Seguem-se os cenários de implementação e os tipos de ficheiros que utilizam em cada:

1. **SSH-rsa** chaves são necessárias para qualquer implementação utilizando o [portal do Azure](https://portal.azure.com)e implementações do Resource Manager utilizando o [CLI do Azure](../../cli-install-nodejs.md).
   * Estas chaves são normalmente, todos os maioria das pessoas ter.
2. A `.pem` ficheiro é necessário criar as VMs com a implementação clássica. Estas chaves são suportadas em implementações clássicas quando utilizar o [portal do Azure](https://portal.azure.com) ou [CLI do Azure](../../cli-install-nodejs.md).
   * Só tem de criar estas chaves adicionais e certificados, se estiver a gerir os recursos criados utilizando o modelo de implementação clássica.

## <a name="install-git-for-windows"></a>Instalar o Git para Windows
A secção anterior vários pacotes listados que incluem o `openssl` ferramenta para o Windows. Esta ferramenta é necessário para criar chaves públicas e privadas. Os seguintes exemplos de como instalar e utilizar detalhe **Git para Windows**, embora pode escolher qualquer pacote que preferir. **Git para Windows** dá-lhe acesso a alguns programas de software open source adicionais ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) ferramentas e utilitários que poderão ser útil à medida que trabalha com VMs com Linux.

1. Transfira e instale **Git para Windows** da seguinte localização: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).
2. Aceite as opções predefinidas durante o processo de instalação não ser que necessite especificamente para alterá-los.
3. Executar **Git Bash** do **iniciar Menu** > **Git** > **Git Bash**. A consola semelhante ao seguinte exemplo:

    ![Git para Windows Bash shell](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>Criar uma chave privada
1. No seu **Git Bash** janela, utilize `openssl.exe` para criar uma chave privada. O exemplo seguinte cria uma chave denominada `myPrivateKey` e o certificado com o nome `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    O resultado semelhante ao seguinte exemplo:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   Se bash reportar um erro, tente abrir um novo **Git Bash** janela com privilégios elevados. Em seguida, execute novamente o `openssl` comando.

2. Responda os pedidos para o nome do país, localização, nome da organização, etc.
3. A nova chave privada e certificado são criados no seu diretório de trabalho atual. Como medida de segurança, deve definir as permissões na sua chave privada para que apenas tem acesso:

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. O [secção seguinte](#create-a-private-key-for-putty) detalhes de utilização PuTTYgen para ver e utilizar a chave pública e de criar uma chave privada específico para utilizar o PuTTY para SSH para VMs com Linux. O seguinte comando gera um ficheiro de chave pública nomeado `myPublicKey.key` que pode utilizar imediatamente:

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. Se também precisa de gerir recursos de clássico, converter o `myCert.pem` para `myCert.cer` (DER codificado X509 certificado). Execute este passo opcional apenas se tiver de especificamente gerir recursos de clássico mais antigos.

    Converter o certificado utilizando o seguinte comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Criar uma chave privada para o PuTTY
PuTTY for um cliente SSH comuns para o Windows. Está a utilizar qualquer cliente SSH que pretende. Para utilizar o PuTTY, terá de criar um tipo de chave - uma chave privada PuTTY (PPK) adicional. Se não pretender utilizar o PuTTY, ignore esta secção.

O exemplo seguinte cria esta chave privada adicional especificamente para o PuTTY para utilizar:

1. Utilize **Git Bash** para converter a chave privada para uma chave privada RSA que possa compreender PuTTYgen. O exemplo seguinte cria uma chave denominada `myPrivateKey_rsa` da chave existente com o nome `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Como medida de segurança, deve definir as permissões na sua chave privada para que apenas tem acesso:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. Transferir e executar PuTTYgen da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. Clique no menu: **ficheiro** > **chave privada de carga**
4. Localize a chave privada (`myPrivateKey_rsa` no exemplo anterior). O diretório predefinido quando iniciar **Git Bash** é `C:\Users\%username%`. Alterar o filtro de ficheiro para mostrar **todos os ficheiros (\*.\*)** :

    ![Carregar a chave privada existente para PuTTYgen](./media/ssh-from-windows/load-private-key.png)
5. Clique em **abra**. Numa linha indica que a chave foi importada com êxito:

    ![Chave importado com êxito para PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. Clique em **OK** para fechar a linha de comandos.
7. A chave pública é apresentada na parte superior do **PuTTYgen** janela. Copiar e colar esta chave pública no portal do Azure ou o modelo Azure Resource Manager, ao criar uma VM com Linux. Também pode clicar em **Guardar chave pública** para guardar uma cópia para o seu computador:

    ![Guarde o ficheiro de chave pública PuTTY](./media/ssh-from-windows/save-public-key.png)

    O exemplo seguinte mostra como poderia copiar e colar esta chave pública no portal do Azure, quando criar uma VM com Linux. A chave pública, em seguida, é normalmente armazenada num `~/.ssh/authorized_keys` na sua nova VM.

    ![Utilize a chave pública, quando criar uma VM no portal do Azure](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. Novamente no **PuTTYgen**, clique em **guardar a chave privada**:

    ![Guarde o ficheiro de chave privada do PuTTY](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > Uma linha de comandos pede-lhe se pretende continuar sem introduzir uma frase de acesso para a sua chave. Uma frase de acesso é como uma palavra-passe anexada a sua chave privada. Mesmo se alguém obter a chave privada, estes ainda não conseguirão autenticar utilizando apenas a chave. Eles também terá do frase de acesso. Sem uma frase de acesso, se alguém obtém a chave privada, pode iniciar sessão para qualquer serviço que utiliza essa chave ou a VM. Recomendamos que crie uma frase de acesso. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.
   >
   >

    Se pretender introduza uma frase de acesso, clique em **não**, introduza uma frase de acesso na janela principal do PuTTYgen e, em seguida, clique em **Guardar chave privada** novamente. Caso contrário, clique em **Sim** para continuar sem fornecer o frase de acesso opcional.
9. Introduza um nome e uma localização para guardar o ficheiro PPK.

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utilizar o Putty para SSH para uma máquina com Linux
Novamente, PuTTY for um cliente SSH comuns para o Windows. Está a utilizar qualquer cliente SSH que pretende. Os seguintes passos de detalhe como utilizar a chave privada para autenticação com a VM do Azure através do SSH. Os passos serem semelhantes em outros clientes chaves SSH em termos de que necessita de carregar a chave privada para autenticar a ligação SSH.

1. Transferir e executar putty da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Preencha o nome de anfitrião ou endereço IP da sua VM do portal do Azure:

    ![Abrir nova ligação PuTTY](./media/ssh-from-windows/putty-new-connection.png)
3. Antes de selecionar **abra**, clique em **ligação** > **SSH** > **Auth** separador. Procure e selecione a sua chave privada:

    ![Selecione a sua chave privada do PuTTY para autenticação](./media/ssh-from-windows/putty-auth-dialog.png)
4. Clique em **abra** para ligar à máquina virtual

## <a name="next-steps"></a>Passos seguintes
Também pode gerar as chaves públicas e privadas [utilizando OS X e Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre Bash para Windows e as vantagens de ter as ferramentas de OSS prontamente disponíveis no seu computador Windows, consulte [Bash no Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se tiver problemas em utilizar o SSH para ligar à sua VMs com Linux, consulte [resolver problemas de SSH ligações a uma VM com Linux do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
