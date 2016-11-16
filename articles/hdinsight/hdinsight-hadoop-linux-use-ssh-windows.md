---
title: Utilizar chaves SSH com o Hadoop em clusters baseados em Linux a partir do Windows | Microsoft Docs
description: "Saiba como criar e utilizar chaves SSH para se autenticar em clusters do HDInsight baseados em Linux. Ligue-se a clusters a partir de clientes baseados em Windows através do cliente SSH PuTTY."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 639328ca-d800-4fa9-97ed-5664477b88cd
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/30/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d98b40b91f3f84aa717ed2f295fb9c341fe4a38


---
# <a name="use-ssh-with-linuxbased-hadoop-on-hdinsight-from-windows"></a>Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Windows
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

O [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite-lhe efetuar operações remotamente nos seus clusters do HDInsight baseado em Linux utilizando uma interface de linha de comandos. Este documento fornece informações sobre a ligação ao HDInsight a partir de clientes baseados em Windows através do cliente SSH PuTTY.

> [!NOTE]
> Os passos deste artigo partem do princípio de que está a utilizar um cliente baseado em Windows. Se estiver a utilizar um cliente Linux, Unix ou OS X, consulte [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
> 
> Se tiver o Windows 10 e utilizar [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about), pode utilizar os passos do documento [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* **PuTTY** e **PuTTYGen** para clientes baseados em Windows. Estes utilitários estão disponíveis em [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
* Um browser moderno que suporte HTML5.

OU

* [CLI do Azure](../xplat-cli-install.md).
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="what-is-ssh"></a>O que é o SSH?
O SSH é um utilitário para iniciar sessão e executar remotamente comandos num servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma ligação encriptada ao nó principal do cluster e disponibiliza uma linha de comandos utilizada para introduzir os comandos. Em seguida, os comandos são executados diretamente no servidor.

### <a name="ssh-user-name"></a>Nome de utilizador SSH
Um nome de utilizador SSH é o nome que utiliza para se autenticar no cluster do HDInsight. Quando especifica um nome de utilizador SSH durante a criação do cluster, este utilizador é criado em todos os nós do cluster. Assim que o cluster for criado, pode utilizar este nome de utilizador para se ligar aos nós principais do cluster do HDInsight. Depois, a partir dos nós principais, pode ligar-se aos nós de trabalho individuais.

### <a name="ssh-password-or-public-key"></a>Palavra-passe ou Chave pública SSH
Um utilizador do SSH pode utilizar uma palavra-passe ou uma chave pública para a autenticação. Uma palavra-passe é simplesmente uma cadeia de texto criada por si, enquanto uma chave pública faz parte de um par de chaves criptográficas gerado para o identificar de forma exclusiva.

Uma chave é mais segura do que uma palavra-passe. No entanto, são necessários passos adicionais para gerar a chave e tem de manter os ficheiros que a contêm numa localização segura. Se alguém conseguir obter acesso aos ficheiros de chave, também obtém acesso à sua conta. Além disso, se perder os ficheiros de chave, não conseguirá iniciar sessão na sua conta.

Um par de chaves é constituído por uma chave pública (que é enviada para o servidor do HDInsight) e uma chave privada (que é guardada no seu computador cliente). Quando se liga ao servidor do HDInsight através do SSH, o cliente SSH utiliza a chave privada no seu computador para se autenticar junto do servidor.

## <a name="create-an-ssh-key"></a>Criar uma chave SSH
Utilize as seguintes informações caso pretenda utilizar chaves SSH com o cluster. Se pretender utilizar uma palavra-passe, pode ignorar esta secção.

1. Abra o PuTTYGen.
2. Para **Tipo de chave a gerar**, selecione **SSH-2 RSA** e, em seguida, clique em **Gerar**.
   
    ![Interface do PuTTYGen](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)
3. Mova o rato na área abaixo da barra de progresso, até que a barra seja preenchida. Ao mover o rato, gera dados aleatórios que são utilizados para gerar a chave.
   
    ![mover o rato](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)
   
    Assim que a chave for gerada, é apresentada a chave pública.
4. Para uma maior segurança, pode introduzir uma frase de acesso no campo **Frase de acesso da chave** e, em seguida, introduzir o mesmo valor no campo **Confirmar frase de acesso**.
   
    ![frase de acesso](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
   
   > [!NOTE]
   > Recomendamos vivamente que utilize uma frase de acesso segura para a chave. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.
   > 
   > 
5. Clique em **Guardar chave privada** para guardar a chave num ficheiro **.ppk**. Esta chave será utilizada para efetuar a autenticação no cluster do HDInsight baseado em Linux.
   
   > [!NOTE]
   > Deve armazenar esta chave numa localização segura, uma vez que pode ser utilizada para aceder ao seu cluster do HDInsight baseado em Linux.
   > 
   > 
6. Clique em **Guardar chave pública** para guardar a chave como um ficheiro **.txt**. Isto permite-lhe reutilizar a chave pública no futuro quando criar clusters do HDInsight baseados em Linux adicionais.
   
   > [!NOTE]
   > A chave pública também é apresentada na parte superior do PuTTYGen. Pode clicar com o botão direito do rato neste campo, copiar o valor e, em seguida, colá-lo num formulário ao criar um cluster através do Portal do Azure.
   > 
   > 

## <a name="create-a-linuxbased-hdinsight-cluster"></a>Criar um cluster do HDInsight baseado em Linux
Ao criar um cluster do HDInsight baseado em Linux, tem de fornecer a chave pública criada anteriormente. Existem duas formas de criar um cluster do HDInsight baseado em Linux a partir de clientes baseados em Windows:

* **Portal do Azure** – utiliza um portal baseado na Web para criar o cluster.
* **CLI do Azure para Mac, Linux e Windows** – utiliza comandos da linha de comandos para criar o cluster.

Cada um destes métodos requer a utilização da chave pública. Para obter informações completas sobre a criação de um cluster do HDInsight baseado em Linux, consulte [Aprovisionar clusters do HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="azure-portal"></a>Portal do Azure
Ao utilizar o [Portal do Azure][preview-portal] para criar um cluster do HDInsight baseado em Linux, tem de introduzir um **Nome de utilizador SSH** e selecionar a opção para introduzir uma **PALAVRA-PASSE** ou uma **CHAVE PÚBLICA SSH**.

Se selecionar **CHAVE PÚBLICA SSH**, pode colar a chave pública (apresentada no campo **Chave pública para colar no ficheiro authorized\_keys do OpenSSH** no PuttyGen) no campo **CHAVE PÚBLICA SSH** ou selecionar a opção **Selecionar um ficheiro** para procurar e selecionar o ficheiro que contém a chave pública.

![Imagem de um formulário que solicita a chave pública](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Esta ação cria um início de sessão para o utilizador especificado e permite a autenticação por palavra-passe ou a autenticação por chave SSH.

### <a name="azure-commandline-interface-for-mac-linux-and-windows"></a>Interface de Linha de Comandos do Azure para Mac, Linux e Windows
Pode utilizar a [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md) para criar um novo cluster através do comando `azure hdinsight cluster create`.

Para obter mais informações sobre a utilização deste comando, consulte [Aprovisionar clusters do Hadoop em Linux no HDInsight através de opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="connect-to-a-linuxbased-hdinsight-cluster"></a>Ligar a um cluster do HDInsight baseado em Linux
1. Abra o PuTTY.
   
    ![interface do putty](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)
2. Se forneceu uma chave SSH quando criou a sua conta de utilizador, tem de executar o passo seguinte para selecionar a chave privada a utilizar durante a autenticação no cluster:
   
    Em **Categoria**, expanda **Ligação**, expanda **SSH** e selecione **Autenticação**. Por último, clique em **Procurar** e selecione o ficheiro .ppk que contém a chave privada.
   
    ![interface do putty, selecionar chave privada](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)
3. Em **Categoria**, selecione **Sessão**. No ecrã **Opções básicas para a sua sessão do PuTTY**, introduza o endereço SSH do seu servidor do HDInsight no campo **Nome do anfitrião (ou endereço IP)**. Existem dois endereços SSH possíveis que pode utilizar ao ligar a um cluster:
   
   * **Endereço do nó principal**: para se ligar ao nó principal do cluster, utilize o nome do cluster e **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.
   * **Endereço do nó de extremidade**: se estiver a estabelecer ligação a um Servidor R no cluster do HDInsight, pode ligar-se ao nó de extremidade do Servidor R ao utilizar o endereço **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, em que CLUSTERNAME é o nome do cluster. Por exemplo, **RServer.mycluster.ssh.azurehdinsight.net**.
     
     ![interface do putty com endereço ssh](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)
4. Para guardar as informações da ligação para utilização no futuro, introduza um nome para esta ligação em **Sessões Guardadas** e, em seguida, clique em **Guardar**. A ligação será adicionada à lista de sessões guardadas.
5. Clique em **Abrir** para ligar ao cluster.
   
   > [!NOTE]
   > Se for a primeira vez que estabelece ligação ao cluster, irá receber um alerta de segurança. Isto é normal. Selecione **Sim** para colocar a chave RSA2 do servidor em cache e continuar.
   > 
   > 
6. Quando lhe for pedido, introduza o utilizador que introduziu ao criar o cluster. Caso tenha fornecido uma palavra-passe para o utilizador, também lhe é pedido que a introduza.

> [!NOTE]
> Os passos acima partem do princípio de que está a utilizar a porta 22, que ligará ao nó principal primário no cluster do HDInsight. Se utilizar a porta 23, liga-se ao secundário. Para obter mais informações sobre os nós principais, consulte [Disponibilidade e fiabilidade de clusters do Hadoop no HDInsight](hdinsight-high-availability-linux.md).
> 
> 

### <a name="connect-to-worker-nodes"></a>Ligar a nós de trabalho
Os nós de trabalho não estão diretamente acessíveis a partir de fora do datacenter do Azure, mas é possível aceder-lhes a partir do nó principal do cluster através do SSH.

Se forneceu uma chave SSH quando criou a sua conta de utilizador, tem de executar os passos seguintes para utilizar a chave privada durante a autenticação no cluster, caso se pretenda ligar aos nós de trabalho.

1. Instale o Pageant a partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html). Este utilitário é utilizado para colocar chaves SSH em cache para o PuTTY.
2. Execute o Pageant. Este é minimizado para um ícone no tabuleiro de estado. Faça duplo clique no ícone e selecione **Adicionar Chave**.
   
    ![adicionar chave](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)
3. Quando for apresentada a caixa de diálogo de procura, selecione o ficheiro .ppk que contém a chave e, em seguida, clique em **Abrir**. Esta ação adiciona a chave ao Pageant, que a irá fornecer ao PuTTY durante a ligação ao cluster.
   
   > [!IMPORTANT]
   > Se tiver utilizado uma chave SSH para proteger a sua conta, tem de concluir os passos anteriores para se conseguir ligar aos nós de trabalho.
   > 
   > 
4. Abra o PuTTY.
5. Se utilizar uma chave SSH para a autenticação, na secção **Categoria**, expanda **Ligação**, expanda **SSH** e, em seguida, selecione **Autenticação**.
   
    Na secção **Parâmetros de autenticação**, ative **Permitir reencaminhamento de agente**. Isto permite ao PuTTY passar automaticamente a autenticação de certificados através da ligação ao nó principal do cluster durante a ligação aos nós de trabalho.
   
    ![permitir reencaminhamento de agente](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)
6. Ligue-se ao cluster conforme explicado anteriormente. Se utilizar uma chave SSH para a autenticação, não precisa de a selecionar, uma vez que a chave SSH adicionada ao Pageant será utilizada para efetuar a autenticação no cluster.
7. Assim que tiver sido estabelecida a ligação, utilize o seguinte para obter uma lista de nós do cluster. Substitua *ADMINPASSWORD* pela palavra-passe da conta de administrador do cluster. Substitua *CLUSTERNAME* pelo nome do cluster.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Esta ação irá devolver informações no formato JSON para os nós do cluster, incluindo `host_name`, que contém o nome de domínio completamente qualificado (FQDN) de cada nó. Segue-se um exemplo de uma entrada `host_name` devolvida pelo comando **curl**:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
8. Assim que tiver uma lista dos nós de trabalho aos quais se pretende ligar, utilize o seguinte comando da sessão do PuTTY para abrir uma ligação a um nó de trabalho:
   
        ssh USERNAME@FQDN
   
    Substitua *USERNAME* pelo nome de utilizador SSH e *FQDN* pelo FQDN do nó de trabalho. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
   
   > [!NOTE]
   > Se utilizar uma palavra-passe para autenticar a sessão SSH, é-lhe pedido que introduza novamente a palavra-passe. Se utilizar uma chave SSH, a ligação deverá ser concluída sem serem apresentados quaisquer pedidos.
   > 
   > 
9. Quando a sessão for estabelecida, o prompt da sessão do PuTTY mudará de `username@hn#-clustername` para `username@wn#-clustername` para indicar que está ligado ao nó de trabalho. Todos os comandos que executar neste momento serão executados no nó de trabalho.
10. Quando terminar de realizar ações no nó de trabalho, utilize o comando `exit` para fechar a sessão no nó de trabalho. Desta forma, regressa ao prompt `username@hn#-clustername`.

## <a name="add-more-accounts"></a>Adicionar mais contas
Se precisar de adicionar mais contas ao cluster, execute os seguintes passos:

1. Gere uma nova chave pública e uma chave privada para a nova conta de utilizador, conforme descrito anteriormente.
2. Numa sessão SSH para o cluster, adicione o novo utilizador com o seguinte comando:
   
        sudo adduser --disabled-password <username>
   
    Esta ação irá criar uma nova conta de utilizador, mas irá desativar a autenticação por palavra-passe.
3. Crie o diretório e os ficheiros para armazenar a chave ao utilizar os seguintes comandos:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. Quando o editor nano for apresentado, copie e cole o conteúdo da chave pública da nova conta de utilizador. Por último, utilize **Ctrl-X** para guardar o ficheiro e sair do editor.
   
    ![imagem do editor nano com chave de exemplo](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)
5. Utilize o seguinte comando para alterar a propriedade da pasta .ssh e o conteúdo para a nova conta de utilizador:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. Agora, deverá conseguir autenticar-se junto do servidor com a nova conta de utilizador e chave privada.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>Túnel SSH
É possível utilizar o SSH para criar um túnel entre os pedidos locais, tais como pedidos Web, e o cluster do HDInsight. O pedido será encaminhado para o recurso solicitado como se tivesse tido origem no nó principal do cluster do HDInsight.

> [!IMPORTANT]
> Um túnel SSH é um requisito para aceder à IU da Web de alguns serviços do Hadoop. Por exemplo, só é possível aceder à IU do Histórico de Tarefas ou à IU do Resource Manager através de um túnel SSH.
> 
> 

Para obter mais informações sobre como criar e utilizar um túnel SSH, consulte [Utilizar um túnel SSH para aceder à IU da Web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Passos seguintes
Agora que sabe como efetuar a autenticação com uma chave SSH, saiba como utilizar o MapReduce com o Hadoop no HDInsight.

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO2-->


