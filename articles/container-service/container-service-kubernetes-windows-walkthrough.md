---
title: Cluster de Kubernetes no Azure para Windows | Microsoft Docs
description: "Implementar e começar a utilizar um cluster de Kubernetes para contentores do Windows no Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: dlepow
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 010a9a4a9ad0f6f7584b1c9a54e665557078d25b


---

# <a name="get-started-with-windows-containers-in-a-kubernetes-cluster"></a>Introdução aos contentores do Windows num cluster de Kubernetes


Este artigo mostra como criar um cluster de Kubernetes no Azure Container Service que contém nós do Windows para executar contentores do Windows. 

> [!NOTE]
> O suporte para contentores do Windows com Kubernetes no Azure Container Service está em pré-visualização. Utilize o portal do Azure ou um modelo do Resource Manager para criar um cluster de Kubernetes com nós do Windows. Esta funcionalidade não é atualmente suportada com o Azure CLI 2.0.



A imagem seguinte mostra a arquitetura de um cluster de Kubernetes no Azure Container Service com um nó principal do Linux e dois nós de agente do Windows. 

* O nó principal do Linux serve a API REST do Kubernetes e está acessível através de SSH na porta 22 ou `kubectl` na porta 443. 
* Os nós de agente do Windows estão agrupados num conjunto de disponibilidade do Azure e executam os contentores. Os nós do Windows podem ser acedidos através de um túnel SSH de RDP através do nó principal. As regras do Balanceador de Carga do Azure são adicionadas dinamicamente ao cluster consoante os serviços expostos.


![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Todas as VMs estão na mesma rede virtual privada e totalmente acessíveis entre si. Todas as VMs executam um kubelet, Docker e um proxy.

## <a name="prerequisites"></a>Pré-requisitos


* **Chave pública RSA SSH**: ao implementar através do portal ou de um dos modelos de início rápido do Azure, tem de indicar uma chave pública RSA SSH para autenticação nas máquinas virtuais do Azure Container Service. Para criar chaves RSA Secure Shell (SSH), veja as orientações para [OS X e Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID e segredo de cliente de principal de serviço**: para obter mais informações e orientações, veja [Sobre o principal de serviço para um cluster de Kubernetes](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Criar o cluster

Pode utilizar o portal do Azure para [criar um cluster de Kubernetes](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) com os nós de agente do Windows. 

No painel **Configuração da arquitetura**, em **Configuração do Orchestrator**, selecione **Kubernetes - Windows**. 

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Utilize a ferramenta da linha de comandos `kubectl` para ligar a partir do computador local para o nó principal do cluster de Kubernetes. Para obter os passos de instalação e configuração de `kubectl`, veja [Ligar a um cluster do Azure Container Service](container-service-connect.md#connect-to-a-kubernetes-cluster). Pode utilizar os comandos `kubectl` para aceder à IU da web de Kubernetes e para criar e gerir cargas de trabalho de contentores do Windows.

## <a name="create-your-first-kubernetes-service"></a>Criar o primeiro serviço do Kubernetes

Depois de criar o cluster e ligar a `kubectl`, pode tentar iniciar uma aplicação Web do Windows básica e expô-la à Internet. Neste exemplo, vai especificar os recursos de contentor com um ficheiro YAML e, em seguida, criá-lo com `kubctl apply`.

1. Para ver uma lista dos nós, escreva `kubectl get nodes`.  Se quiser detalhes completos dos nós, escreva:  

  ```
  kubectl get nodes -o yaml
  ```

2. Crie um ficheiro denominado `simpleweb.yaml` e copie o seguinte. Este ficheiro configura uma aplicação Web com a imagem do SO base Server Core do Windows Server 2016 a partir de [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/).  

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$ip = (Get-NetIPAddress | where {$$_.IPAddress -Like '*.*.*.*'})[0].IPAddress ; $$url = 'http://'+$$ip+':80/' ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add($$url) ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at {0}...' -f $$url) ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

3. Para iniciar a aplicação, escreva:

  ```
  kubectl apply -f simpleweb.yaml
  ```
  
  > [!NOTE] 
  > A configuração inclui `type: LoadBalancer`. Esta definição faz com que o serviço seja exposto à Internet através de um balanceador de carga do Azure. Para obter mais informações, veja [Contentores de balanceamento de carga num cluster do Kubernetes no Azure Container Service](container-service-kubernetes-load-balancing.md).
  
4. Para verificar a implementação do serviço (o que demora cerca de 30 segundos), escreva:

  ```
  kubectl get pods
  ```

5. Depois de o serviço estar em execução, para ver os endereços IP internos e externos do serviço, escreva:

  ```
  kubectl get svc
  ``` 

  ![Endereços IP do serviço Windows](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

  A adição do endereço IP externo demora vários minutos. Antes de o balanceador de carga configurar o endereço externo, é apresentado como `<pending>`.


6. Depois de o endereço IP externo estar disponível, pode aceder ao serviço no seu browser.

  ![Aplicação do Windows server no browser](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Aceder a nós do Windows
Os nós do Windows podem ser acedidos a partir de um computador local Windows através de uma Ligação ao Ambiente de Trabalho Remoto. Recomendamos que utilize um túnel SSH de RDP através do nó principal. 

Existem várias opções para criar túneis SSH no Windows. Esta secção descreve como utilizar o PuTTY para criar o túnel.

1. [Transfira o PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para o seu sistema Windows.

2. Execute a aplicação.

3. Introduza um nome de anfitrião composto pelo nome de utilizador administrador do cluster e o nome DNS público do primeiro mestre no cluster. O **Nome de Anfitrião** é semelhante a `adminuser@PublicDNSName`. Introduza 22 para a **Porta**.

    ![Configuração do puTTY 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Selecione **SSH > Auth**. Adicione um caminho para o ficheiro da chave privada (formato .ppk) para autenticação. Pode utilizar uma ferramenta como [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para gerar este ficheiro a partir da chave SSH utilizada para criar o cluster.

    ![Configuração do puTTY 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Selecione **SSH > Túneis** e configure as portas reencaminhadas. Uma vez que o computador Windows local já está a utilizar a porta 3389, recomenda-se que utilize as seguintes definições para aceder ao nó 0 do Windows 0 e ao nó 1 do Windows. (Continue este padrão para outros nós do Windows.)

  **Nó 0 do Windows**

  * **Porta de Origem:** 3390
  * **Destino:** 10.240.245.5:3389

  **Nó 1 do Windows**

  * **Porta de Origem:** 3391
  * **Destino:** 10.240.245.6:3389

  ![Imagem dos túneis RDP do Windows](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Quando tiver terminado, clique em **Sessão > Guardar**, para guardar a configuração da ligação.

7. Para ligar à sessão PuTTY, clique em **Abrir**. Complete a ligação ao nó principal.

8. Inicie Ligação ao Ambiente de Trabalho Remoto. Para ligar ao primeiro nó do Windows, para **Computador**, especifique `localhost:3390` e clique em **Ligar**. (Para ligar ao segundo, especifique `localhost:3390`, etc.) Para concluir a ligação, indique a palavra-passe de administrador do Windows local que configurou durante a implementação.








## <a name="next-steps"></a>Passos seguintes

Eis as ligações recomendadas para saber mais sobre o Kubernetes:

* [Bootcamp do Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) – Mostra como implementar, dimensionar, atualizar e depurar aplicações em contentores.
* [Guia de Utilizador do Kubernetes](http://kubernetes.io/docs/user-guide/) – Fornece informações sobre como executar programas num cluster do Kubernetes existente.
* [Exemplos do Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) – Fornece exemplos sobre como executar aplicações reais com o Kubernetes.


<!--HONumber=Feb17_HO4-->


