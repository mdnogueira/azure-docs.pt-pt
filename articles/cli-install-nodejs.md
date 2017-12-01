---
title: Instalar a CLI do Azure 1.0 | Microsoft Docs
description: "Instalar a CLI do Azure 1.0 para Mac, Linux e Windows começar a utilizar os serviços do Azure"
editor: 
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: bdb776c8-7a76-4f3a-887c-236b4fffee10
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: rasquill
ms.openlocfilehash: 0dc9d95729f28dd0846178e24a25425170c893df
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="install-the-azure-cli-10"></a>Instalar a CLI do Azure 1.0
> [!div class="op_single_selector"]
> * [PowerShell](/powershell/azure/overview)
> * [CLI do Azure 1.0](cli-install-nodejs.md)
> * [CLI 2.0 do Azure](/cli/azure/install-azure-cli)

> [!IMPORTANT]
> Este tópico descreve como instalar a CLI do Azure 1.0. Este CLI foi preterido e só deve ser utilizada para o suporte com o modelo de gestão de serviço do Azure (ASM) com o recursos "clássico".
> Para implementações do Azure Resource Manager (ARM), utilize [Azure CLI 2.0](/cli/azure/overview).

Instale rapidamente a Interface de linha de comandos do Azure (CLI do Azure 1.0) para utilizar um conjunto de open source baseada na shell de comandos para criar e gerir recursos no Microsoft Azure. Tem várias opções para instalar estas ferramentas de plataforma no seu computador:

* **pacote npm** -execute npm (o Gestor de pacotes de JavaScript) para instalar o pacote mais recente da CLI do Azure 1.0 no seu SO ou a distribuição de Linux. Requer node.js e npm no seu computador.
* **Instalador** -transferir um instalador para a instalação fácil no Mac ou o Windows.
* **Contentor de docker** - começar a utilizar a CLI num contentor de Docker prontos para execução mais recente. Requer que os anfitriões de Docker no seu computador.

Para mais opções e em segundo plano, consulte o repositório de projeto no [GitHub](https://github.com/azure/azure-xplat-cli).

Assim que a CLI do Azure 1.0 estiver instalado, [ligá-lo com a sua subscrição do Azure](xplat-cli-connect.md) e execute o **azure** comandos a partir da sua interface de linha de comandos (Bash, Terminal, linha de comandos e assim sucessivamente) para trabalhar com os recursos do Azure.

## <a name="option-1-install-an-npm-package"></a>Opção 1: Instalar um pacote npm
Para instalar a CLI de um pacote npm, certifique-se de que transferiu e instalou o [mais recente Node.js e npm](https://nodejs.org/en/download/package-manager/). Em seguida, executar **npm instalar** para instalar o pacote da cli do azure:

```bash
npm install -g azure-cli
```

Sobre as distribuições do Linux, poderá ter de utilizar **sudo** para executar com êxito o **npm** comando da seguinte forma:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Se precisar de instalar ou atualizar o Node.js e npm no seu SO ou a distribuição de Linux, recomendamos que instale a versão mais recente do Node.js LTS (4. x). Se utilizar uma versão mais antiga, poderá obter erros de instalação.

Se preferir, transfira o mais recente Linux [ficheiros tar] [ linux-installer] para o pacote npm localmente. Em seguida, instale o pacote de npm for transferido da seguinte forma (sobre as distribuições do Linux poderá ter de utilizar **sudo**):

```bash
npm install -g <path to downloaded tar file>
```

## <a name="option-2-use-an-installer"></a>Opção 2: Utilizar um instalador
Se utilizar um computador Mac ou Windows, os programas de instalação do CLI seguintes estão disponíveis para transferência:

* [Instalador do Mac OS X][mac-installer]
* [Windows MSI][windows-installer]

> [!TIP]
> No Windows, também pode transferir o [instalador de plataforma Web](https://go.microsoft.com/?linkid=9828653) para instalar a CLI. Este instalador dá-lhe a opção para instalar o SDK do Azure adicionais e ferramentas de linha de comandos depois de instalar a CLI.

## <a name="option-3-use-a-docker-container"></a>Opção 3: Utilizar um contentor de Docker
Se tiver configurado o computador como um [Docker](https://docs.docker.com/engine/understanding-docker/) anfitrião, pode executar a CLI do Azure mais recente 1.0 num contentor de Docker. Execute o seguinte comando (em que poderá ter de utilizar as distribuições do Linux **sudo**):

```bash
docker run -it microsoft/azure-cli
```

## <a name="run-azure-cli-10-commands"></a>Executar comandos da CLI do Azure 1.0
Após a CLI do Azure 1.0 está instalado, execute o **azure** comando da sua interface de utilizador da linha de comandos (Bash, Terminal, linha de comandos e assim sucessivamente). Por exemplo, para executar o comando de ajuda, escreva o seguinte:

```azurecli
azure help
```

> [!NOTE]
> Em algumas distribuições de Linux, poderá receber um erro semelhante `/usr/bin/env: ‘node’: No such file or directory`. Este erro provém de instalações recentes do Node.js a ser instalado em /usr/bin/nodejs. Para o corrigir, crie uma ligação simbólica para /usr/bin/node ao executar o comando:

```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Para ver a versão da CLI do Azure 1.0 instalou, escreva o seguinte:

```azurecli
azure --version
```

Agora, está pronto! Para aceder a todos os os comandos da CLI para trabalhar com os seus próprios recursos, [ligar a sua subscrição do Azure a partir da CLI do Azure](xplat-cli-connect.md).

> [!NOTE]
> Quando utilizar a CLI do Azure pela primeira vez, é apresentada uma mensagem a perguntar se pretende permitir à Microsoft recolher informações de utilização. A participação é voluntária. Se optar por participar, pode parar em qualquer altura executando `azure telemetry --disable`. Para ativar a participação em qualquer altura, execute `azure telemetry --enable`.

## <a name="update-the-cli"></a>Atualizar a CLI
Microsoft frequentemente versões versões atualizadas da CLI do Azure. Reinstale a CLI do AZURE utilizando o instalador do seu sistema operativo, ou executar o contentor de Docker mais recente. Ou, se tiver o Node.js e npm instalado mais recentes, atualizar, escrevendo o seguinte (em que poderá ter de utilizar as distribuições do Linux **sudo**).

```bash
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Ativar a conclusão de separador
Conclusão de separador de comandos da CLI é suportada para Mac e Linux.

Para ativá-la no zsh, execute:

```bash
echo '. <(azure --completion)' >> .zshrc
```

Para ativá-la no bash, execute:

```bash
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Passos seguintes
* [Ligar a partir da CLI para a sua subscrição do Azure](xplat-cli-connect.md) para criar e gerir recursos do Azure.
* Para obter mais informações sobre a CLI do Azure, código de origem da transferência, problemas de relatório, ou a contribuir para o projeto, visite o [repositório do GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).
* Se tiver dúvidas sobre como utilizar a CLI do Azure ou do Azure, visite o [fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).


[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: /cli/azure/get-started-with-az-cli2
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
