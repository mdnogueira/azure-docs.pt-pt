---
title: "Utilize os agentes de VM do Azure para uma integração contínua com o Jenkins."
description: "Agentes de VM do Azure como Jenkins secundários."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: pt-pt
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Utilize os agentes de VM do Azure para uma integração contínua com o Jenkins.

Este guia de início rápido mostra como usar o plugin do agente Jenkin de VM para criar um agente do Linux (Ubuntu) exigido pelo Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Se ainda não tiver um Jenkins Master, pode começar com o [modelo de solução](install-jenkins-solution-template.md) 
* Consulte [Criar um principal de serviço do Azure com CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) se ainda não tiver um principal de serviço do Azure.

## <a name="install-azure-vm-agents-plugin"></a>Plugin dos agentes de VM do Azure para Jenkins

Se começar com o [Modelo de solução](install-jenkins-solution-template.md), o plugin do agente de VM do Azure está instalado no Jenkins Master.

Caso contrário, instale o plugin dos **Agentes de VM do Azure** a partir do painel do Jenkins.

## <a name="configure-the-plugin"></a>Configurar o plug-in

* No painel Jenkins, clique em **Gerir Jenkins -> Configurar o sistema ->**. Desloque-se até à parte inferior da página e localize a secção com o menu pendente **Adicionar nova nuvem**. No menu, selecione **Agentes de VM do Microsoft Azure**
* Selecione uma conta existente no menu pendente de credenciais do Azure.  Para adicionar um novo **Principal do serviço do Microsoft Azure,** insira os seguintes valores: ID de subscrição, ID do cliente, segredo de cliente e ponto final de Tokens de OAuth 2.0.

![Credenciais do Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Clique em **Verificar configuração** para se certificar de que a configuração do perfil está correta.
* Guarde a configuração e avance para o passo seguinte.

## <a name="template-configuration"></a>Configuração do modelo

### <a name="general-configuration"></a>Configuração geral
Em seguida, configure um modelo para ser usado na definição de um agente de VM do Azure. 

* Clique em **Adicionar** para adicionar um modelo. 
* Atribua um nome ao seu novo modelo. 
* Para a etiqueta, insira "ubuntu". Essa etiqueta é usada durante a configuração da tarefa.
* Selecione a região desejada na caixa de combinação.
* Selecione o tamanho desejado da VM.
* Especifique o nome da conta de armazenamento do Azure ou deixe em branco para usar o nome predefinido "jenkinsarmst".
* Especifique o tempo de retenção em minutos. Esta configuração define o número de minutos que o Jenkins pode aguardar antes de eliminar automaticamente um agente inativo. Especifique 0 se não pretender que os agentes inativos sejam eliminados automaticamente.

![Configuração geral](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Configuração da imagem

Para criar um agente do Linux (Ubuntu), selecione **Referência da imagem** e use a configuração seguinte como exemplo. Consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para as imagens mais recentes suportadas pelo Azure.

* Editor de imagem: Canonical
* Disponibilização de imagem: UbuntuServer
* Sku da imagem: 14.04.5-LTS
* Versão da imagem: mais recente
* Tipo de SO: Linux
* Método de arranque: SSH
* Fornecer credenciais de administrador
* Para o script de inicialização de VM, digite:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Configuração da imagem](./media/jenkins-azure-vm-agents/image-config.png)

* Clique em **Verificar modelo** para verificar a configuração.
* Clique em **Guardar**.

## <a name="create-a-job-in-jenkins"></a>Criar uma tarefa no Jenkins

* No painel do Jenkins, clique em **Novo item**. 
* Insira um nome e selecione **Projeto Freestyle** e clique em **OK**.
* No separador **Geral** , selecione "Restringir onde o projeto puder ser executado" e digite "ubuntu" na expressão da etiqueta. Agora, verá "ubuntu" no menu pendente.
* Clique em **Guardar**.

![Configurar tarefa](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Criar novo projeto

* Volte ao painel do Jenkins.
* Clique com o botão direito do rato na nova tarefa criou e clique em **Compilar agora**. Uma compilação é iniciada. 
* Quando a compilação estiver concluída, vá para **Saída da consola**. Verá que a compilação foi executada remotamente no Azure.

![Saída da consola](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Referência

* Vídeo de sexta-feira do Azure: [Integração contínua com Jenkins usando agentes de VM do Azure](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)
* Opções de configuração e informações de suporte: [Wiki de plugin do agente Jenkins de VM do Azure](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


