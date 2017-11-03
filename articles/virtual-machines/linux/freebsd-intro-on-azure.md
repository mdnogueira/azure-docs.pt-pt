---
title: "Introdução ao FreeBSD no Azure | Microsoft Docs"
description: "Saiba como utilizar FreeBSD máquinas de virtuais no Azure"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 2369bc893d28cf6f6174376eb961049b651c66a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este tópico fornece uma descrição geral da execução de uma máquina virtual de FreeBSD no Azure.

## <a name="overview"></a>Descrição geral
FreeBSD do Microsoft Azure é um sistema operativo do computador avançadas utilizado para servidores de energia moderna, ambientes de trabalho e incorporados plataformas.

Microsoft Corporation é disponibilizar imagens de FreeBSD no Azure com o [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) pré-configurados. Atualmente, as seguintes versões de FreeBSD são oferecidas como imagens pela Microsoft:

- FreeBSD 10.3-versão
- FreeBSD 11.0-versão
- FreeBSD 11.1-versão

O agente é responsável pela comunicação entre as VM FreeBSD e os recursos de infraestrutura do Azure para as operações, tais como o aprovisionamento da VM na primeira utilização (nome de utilizador, palavra-passe ou chave SSH, o nome do anfitrião, etc.) e a ativação da funcionalidade de extensões VM seletivos.

Para versões futuras do FreeBSD, a estratégia é permanecerá atual e disponibilizar as versões mais recentes pouco tempo depois de serem publicados pela equipa de engenharia do FreeBSD versão.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementar uma máquina virtual de FreeBSD
Implementar uma máquina virtual de FreeBSD é um processo simples utilizando uma imagem do Azure Marketplace do portal do Azure:

- [FreeBSD 10.3 no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
- [FreeBSD 11.0 no Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/)
- [FreeBSD 11.1 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD111)

### <a name="create-a-freebsd-vm-through-azure-cli-20-on-freebsd"></a>Criar uma VM de FreeBSD através da CLI do Azure 2.0 no FreeBSD
Primeiro tem de instalar [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) apesar de os seguintes comandos numa máquina FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se bash não está instalado no seu computador FreeBSD, execute o seguinte comando antes da instalação. 

```bash
sudo pkg install bash
```

Se o python não está instalado no seu computador FreeBSD, execute os seguintes comandos antes da instalação. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante a instalação, é-lhe perguntado `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Se responder `y` e introduza `/etc/rc.conf` como `a path to an rc file to update`, pode satisfazer o problema `ERROR: [Errno 13] Permission denied`. Para resolver este problema, só deverá conceder a operação de escrita à direita para o utilizador atual contra o ficheiro `etc/rc.conf`.

Agora pode iniciar sessão no Azure e criar a VM FreeBSD. Segue-se um exemplo para criar uma VM de 11.0 FreeBSD. Também pode adicionar o parâmetro `--public-ip-address-dns-name` com um nome globalmente exclusivo de DNS para um IP público criado recentemente. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, pode iniciar sessão para a VM FreeBSD através do endereço de ip impresso na saída do acima implementação. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões VM para FreeBSD
Seguem-se as extensões de VM suportadas no FreeBSD.

### <a name="vmaccess"></a>VMAccess
O [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) extensão pode:

* Repor a palavra-passe do utilizador sudo original.
* Crie um novo utilizador de sudo com a palavra-passe especificada.
* Defina a chave de anfitrião público com a chave fornecida.
* Repor a chave de anfitrião público fornecida durante o aprovisionamento de VM se a chave do anfitrião não for fornecida.
* Abra a porta SSH (22) e restaurar o sshd_config se reset_ssh estiver definido como true.
* Remova o utilizador existente.
* Verifique os discos.
* Repare um disco adicionado.

### <a name="customscript"></a>CustomScript
O [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) extensão pode:

* Se for indicado, transfira os scripts personalizados do Storage do Azure ou armazenamento público externo (por exemplo, o GitHub).
* Execute o script de ponto de entrada.
* Suporta comandos inline.
* Converta o estilo de Windows tabulação na shell e Python scripts automaticamente.
* Remova automaticamente LM na shell e Python scripts.
* Proteger os dados confidenciais do CommandToExecute.

> [!NOTE]
> FreeBSD VM só suporta CustomScript versão 1. x, por agora.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: os nomes de utilizador, palavras-passe e chaves SSH
Quando estiver a criar uma máquina virtual de FreeBSD utilizando o portal do Azure, tem de fornecer um nome de utilizador, palavra-passe ou chave pública SSH.
Para implementar uma máquina virtual de FreeBSD no Azure, os nomes de utilizador não tem de corresponder ao nomes das contas de sistema (UID < 100) já está presente na máquina virtual ("raiz", por exemplo).
Atualmente, apenas a RSA chave SSH é suportada. Tem de começar com uma chave SSH com várias linhas `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>A obtenção de privilégios de Superutilizador
A conta de utilizador que é especificada durante a implementação da instância de máquina virtual no Azure é uma conta com privilégios. O pacote de sudo foi instalado na imagem FreeBSD publicada.
Depois de que tem sessão iniciada através desta conta de utilizador, pode executar comandos como raiz utilizando a sintaxe do comando.

```
$ sudo <COMMAND>
```

Opcionalmente, pode obter uma shell de raiz utilizando `sudo -s`.

## <a name="known-issues"></a>Problemas conhecidos
O [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.2 tem um [problema conhecido] (https://github.com/Azure/WALinuxAgent/pull/517) que provoca a falha de aprovisionar para FreeBSD VM no Azure. A correção capturada pelos [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.3 e versões posteriores. 

## <a name="next-steps"></a>Passos seguintes
* Aceda a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd110/) para criar uma VM FreeBSD.
* Se pretender colocar o seus próprios FreeBSD para o Azure, consulte [criar e carregar um VHD FreeBSD para o Azure](classic/freebsd-create-upload-vhd.md).
