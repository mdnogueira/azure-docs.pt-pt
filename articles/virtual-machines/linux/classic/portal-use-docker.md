---
title: "Utilizando a extensão VM de Docker para Linux | Microsoft Docs"
description: "Descreve Docker e as extensões de máquinas virtuais do Azure e como criar máquinas virtuais do Azure que os anfitriões de docker utilizando a CLI do Azure no modelo de implementação clássica."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 932744208d9d53c87e31dcdf9e34539750be4bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Utilizar a Extensão VM do Docker com o portal clássico do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../resource-manager-deployment-model.md). Este artigo abrange utilizando o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

[Docker](https://www.docker.com/) é uma das abordagens de Virtualização mais populares, que utiliza [Linux contentores](http://en.wikipedia.org/wiki/LXC) em vez de máquinas virtuais como uma forma de isolando os dados e computação em recursos partilhados. Pode utilizar a extensão de VM de Docker gerida pelo [agente Linux do Azure] para criar uma VM de Docker que aloja qualquer número de contentores para as suas aplicações no Azure.

> [!NOTE]
> Este tópico descreve como criar uma VM de Docker do portal clássico do Azure. Para ver como criar uma VM de Docker na linha de comandos, consulte [como utilizar a extensão da VM de linha de comandos Interface do Azure (Azure CLI) Docker]. Para ver um debate de alto nível de contentores e as vantagens, consulte o [Whiteboard de nível elevado de Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Criar uma nova VM a partir da Galeria de imagem
O primeiro passo necessita de uma VM do Azure a partir de uma imagem de Linux que suporte a extensão da VM Docker, utilizando uma imagem de Ubuntu 14.04 LTS na galeria do imagem como uma imagem de servidor de exemplo e ambiente de trabalho do Ubuntu 14.04 como um cliente. No portal, clique em **+ novo** no canto inferior esquerdo para criar uma nova instância VM e selecionar uma imagem de Ubuntu 14.04 LTS as seleções disponíveis ou a Galeria de imagem completa, conforme mostrado abaixo.

> [!NOTE]
> Atualmente, apenas Ubuntu 14.04 LTS imagens mais recentes de Julho de 2014 suportam a extensão de VM de Docker.
> 
> 

![Criar uma nova imagem de Ubuntu](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Criar certificados de Docker
Depois de criada a VM, certifique-se de que o Docker está instalado no computador cliente. (Para obter mais informações, consulte [instruções de instalação do Docker](https://docs.docker.com/installation/#installation).)

Criar os ficheiros de certificado e chave para a comunicação de Docker em conformidade com [Docker em execução com https] e colocá-los no  **`~/.docker`**  diretório no computador cliente.

> [!NOTE]
> A extensão da VM no portal do Docker atualmente exige credenciais que são codificados em base64.
> 
> 

Na linha de comandos, utilize  **`base64`**  ou outra ferramenta de codificação favorita criar tópicos com codificação base64. Fazê-lo com um conjunto simples de ficheiros de certificado e chave poderá ter um aspeto semelhante a isto:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Adicionar a extensão VM de Docker
Para adicionar a extensão de VM de Docker, localize a instância VM que criou e desloque para baixo até **extensões** e clique na mesma para trazer as extensões de VM, conforme mostrado abaixo.

> [!NOTE]
> Esta funcionalidade é suportada no portal de pré-visualização apenas: https://portal.azure.com/
> 
> 

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Adicionar uma extensão
Clique em de **+ adicionar** para apresentar as extensões de VM possíveis, pode adicionar esta VM.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Selecione a extensão VM de Docker
Escolha a extensão da VM Docker, que proporciona a descrição do Docker e ligações importantes, e, em seguida, clique em **criar** na parte inferior para iniciar o procedimento de instalação.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Adicione o certificado e ficheiros de chave:
Nos campos do formulário, introduza as versões com codificação base64 do seu certificado da AC, o certificado de servidor e a chave de servidor, conforme mostrado no seguinte gráfico.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Tenha em atenção que (como a imagem anterior) a 2376 é preenchidos por predefinição. Pode introduzir qualquer ponto final aqui, mas o passo seguinte será para abrir o ponto final correspondente. Se alterar a predefinição, certifique-se abrir o ponto final correspondente no próximo passo.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Adicionar o ponto final de comunicação de Docker
Ao visualizar o grupo de recursos que criou, selecione o grupo de segurança de rede associado à sua VM e clique em **regras de segurança de entrada** para ver as regras, conforme mostrado aqui.

![](media/portal-use-docker/AddingEndpoint.png)

Clique em **+ adicionar** adicionar outra regra e, no caso de predefinidos, introduza um nome para o ponto final (neste exemplo, **Docker**) e 2376 'destino intervalo de portas'. Definir a apresentação de valor de protocolo **TCP**e clique em **OK** para criar a regra.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Testar o cliente de Docker e anfitriões de Docker do Azure
Localize e copie o nome de domínio da VM e, na linha de comandos do computador cliente, tipo `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (onde *dockerextension* é substituído pelo subdomínio para a VM).

O resultado deve ser semelhante a isto:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Depois de concluir os passos acima, tem agora um anfitrião de Docker totalmente funcional com uma VM do Azure, ser ligada a remotamente a partir de outros clientes.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
Está pronto para ir para o [Guia do utilizador Docker] e utilizar a VM de Docker. Se pretender automatizar criação anfitriões de Docker em VMs do Azure através da interface de linha de comandos, consulte [como utilizar a extensão da VM de linha de comandos Interface do Azure (Azure CLI) Docker]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[como utilizar a extensão da VM de linha de comandos Interface do Azure (Azure CLI) Docker]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[agente Linux do Azure]:../agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Docker em execução com https]:http://docs.docker.com/articles/https/
[Guia do utilizador Docker]:https://docs.docker.com/userguide/
