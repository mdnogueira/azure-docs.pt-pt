---
title: Instalar RStudio com o servidor R no HDInsight - Azure | Microsoft Docs
description: Como instalar RStudio com o servidor R no HDInsight.
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aaf527d6d95b97eff5edcab9040ce08751bb8296
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Instalação do RStudio com o servidor R no HDInsight

Este artigo descreve como instalar a versão (gratuita) da Comunidade do [RStudio servidor](https://www.rstudio.com/products/rstudio-server/) no nó de extremidade de um cluster utilizando um script personalizado. Servidor do RStudio fornece um IDE baseada no browser para utilização por clientes remotos e é amplamente utilizado no Linux. Existem vários integrada ambientes de desenvolvimento (IDEs) disponíveis para R hoje em dia, incluindo:

- Microsoft [R Tools para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [Servidor do RStudio](https://www.rstudio.com/products/rstudio-server/) 
- Walware do baseado no Eclipse [StatET](http://www.walware.de/goto/statet).

A vantagem de instalar o servidor de RStudio no nó de extremidade de um cluster do HDInsight é que fornece uma experiência de IDE completa para o desenvolvimento e a execução dos scripts de R com o servidor R no cluster. Esta configuração pode ser consideravelmente mais produtiva ao utilizar predefinição da consola do R.

> [!NOTE]
> O procedimento descrito neste artigo só é relevante se não selecionou para instalar a edição de Comunidade do servidor do RStudio quando aprovisionar o seu cluster. Se adicionou durante o aprovisionamento, em seguida, para aceder ao mesmo, clique em de **Dashboards de servidor R** mosaico na entrada portal do Azure para o cluster, em seguida, o **R Studio Server** mosaico. 

Se preferir utilizar a versão de Pro comercialmente licenciada do RStudio Server, tem de seguir as instruções de instalação do [RStudio servidor](https://www.rstudio.com/products/rstudio/download-server/).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Azure HDInsight com o servidor R instalado. Para obter instruções, consulte [começar com o servidor R nos clusters do HDInsight](r-server-get-started.md).
* Um cliente SSH. Para as distribuições de Linux e Unix ou Macintosh OS X, o `ssh` comando é fornecido com o sistema operativo. Para o Windows, recomendamos [Cygwin](http://www.redhat.com/services/custom/cygwin/) com o [OpenSSH opção](https://www.youtube.com/watch?v=CwYSvvGaiWU), ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalar RStudio no cluster utilizando um script personalizado

Eis os passos:

1. Identifica o nó de extremidade do cluster. Para um cluster do HDInsight com o servidor R, segue-se a Convenção de nomenclatura para o nó principal e o nó de extremidade.
   * Nó principal`CLUSTERNAME-ssh.azurehdinsight.net`
   * Nó de extremidade`CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH no nó de extremidade do cluster utilizando o padrão de nomenclatura fornecido no passo 1. Para obter mais informações, veja [Utilizar SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Assim que estiver ligado, tornar-se um utilizador de raiz no cluster. Na sessão SSH, utilize o seguinte comando:

        sudo su -

4. Transferir o script personalizado para instalar RStudio. Utilize o seguinte comando:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Alterar as permissões para o ficheiro de script personalizado e execute o script. Utilize os seguintes comandos:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se utilizou uma palavra-passe do SSH ao criar um cluster do HDInsight com o servidor R, pode ignorar este passo e avance para o seguinte. Se utilizou uma chave SSH em vez disso, para criar o cluster, tem de definir uma palavra-passe para o utilizador SSH. Terá desta palavra-passe ao ligar a RStudio. Execute os seguintes comandos:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Quando lhe for pedido para **palavra-passe do Kerberos atual**, prima **ENTER**.  Tenha em atenção que tem de substituir `USERNAME` com um utilizador SSH para o seu cluster HDInsight. Se a palavra-passe é definida com êxito, deverá ver a mensagem seguinte:

        passwd: password updated successfully

    Sair da sessão SSH.

8. Criar um túnel SSH para o cluster ao mapeamento `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` no cluster do HDInsight para o computador cliente. Tem de criar um túnel SSH antes de abrir uma nova sessão de browser.

   * Um cliente Linux ou um cliente do Windows com [Cygwin](http://www.redhat.com/services/custom/cygwin/), abra uma sessão de terminal e utilize o seguinte comando:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Substitua **USERNAME** com um utilizador SSH para o cluster do HDInsight e substitua **CLUSTERNAME** com o nome do cluster do HDInsight.
       Também pode utilizar uma chave SSH, em vez de uma palavra-passe adicionando `-i id_rsa_key`.        
   * Se utilizar um cliente do Windows com PuTTY, em seguida

     1. Abra o PuTTY e introduza as suas informações de ligação.
     2. No **categoria** secção para a esquerda da caixa de diálogo, expanda **ligação**, expanda **SSH**e, em seguida, selecione **túneis**.
     3. Fornecem as seguintes informações sobre o **opções controlar SSH porta reencaminhamento** formulário:

        * **Porta de origem** - A porta no cliente que pretende reencaminhar. Por exemplo, **8787**.
        * **Destino** -o de destino que tem de ser mapeado para a máquina do cliente local. Por exemplo, **localhost:8787**.

            ![Criar um túnel SSH](./media/r-server-install-r-studio/createsshtunnel.png "criar um túnel SSH")

     4. Clique em **adicionar** para adicionar as definições e, em seguida, clique em **abrir** para abrir uma ligação SSH.
     5. Quando lhe for solicitado, inicie sessão no servidor para estabelecer uma sessão SSH e ativará o túnel.

9. Abra um browser e introduza o seguinte URL com base na porta que introduziu para o túnel:

        http://localhost:8787/ 

10. Lhe for pedido que introduza o nome de utilizador do SSH e a palavra-passe para ligar ao cluster. Se tiver utilizado uma chave SSH ao criar o cluster, tem de introduzir a palavra-passe que criou no passo 5.

    ![Ligar a R Studio](./media/r-server-install-r-studio/connecttostudio.png "criar um túnel SSH")

11. Para testar se a instalação do RStudio foi concluída com êxito, pode executar um script de teste que executa tarefas de MapReduce e Spark baseado em R no cluster. Para transferir o script de teste para ser executada no RStudio, volte atrás para a consola SSH e introduza os seguintes comandos:

    *    Se tiver criado um cluster de Hadoop com R, utilize este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Se tiver criado um cluster do Spark com R, utilize este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. No RStudio, consulte o script de teste que transferiu. Faça duplo clique o ficheiro para abri-lo, selecione o conteúdo do ficheiro e, em seguida, clique em **executar**. Deverá ver o resultado no **consola** painel:

   ![Testar a instalação](./media/r-server-install-r-studio/test-r-script.png "testar a instalação")

Outra opção seria escreva `source(testhdi.r)` ou `source(testhdi_spark.r)` ao executar o script.

## <a name="see-also"></a>Consultar também

* [As opções de contexto para o servidor R nos clusters do HDInsight de computação](r-server-compute-contexts.md)
* [Azure Storage options for R Server on HDInsight (Opções do Armazenamento do Azure para o R Server no HDInsight)](r-server-storage.md)

