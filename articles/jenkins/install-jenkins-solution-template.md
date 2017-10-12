---
title: Criar um servidor do Jenkins no Azure
description: "Instale o Jenkins numa máquina virtual do Linux do Azure a partir do modelo de solução do Jenkins e crie uma aplicação Java de exemplo."
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Criar um servidor do Jenkins numa VM do Linux do Azure a partir do portal do Azure

Este guia de introdução mostra como instalar o [Jenkins](https://jenkins.io) numa VM do Ubuntu Linux com as ferramentas e plug-ins configurados para trabalhar com o Azure. Quando tiver terminado, terá um servidor do Jenkins em execução no Azure e a compilar uma aplicação Java de exemplo a partir do [GitHub](https://github.com).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.
* Acesso ao SSH na linha de comandos do seu computador (por exemplo, o Bash da shell ou [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Criar a VM do Jenkins a partir do modelo de solução

Abra a [imagem do marketplace para o Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) no seu browser e selecione **OBTER AGORA** no lado esquerdo da página. Consulte os detalhes de preços e selecione **Continuar**, em seguida selecione **Criar** para configurar o servidor do Jenkins no portal do Azure. 
   
![Caixa de diálogo do portal do Azure](./media/install-jenkins-solution-template/ap-create.png)

No separador **Configurar definições básicas**, preencha os campos seguintes:

![Configurar as definições básicas](./media/install-jenkins-solution-template/ap-basic.png)

* Utilize **Jenkins** no **Nome**.
* Introduza um **Nome de utilizador**. O nome de utilizador tem de cumprir [requisitos específicos](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
* Selecione a **Palavra-passe** como o **Tipo de autenticação** e introduza uma palavra-passe. A palavra-passe tem de ter um caráter maiúsculo, um número e um caráter especial.
* Utilize **myJenkinsResourceGroup** no **Grupo de Recursos**.
* Escolha **E.U.A. Leste** na [Região do Azure](https://azure.microsoft.com/regions/) na lista pendente **Localização**.

Selecione **OK** para continuar para o separador **Configurar opções adicionais**. Introduza um nome de domínio exclusivo para identificar o servidor do Jenkins e selecione **OK**.

![Configurar opções adicionais](./media/install-jenkins-solution-template/ap-addtional.png)  

 Depois de passar a validação, selecione **OK** novamente no separador **Resumo**. Por fim, selecione **Comprar** para criar a VM do Jenkins. Quando o servidor estiver pronto, recebe uma notificação no portal do Azure:   

![A notificação do Jenkins está pronta](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Ligar-se ao Jenkins

Navegue para a máquina virtual (por exemplo, http://jenkins2517454.eastus.cloudapp.azure.com/) no seu browser. A consola do Jenkins não está acessível através de HTTP não seguro, pelo que são fornecidas instruções na página para aceder à consola do Jenkins em segurança a partir do seu computador com um túnel SSH.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Configure o túnel com o comando `ssh` na página da linha de comandos, substituindo `username` pelo nome do utilizador administrador da máquina virtual escolhido anteriormente ao configurar a máquina virtual a partir do modelo de solução.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

Depois de iniciar o túnel, aceda a http://localhost:8080/ no seu computador local. 

Obtenha a palavra-passe inicial ao executar o seguinte comando na linha de comandos, enquanto está ligado através de SSH à VM do Jenkins.

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

Desbloqueie o painel do Jenkins pela primeira vez com a palavra-passe inicial.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Selecione **Instalar plug-ins sugeridos** na próxima página e, em seguida, crie um utilizador administrador do Jenkins utilizado para aceder ao dashboard do Jenkins.

![O Jenkins está pronto!](./media/install-jenkins-solution-template/jenkins-welcome.png)

O servidor do Jenkins já está pronto para compilar o código.

## <a name="create-your-first-job"></a>Criar a sua primeira tarefa

Selecione **Criar novas tarefas** na consola do Jenkins e, em seguida, dê-lhe o nome **mySampleApp**, selecione **Projeto freestyle** e **OK**.

![Criar uma nova tarefa](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Selecione o separador **Gestão da Origem de Código**, ative **Git** e introduza o seguinte URL no campo **URL do Repositório**: `https://github.com/spring-guides/gs-spring-boot.git`

![Definir o repositório de Git](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Selecione o separador **Compilar**, em seguida selecione **Adicionar passo de compilação**, **Invocar script Gradle**. Selecione **Utilizar Gradle Wrapper**, em seguida introduza `complete` na **localização do Wrapper** e `build` para **Tarefas**.

![Utilize o Gradle wrapper para compilar](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Selecione **Avançado...** e, em seguida, introduza `complete` no campo **Script de Compilação de raiz**. Selecione **Guardar**.

![Configure definições avançadas no passo de compilação do Gradle wrapper](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Compilar o código

Selecione **Compilar Agora** para compilar o código e empacotar a aplicação de exemplo. Quando tiver concluído a compilação, selecione a ligação da **Área de trabalho** para o projeto.

![Navegue para a área de trabalho para obter o ficheiro JAR da compilação](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navegue para `complete/build/libs` e certifique-se de que `gs-spring-boot-0.1.0.jar` está disponível para verificar se a compilação foi bem-sucedida. O servidor do Jenkins já está pronto para compilar os seus próprios projetos no Azure.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar VMs do Azure como agentes do Jenkins](jenkins-azure-vm-agents.md)
