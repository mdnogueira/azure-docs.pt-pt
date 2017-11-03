---
title: "Executar o servidor de aplicações Java numa VM do Azure clássico | Microsoft Docs"
description: "Este tutorial utiliza recursos criados com o modelo de implementação clássica e mostra como criar uma Máquina Virtual do Windows e configurá-la para executar o servidor de aplicações do Apache Tomcat."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 03/16/2017
ms.author: robmcm
ms.openlocfilehash: 6e02f42613808bcb13c0057e9f8fcc1c02273e77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Como executar um servidor de aplicações Java numa máquina virtual criada com o modelo de implementação clássica
> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para um modelo do Resource Manager implementar um webapp com Java 8 e Tomcat, consulte [aqui](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).

Com o Azure, pode utilizar uma máquina virtual para fornecer capacidades de servidor. Por exemplo, uma máquina virtual em execução no Azure podem ser configurada para alojar um servidor de aplicações Java, tais como o Apache Tomcat.

Depois de concluir este guia, terá uma compreensão sobre como criar uma máquina virtual em execução no Azure e configurá-la para executar um servidor de aplicações Java. Irá aprender e efetuar as seguintes tarefas:

* Como criar uma máquina virtual que tenha um Kit de desenvolvimento do Java (JDK) já instalado.
* Como iniciar sessão remotamente no sua máquina virtual.
* Como instalar um servidor de aplicações Java – Apache Tomcat – na sua máquina virtual.
* Como criar um ponto final para a máquina virtual.
* Como abrir uma porta na firewall para o servidor de aplicação.

Os resultados da instalação foi concluída no Tomcat em execução numa máquina virtual.

![Máquina virtual com Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para criar uma máquina virtual
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).  
2. Clique em **novo**, clique em **computação**, em seguida, clique em **ver todos os** no **aplicações em destaque**.
3. Clique em **JDK**, clique em **JDK 8** no **JDK** painel.  
   Imagens de máquina virtual que suportem **JDK 6** e **JDK 7** estão disponíveis se tiver aplicações antigas que não estão prontas ser executada no JDK 8.
4. No painel de JDK 8, selecione **clássico**, em seguida, clique em **criar**.
5. No **Noções básicas** painel:
   1. Especifique um nome para a máquina virtual.
   2. Introduza um nome para o administrador no **nome de utilizador** campo. Lembre-se de que este nome e a palavra-passe associada que se segue no campo seguinte. Necessário-las quando que iniciam sessão remotamente em à máquina virtual.
   3. Introduza uma palavra-passe no **nova palavra-passe** campo e reintroduza-a no **Confirmar palavra-passe** campo. É esta palavra-passe da conta de administrador.
   4. Selecione as adequadas **subscrição**.
   5. Para o **grupo de recursos**, clique em **criar nova** e introduza o nome de um novo grupo de recursos. Ou, clique em **utilizar existente** e selecione um dos grupos de recursos disponíveis.
   6. Selecione uma localização onde reside a máquina virtual, tal como **Sul Central nos**.
6. Clique em **Seguinte**.
7. No **tamanho da imagem de Máquina Virtual** painel, selecione **A1 padrão** ou outra imagem adequada.
8. Clique em **Selecionar**.

9. No **definições** painel, clique em **OK**. Pode utilizar os valores predefinidos fornecidos pelo Azure.  
10. No **resumo** painel, clique em **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para iniciar sessão remotamente no sua máquina virtual
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **máquinas virtuais (clássicas)**. Se for necessário, clique em **mais serviços** no canto inferior esquerdo sob as categorias de serviço. O **máquinas virtuais (clássicas)** entrada está listada no **computação** grupo.
3. Clique no nome da máquina virtual que pretende iniciar sessão.
4. Depois da máquina virtual foi iniciada, um menu na parte superior do painel de permite ligações.
5. Clique em **Ligar**.
6. Responda aos avisos, conforme necessário para ligar à máquina virtual. Normalmente, pode guarda ou abrir o ficheiro. rdp que contém os detalhes de ligação. Poderá ter de copiar a url: porta como a última parte da primeira linha do ficheiro. rdp e colá-lo numa aplicação de início de sessão remota.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Para instalar um servidor de aplicações Java na sua máquina virtual
Pode copiar um servidor de aplicações Java para a máquina virtual, ou pode instalar um servidor de aplicações Java através de um programa de instalação.

Este tutorial utiliza o Tomcat como o servidor de aplicações Java para instalar.

1. Quando tem sessão iniciada para a máquina virtual, abra uma sessão de browser para [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Faça duplo clique na ligação para **instalador do serviço de Windows de 32 bits/64-bits**. Ao utilizar esta técnica, o Tomcat é instalado como um serviço do Windows.
3. Quando lhe for pedido, optar por executar o instalador.
4. Dentro do **o programa de configuração do Apache Tomcat** assistente, siga as instruções para instalar o Tomcat. Para efeitos deste tutorial, é adequado aceitar as predefinições. Quando chegar a **concluir o Assistente de configuração do Apache Tomcat** caixa de diálogo, opcionalmente, pode verificar **executar Apache Tomcat** ter Tomcat iniciar agora. Clique em **concluir** para concluir o processo de configuração do Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar o Tomcat

Pode iniciar manualmente a Tomcat, abra uma linha de comandos na sua máquina virtual e executar o comando **net&nbsp;iniciar&nbsp;Tomcat8**.

Depois de Tomcat está em execução, pode aceder Tomcat ao introduzir o URL <http://localhost:8080> no browser da máquina virtual.

Para ver o Tomcat em execução a partir de computadores externos, terá de criar um ponto final e abrir uma porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para criar um ponto final para a máquina virtual
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **máquinas virtuais (clássicas)**.
3. Clique no nome da máquina virtual que está a executar o servidor de aplicações Java.
4. Clique em **Pontos Finais**.
5. Clique em **Adicionar**.
6. No **adicionar ponto final** caixa de diálogo:
   1. Especifique um nome para o ponto final; Por exemplo, **HttpIn**.
   2. Selecione **TCP** para o protocolo.
   3. Especifique **80** para a porta pública.
   4. Especifique **8080** para a porta privada.
   5. Selecione **desativado** para o endereço IP flutuante.
   6. Deixe a lista de controlo de acesso, tal como está.
   7. Clique em de **OK** botão para fechar a caixa de diálogo e criar o ponto final.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir uma porta na firewall para a máquina virtual
1. Inicie sessão sua máquina virtual.
2. Clique em **Windows início**.
3. Clique em **painel de controlo**.
4. Clique em **sistema e segurança**, clique em **Firewall do Windows**e, em seguida, clique em **definições avançadas**.
5. Clique em **regras de entrada**e, em seguida, clique em **nova regra**.  
   ![Nova regra de entrada][NewIBRule]
6. Para o **tipo de regra**, selecione **porta**e, em seguida, clique em **seguinte**.  
   ![Nova porta de regra de entrada][NewRulePort]
7. No **protocolo e portas** ecrã, selecione **TCP**, especifique **8080** como o **portas locais específicas**e, em seguida, clique em  **Seguinte**.  
  ![Nova regra de entrada][NewRuleProtocol]
8. No **ação** ecrã, selecione **permitir a ligação**e, em seguida, clique em **seguinte**.
   ![Nova ação de regra de entrada][NewRuleAction]
9. No **perfil** ecrã, certifique-se de que **domínio**, **privada**, e **pública** são selecionadas e, em seguida, clique em **seguinte**.
   ![Novo perfil de regra de entrada][NewRuleProfile]
10. No **nome** ecrã, especifique um nome para a regra, tal como **HttpIn** (o nome da regra não tem de corresponder ao nome do ponto final, no entanto) e, em seguida, clique em **concluir**.  
    ![Novo nome de regra de entrada][NewRuleName]

Neste momento, o seu Web site Tomcat deve ser visível a partir de um browser externo. Na janela de endereço do browser, escreva um URL do formulário  **http://*sua\_DNS\_nome*. cloudapp.net**, onde ***sua\_DNS\_nome*** é o nome DNS que especificou quando criou a máquina virtual.

## <a name="application-lifecycle-considerations"></a>Considerações de ciclo de vida de aplicação
* Foi possível criar o seu próprio arquivo da aplicação web (WAR) e adicione-o ao **webapps** pasta. Por exemplo, crie um projeto de web dinâmico de página de serviço de Java (JSP) básico e exportá-lo como um ficheiro WAR. Em seguida, copie o WAR para o Apache Tomcat **webapps** pasta na máquina virtual, em seguida, execute-o num browser.
* Por predefinição quando é instalado o serviço de Tomcat, está definido para iniciar manualmente. Pode mudar-o para iniciar automaticamente utilizando o snap-in Serviços. Inicie o snap-in de serviços, clicando em **Windows iniciar**, **ferramentas administrativas**e, em seguida, **serviços**. Faça duplo clique o **Apache Tomcat** de serviço e definir **o tipo de arranque** para **automática**.

    ![A definição de um serviço para iniciar automaticamente][service_automatic_startup]

    A vantagem de ter Tomcat iniciar automaticamente é que entra em execução quando a máquina virtual é reiniciada (por exemplo, depois de instalar as atualizações de software que requerem um reinício).

## <a name="next-steps"></a>Passos seguintes
Pode saber sobre outros serviços (por exemplo, o Storage do Azure, o barramento de serviço e a SQL Database) que pode querer incluir com as aplicações de Java. Ver as informações disponíveis no [Centro de programadores Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
