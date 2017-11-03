---
title: Criar um pipeline de desenvolvimento no Azure com Jenkins | Microsoft Docs
description: "Saiba como criar uma máquina virtual de Jenkins no Azure que obtém a partir do GitHub em cada consolidação de código e cria um novo contentor de Docker para executar a aplicação"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 52408184c8cff53f8bb7006fa940b0db4b900db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Como criar uma infraestrutura de desenvolvimento numa VM com Linux no Azure com Jenkins, GitHub e Docker
Para automatizar a fase de criação e teste de desenvolvimento de aplicações, pode utilizar uma integração contínua e o pipeline de implementação (CI/CD). Neste tutorial, vai criar um pipeline de CI/CD numa VM do Azure incluindo como:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Instalar e configurar Jenkins
> * Criar o webhook integração entre o GitHub e Jenkins
> * Criar e consolida acionador que jenkins criar tarefas a partir do GitHub
> * Criar uma imagem de Docker para a sua aplicação
> * Certifique-se de consolidações de GitHub criem nova imagem do Docker e executar a aplicação de atualizações


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-jenkins-instance"></a>Criar uma instância de Jenkins
Um tutorial anterior em [como personalizar uma máquina virtual do Linux no primeiro arranque](tutorial-automate-vm-deployment.md), aprendeu a automatizar a personalização de VM com init de nuvem. Este tutorial utiliza um ficheiro de nuvem init para instalar Jenkins e Docker numa VM. Jenkins é um servidor de automatização de open source para populares que se integra perfeitamente com o Azure para ativar a integração contínua (CI) e entrega contínua (CD). Para obter tutoriais mais sobre como utilizar Jenkins, consulte o [Jenkins no hub do Azure](https://docs.microsoft.com/azure/jenkins/).

Na sua shell atual, crie um ficheiro denominado *nuvem init.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro na Shell na nuvem não no seu computador local. Introduza `sensible-editor cloud-init-jenkins.txt` para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de toda a nuvem-init é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - wget -q -O - https://jenkins-ci.org/debian/jenkins-ci.org.key | apt-key add -
  - sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Antes de poder criar uma VM, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupJenkins* no *eastus* localização:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Agora criar uma VM com [az vm criar](/cli/azure/vm#create). Utilize o `--custom-data` parâmetro para passar o ficheiro de configuração de nuvem init. Forneça o caminho completo para *nuvem-init-jenkins.txt* se guardou o ficheiro fora do diretório de trabalho presente.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Demora alguns minutos para que a VM ser criada e configurada.

Para permitir o tráfego da web alcançar a VM, utilize [az vm open-porta](/cli/azure/vm#open-port) para abrir a porta *8080* Jenkins para tráfego de e porta *1337* para a aplicação Node.js que é utilizada para executar uma aplicação de exemplo:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Configurar Jenkins
Para aceder à sua instância Jenkins, obter o endereço IP público da sua VM:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Por motivos de segurança, tem de introduzir a palavra-passe de administrador inicial que é armazenada num ficheiro de texto na VM para iniciar a instalação de Jenkins. Utilize o endereço IP público que obteve no passo anterior para SSH para a VM:

```bash
ssh azureuser@<publicIps>
```

Ver o `initialAdminPassword` para sua Jenkins instalar e copie-o:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Se o ficheiro ainda não está disponível, aguarde alguns minutos para a nuvem-init concluir a instalação Jenkins e Docker.

Agora abra um browser e aceda a `http://<publicIps>:8080`. Conclua a configuração inicial do Jenkins da seguinte forma:

- Introduza o *initialAdminPassword* obtido a partir da VM no passo anterior.
- Escolha **selecione plug-ins para instalar**
- Procurar *GitHub* na caixa de texto na parte superior, selecione o *Plug-in do GitHub*, em seguida, selecione **instalar**
- Para criar uma conta de utilizador Jenkins, preencha o formulário conforme pretendido. Numa perspetiva de segurança, deve criar este primeiro utilizador Jenkins em vez de os continuar como a conta de administrador predefinida.
- Quando terminar, selecione **começar a utilizar Jenkins**


## <a name="create-github-webhook"></a>Criar o GitHub webhook
Para configurar a integração com o GitHub, abra o [aplicação de exemplo Olá mundo Node.js](https://github.com/Azure-Samples/nodejs-docs-hello-world) do repositório exemplos do Azure. Para copiar o repositório à sua própria conta do GitHub, selecione o **bifurcação** botão no canto superior direito.

Crie um webhook no interior de bifurcação que criou:

- Selecione **definições**, em seguida, selecione **integrações & serviços** no lado esquerdo.
- Escolha **Adicionar serviço**, em seguida, introduza *Jenkins* na caixa Filtro.
- Selecione *Jenkins (GitHub Plug-in)*
- Para o **Jenkins ligue URL**, introduza `http://<publicIps>:8080/github-webhook/`. Certifique-se de que incluiu /
- Selecione **Adicionar serviço**

![Adicionar GitHub webhook ao seu repositório forked](media/tutorial-jenkins-github-docker-cicd/github_webhook.png)


## <a name="create-jenkins-job"></a>Criar tarefa de Jenkins
Para ter Jenkins respondeu a um evento no GitHub, tais como consolidar o código, crie uma tarefa de Jenkins. 

No seu Web site Jenkins, selecione **criar novas tarefas** na home page:

- Introduza *Olámundo* como o nome da tarefa. Escolha **projeto Freestyle**, em seguida, selecione **OK**.
- Sob o **geral** secção, selecione **GitHub** projeto e introduza o URL do repositório escolhido, tais como *https://github.com/iainfoulds/nodejs-docs-hello-world*
- Sob o **gestão de código de origem** secção, selecione **Git**, introduza o seu repositório forked *.git* URL, tais como *https://github.com/iainfoulds/nodejs-docs-hello-world.git*
- Sob o **criar Acionadores** secção, selecione **acionador de rotina do GitHub para consulta GITscm**.
- Sob o **criar** secção, escolha **Adicionar passo de compilação**. Selecione **executar shell**, em seguida, introduza `echo "Testing"` na janela de comando.
- Selecione **guardar** na parte inferior da janela tarefas.


## <a name="test-github-integration"></a>Integração do GitHub de teste
Para testar a integração do GitHub com Jenkins, consolide uma alteração na sua bifurcação. 

No GitHub da IU da web, selecione o seu repositório forked e, em seguida, selecione o **index.js** ficheiro. Selecione o ícone de lápis para editar este ficheiro para a linha 6 lê:

```nodejs
response.end("Hello World!");
```

Para consolidar as alterações, selecione o **consolidar alterações** na parte inferior.

No Jenkins, uma nova compilação inicia sob o **histórico de compilação** secção do canto inferior esquerdo da sua página de tarefa. Escolha a ligação de número de compilação e selecione **consola saída** no tamanho da esquerda. Pode ver os passos Jenkins demora como o seu código é solicitado a partir do GitHub e a ação de compilação produz a mensagem `Testing` para a consola. Sempre que uma consolidação é efetuada no GitHub, o webhook acede ao Jenkins e acionam uma nova compilação desta forma.


## <a name="define-docker-build-image"></a>Definir imagem de compilação do Docker
Para ver a aplicação Node.js em execução com base no seu consolidações do GitHub, permite construir uma imagem do Docker para executar a aplicação. A imagem é criada a partir de um Dockerfile que define como configurar o contentor que executa a aplicação. 

A ligação de SSH para a VM, mude para o diretório de área de trabalho de Jenkins chamado após a tarefa que criou no passo anterior. No nosso exemplo, que foi atribuído o nome *Olámundo*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Criar um ficheiro com neste diretório de área de trabalho com `sudo sensible-editor Dockerfile` e cole o seguinte conteúdo. Certifique-se de que o Dockerfile todo é copiado corretamente, especialmente a primeira linha:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Este Dockerfile utiliza a imagem base do Node.js com Linux Extreme, porta expõe 1337 que executa a aplicação Olá, mundo, em seguida, copia os ficheiros de aplicação e inicializa-lo.


## <a name="create-jenkins-build-rules"></a>Criar regras de compilação Jenkins
No passo anterior, criou uma regra de compilação Jenkins básica que uma mensagem para a consola de saída. Permite criar o passo de compilação para utilizar o nosso Dockerfile e executar a aplicação.

Novamente na instância Jenkins, selecione a tarefa que criou no passo anterior. Selecione **configurar** no lado esquerdo e desloque-se para baixo até o **criar** secção:

- Remover existentes `echo "Test"` passo de compilação. Selecione entre a vermelho no canto superior direito da caixa de passo de compilação existente.
- Escolha **Adicionar passo de compilação**, em seguida, selecione **executar shell**
- No **comando** caixa, introduza os seguintes comandos do Docker, em seguida, selecione **guardar**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

Os passos de compilação do Docker criar uma imagem e a etiqueta com o Jenkins número de compilação para pode manter um histórico de imagens. Quaisquer contentores existentes a executar a aplicação são parados e, em seguida, são removidos. Um novo contentor, em seguida, é iniciado utilizando a imagem e executa a aplicação Node.js com base de consolidações mais recentes no GitHub.


## <a name="test-your-pipeline"></a>Testar o pipeline
Para ver o pipeline todo em ação, edite o *index.js* ficheiros no seu repositório do GitHub forked novamente e selecione **Confirmar alteração**. Inicia uma nova tarefa em Jenkins com base no webhook GitHub. Demora alguns segundos para criar a imagem do Docker e iniciar a sua aplicação num contentor de novo.

Se for necessário, obter novamente o endereço IP público da sua VM:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Abra um browser e introduza `http://<publicIps>:1337`. A aplicação Node.js é apresentada e reflete as consolidações mais recentes no seu bifurcação de GitHub da seguinte forma:

![Aplicação Node.js em execução](media/tutorial-jenkins-github-docker-cicd/running_nodejs_app.png)

Agora a efetuar outra edição para o *index.js* de ficheiros no GitHub e a consolidação da alteração. Aguarde alguns segundos para a tarefa para concluir a Jenkins, em seguida, atualize o browser para ver a versão atualizada da sua aplicação em execução num novo contentor da seguinte forma:

![Aplicação Node.js em execução depois de outra consolidação do GitHub](media/tutorial-jenkins-github-docker-cicd/another_running_nodejs_app.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou GitHub para executar uma tarefa de compilação Jenkins em cada consolidação de código e, em seguida, implementar um contentor de Docker para testar a aplicação. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Instalar e configurar Jenkins
> * Criar o webhook integração entre o GitHub e Jenkins
> * Criar e consolida acionador que jenkins criar tarefas a partir do GitHub
> * Criar uma imagem de Docker para a sua aplicação
> * Certifique-se de consolidações de GitHub criem nova imagem do Docker e executar a aplicação de atualizações

Avançar para o próximo tutorial para saber mais sobre como integrar Jenkins no Visual Studio Team Services.

> [!div class="nextstepaction"]
> [Implementar aplicações com Jenkins e serviços da equipa](tutorial-build-deploy-jenkins.md)