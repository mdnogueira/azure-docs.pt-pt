---
title: "Aprovisionar uma profunda aprendizagem Máquina Virtual de ciência de dados no Azure | Microsoft Docs"
description: "Configurar e criar uma profunda Learning dados ciência de Máquina Virtual no Azure para análise e machine learning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: db1360fa54d82c50adc04194697d994925338296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Aprovisionar uma profunda aprendizagem Máquina Virtual no Azure 

O profunda de aprendizagem Máquina Virtual (DLVM) é uma variante especial configurada do populares [máquinas de virtuais de ciência de dados](http://aka.ms/dsvm) (DSVM) torna mais fácil de utilizar a VM com base em GPU instâncias rapidamente modelos learning profunda de preparação. É suportada com o Windows 2016 ou o DSVM Ubuntu como base. O DLVM partilha as mesmas imagens da VM principal e, por conseguinte, todos os o conjunto de ferramentas avançado que está disponível no DSVM. 

O DLVM contém várias ferramentas para AI incluindo edições GPU de arquiteturas populares learning profunda Microsoft cognitivos Toolkit, TensorFlow, Keras, Caffe2, Chainer; ferramentas para adquirir e imagem previamente processo, textual dados, as ferramentas para dados ciência modelação e desenvolvimento de atividades tais como Microsoft R Server Developer Edition, Anaconda Python, blocos de notas do Jupyter para Python e R, IDEs para Python e R, o SQL Server e muitas outras bases de dados ferramentas de ML e ciência de dados. 

## <a name="create-your-deep-learning-virtual-machine"></a>Criar a ligação avançada de aprendizagem Máquina Virtual
Eis os passos para criar uma instância da máquina de Virtual profunda do Learning: 

1. Navegue para a máquina virtual listagem no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Selecione o **criar** na parte inferior para ser levados num assistente.![ dlvm criar](./media/dlvm-provision-wizard.PNG)
3. O assistente utilizado para criar o DLVM requer **entradas** para cada um do **quatro passos** enumerado à direita desta figura. Seguem-se as entradas necessárias para configurar cada um destes passos:
   
   1. **Noções básicas**
      
      1. **Nome**: nome do seu servidor de ciência de dados que está a criar.
      2. **Selecione o tipo de SO para a VM de aprendizagem profunda**: Escolha Windows ou Linux (para Windows 2016 e DSVM base Ubuntu Linux)
      2. **Nome de utilizador**: id de início de sessão de conta de administrador.
      3. **Palavra-passe**: palavra-passe da conta de administrador.
      4. **Subscrição**: Se tiver mais do que uma subscrição, selecione aquele no qual a máquina está a ser criado e cobrados.
      5. **Grupo de recursos**: pode criar uma nova ou utilize um **vazio** grupo de recursos do Azure existente na sua subscrição.
      6. **Localização**: selecione o Centro de dados que é mais adequado. Normalmente, é o Centro de dados que contém a maioria dos seus dados ou se mais próxima da sua localização física para acesso de rede mais rápido. 
      
> [!NOTE]
> Uma vez que é aprovisionado DLVM em instâncias de VM de GPU de série de NC do Azure, tem de escolher uma das localizações no Azure que tenha GPUs. Atualmente as localizações que tenham GPU VMs são: **EUA leste, Norte Central dos EUA, Sul Central dos EUA, EUA oeste, 2, Europa do Norte, Europa Ocidental**. Para obter a lista mais recente, verifique o [produtos do Azure por região página](https://azure.microsoft.com/en-us/regions/services/) e procure **NC série** em **computação**. 

   2. **Definições**: selecione um tamanho da máquina virtual para a série de NC GPU que cumpra os requisitos funcionais e restrições de custo. Crie uma conta de armazenamento para a VM.  ![definições de dlvm](./media/dlvm-provision-step-2.PNG)
   
   3. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas.
   5. **Comprar**: clique em **comprar** para iniciar o aprovisionamento. É fornecida uma ligação para os termos da transação. A VM não tem quaisquer taxas adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo. 

> [!NOTE]
> O aprovisionamento deve demorar cerca de 10 a 20 minutos. O estado de aprovisionamento da é apresentado no portal do Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Como aceder a máquina de Virtual Learning profunda

### <a name="windows-edition"></a>Edição do Windows
Depois da VM é criada, pode ambiente de trabalho remoto para a mesma utilizando as credenciais de conta de administrador que configurou no precedente **Noções básicas** secção. 

### <a name="linux-edition"></a>Edição do Linux

Depois de criada a VM, pode iniciar sessão-lo através de SSH. Utilizar as credenciais da conta que criou no **Noções básicas** secção do passo 3 para a interface de shell de texto. No cliente aWindows, pode transferir uma ferramenta de cliente SSH como [Putty](http://www.putty.org). Se preferir um ambiente de trabalho gráfico (X Windows sistema), pode utilizar o Putty de reencaminhamento de X11 ou instalar o cliente X2Go.

> [!NOTE]
> O cliente X2Go efetuar melhor no nosso teste de reencaminhamento de X11. Recomendamos que utilize o cliente X2Go para uma interface gráfica do ambiente de trabalho.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalar e configurar o cliente X2Go
O DLVM Linux já está a ser aprovisionado com o servidor de X2Go e pronto para aceitar ligações de cliente. Para se ligar no ambiente de trabalho de gráfico de VM com Linux, execute o seguinte procedimento no cliente:

1. Transferir e instalar o cliente X2Go para a sua plataforma de cliente de [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Executar o cliente X2Go e selecione **nova sessão**. É aberta uma janela de configuração com vários separadores. Introduza os seguintes parâmetros de configuração:
   * **Separador sessões**:
     * **Anfitrião**: O nome de anfitrião ou endereço IP da sua VM de ciência de dados do Linux.
     * **Início de sessão**: nome de utilizador na VM do Linux.
     * **Porta SSH**: deixe-22, o valor predefinido.
     * **Tipo de sessão**: Altere o valor para **XFCE**. Atualmente a DSVM Linux só suporta o ambiente de trabalho XFCE.
   * **Separador de suporte de dados**: pode desativar o suporte de som e cliente impressão se não pretender utilizá-los.
   * **Pastas partilhadas**: Se pretender que diretórios das suas máquinas de cliente instaladas na VM do Linux, adicione os diretórios de máquina do cliente que pretende partilhar com a VM neste separador.

Depois de iniciar sessão para a VM ao utilizar o cliente SSH ou de ambiente de trabalho gráfico XFCE através do cliente X2Go, está pronto para começar a utilizar as ferramentas que estão instaladas e configuradas na VM. No XFCE, pode ver os atalhos de menu de aplicações e ícones do ambiente de trabalho para muitas das ferramentas.

Assim que a VM é criada e aprovisionada, estará pronto começar a utilizar as ferramentas que estão instaladas e configuradas no mesmo. Existem mosaicos de menu de início e de ambiente de trabalho ícones de muitas das ferramentas. 
