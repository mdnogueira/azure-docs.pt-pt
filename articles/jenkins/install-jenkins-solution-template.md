---
title: Criar o seu primeiro Jenkins Master numa VM com Linux (Ubuntu) no Azure
description: "Tire partido do modelo de solução para implementar o Jenkins."
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: pt-pt
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>Criar o seu primeiro Jenkins Master numa VM com Linux (Ubuntu) no Azure

Este guia de início rápido explica como instalar a versão Jenkins mais estável e mais recente numa VM com Linux (Ubuntu 14.04 LTS), juntamente com as ferramentas e os plugins configurados para funcionar com o Azure. Estas ferramentas incluem:
<ul>
<li>Git para controlo da origem</li>
<li>Plugin de credencial do Azure para se ligar com segurança</li>
<li>Plugin de agentes da MV do Azure para compilação elástica, teste e integração contínua</li>
<li>Plugin do armazenamento do Azure para armazenar os artefactos</li>
<li>CLI do Azure para implementar aplicações usando scripts</li>
</ul>

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma conta do Azure gratuita.
> * Crie um Jenkins Master numa VM do Azure com um modelo de solução. 
> * Execute a configuração inicial do Jenkins.
> * Instale os plugins sugeridos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>Criar a MV no Azure ao implementar o modelo de solução para Jenkins

Os modelos de início rápido do Azure permitem a implementação rápida e fiável de tecnologia complexa no Azure.  O Azure Resource Manager permite-lhe utilizar as suas aplicações com um [modelo declarativo.](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins) Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.

Consulte a informação sobre os [planos e preços](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview) deste modelo para compreender as opções de custo.

Aceda a [A imagem do Jenkins no mercado](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview), clique em **OBTER AGORA**  

No portal do Azure, clique em **Criar**.  Este modelo requer o uso do Resource Manager para ser desativado.
   
![Caixa de diálogo do portal do Azure](./media/install-jenkins-solution-template/ap-create.png)

No separador **Configurar as definições básicas**:

![Configurar as definições básicas](./media/install-jenkins-solution-template/ap-basic.png)

* Atribua um nome à sua instância Jenkins.
* Selecione um tipo de disco da VM.  Para cargas de trabalho de produção, escolha uma VM e uma SSD de maiores dimensões para melhorar o desempenho.  Pode ler mais [aqui](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) sobre os tipos de disco do Azure.
* Nome de utilizador: deve cumprir os requisitos de comprimento e não deve incluir palavras reservadas ou carateres não suportados. Não são permitidos nomes como "admin".  Para mais informações sobre os requisitos de nome de utilizador e palavra-passe, consulte [aqui](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq).
* Tipo de autenticação: crie uma instância que é protegida por uma palavra-passe ou [chave pública SSH](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows). A palavra-passe tem de cumprir, pelo menos 3 dos seguintes requisitos: uma letra minúscula, uma letra maiúscula, um número e um caráter especial.
* Mantenha o tipo de versão do Jenkins como **LTS**
* Selecione uma subscrição.
* Crie um grupo de recursos ou utilize um já existente que esteja vazio. 
* Selecione uma localização.

No separador **Configurar opções adicionais**:

![Configurar opções adicionais](./media/install-jenkins-solution-template/ap-addtional.png)

* Atribua um nome de domínio para identificar exclusivamente o Jenkins Master.

Clique em **OK** para avançar para o passo seguinte. 

Quando a validação é bem-sucedida, clique em **OK** para transferir o modelo e os parâmetros. 

Em seguida, selecione **Comprar** para aprovisionar todos os recursos.

## <a name="setup-ssh-port-forwarding"></a>Configurar o encaminhamento da porta SSH

Por predefinição, a instância do Jenkins está a usar o protocolo http e escuta na porta 8080. Os utilizadores não devem autenticar mediante protocolos não seguros.
    
Configure o encaminhamento de porta para exibir a UI do Jenkins no seu computador local.

### <a name="if-you-are-using-windows"></a>Se estiver a utilizar o Windows:

Instale o PuTTY e execute este comando se usar a palavra-passe para proteger o Jenkins:
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para iniciar sessão, introduza a palavra-passe.

![Para iniciar sessão, introduza a palavra-passe.](./media/install-jenkins-solution-template/jenkins-pwd.png)

Se utilizar SSH, execute este comando:
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>Se utilizar Linux ou Mac:

Se utilizar uma palavra-passe para proteger o Jenkins Master, execute este comando:
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* Para iniciar sessão, introduza a palavra-passe.

Se utilizar SSH, execute este comando:
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>Ligar-se ao Jenkins
Depois de iniciar o túnel, aceda a http://localhost:8080/ no seu computador local.

Desbloqueie o painel do Jenkins pela primeira vez com a palavra-passe de administrador inicial.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

Para obter um token, execute o SSH na VM e execute `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Desbloquear o Jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png)

É-lhe solicitado que instale os plugins sugeridos.

Em seguida, crie um utilizador de administrador para o Jenkins Master.

A sua instância do Jenkins está agora pronta a utilizar! Pode aceder a uma vista apenas de leitura, indo até http://\<Nome da instância do DNS público que acabou de criar\>.

![O Jenkins está pronto!](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial:

> [!div class="checklist"]
> * Criou um Jenkins Master com o modelo de solução.
> * Executou uma configuração inicial do Jenkins.
> * Instalou os plugins.

Aceda a esta ligação para saber como utilizar os agentes de VM do Azure para uma integração contínua com o Jenkins.

> [!div class="nextstepaction"]
> [VM do Azure como agentes do Jenkins](jenkins-azure-vm-agents.md)

