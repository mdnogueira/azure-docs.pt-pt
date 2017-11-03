---
title: Proteger um servidor web com certificados SSL no Azure | Microsoft Docs
description: Saiba como proteger o servidor de web NGINX com certificados SSL numa VM com Linux no Azure
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
ms.date: 07/17/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e3ad8a5c08b739d8b2c6e224db0c8f88c1893ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Proteger um servidor web com certificados SSL numa máquina virtual com Linux no Azure
Para proteger servidores web, um certificado Secure Sockets mais tarde (SSL) pode ser utilizado para encriptar o tráfego da web. Estes certificados SSL podem ser armazenados no Cofre de chaves do Azure e permitem implementações seguras de certificados para máquinas de virtuais (VMs) do Linux no Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre de chaves
> * Criar uma VM e instalar o servidor de web NGINX
> * Inserir o certificado para a VM e configurar NGINX com um enlace SSL

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Descrição geral
O Cofre de chaves do Azure salvaguarda as chaves criptográficas e segredos, esses certificados ou palavras-passe. O Cofre de chaves ajuda a simplificar o processo de gestão de certificados e permite-lhe manter o controlo de teclas que aceder a esses certificados. Pode criar um certificado autoassinado no interior do Cofre de chaves ou carregar um certificado existente, fidedigno que já possui.

Em vez de utilizar uma imagem de VM personalizada, incluindo certificados integrada-in, inserir certificados para uma VM em execução. Este processo garante que os certificados mais atualizadas à sua estão instalados num servidor web durante a implementação. Se renovar ou substituir um certificado, também não tem de criar uma nova imagem VM personalizada. Os certificados mais recentes são automaticamente injetados como criar VMs adicionais. Durante o processo de todo, os certificados nunca deixam a plataforma do Azure ou estão expostos num script, o histórico da linha de comandos ou o modelo.


## <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves do Azure
Antes de poder criar um cofre de chaves e certificados, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupSecureWeb* no *eastus* localização:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Em seguida, crie um cofre de chaves com [az keyvault criar](/cli/azure/keyvault#create) e ative-a para utilização quando implementar uma VM. Cada Cofre de chaves requer um nome exclusivo e deve ser todas as letras maiúsculas e minúsculas. Substitua  *<mykeyvault>*  no exemplo seguinte, com o seu próprio nome exclusivo do Cofre de chaves:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Gerar um certificado e armazenar no Cofre de chaves
Para utilização em produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [importação de certificados de keyvault az](/cli/azure/certificate#import). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [Criar certificado de keyvault az](/cli/azure/certificate#create) que utiliza a política de certificado predefinido:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Preparar um certificado para utilização com uma VM
Para utilizar o certificado durante a VM processo de criar, obter o ID do certificado com [az keyvault lista-as versões do segredo](/cli/azure/keyvault/secret#list-versions). Converter o certificado com [az vm formato-secret](/cli/azure/vm#format-secret). O seguinte exemplo atribui o resultado destes comandos variáveis facilidade de utilização nos passos:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Criar uma configuração de nuvem init para proteger NGINX
[Nuvem init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux como efetua o arranque pela primeira vez. Pode utilizar a cloud init para instalar pacotes e escrever em ficheiros, ou para configurar utilizadores e de segurança. Como é executado na nuvem init durante o processo de arranque inicial, existem não existem passos adicionais ou agentes necessários para aplicar a configuração.

Quando cria uma VM, certificados e chaves são armazenadas no protegida */var/lib/waagent/* diretório. Para automatizar a adicionar o certificado para a VM e configurar o servidor web, utilize init de nuvem. Neste exemplo, instalar e configurar o servidor de web NGINX. Pode utilizar o mesmo processo para instalar e configurar o Apache. 

Crie um ficheiro denominado *nuvem-init-web-server.txt* e cole a seguinte configuração:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Criar uma VM segura
Agora criar uma VM com [az vm criar](/cli/azure/vm#create). Os dados de certificado são injetados a partir do Cofre de chaves com o `--secrets` parâmetro. Passa a configuração de nuvem init com o `--custom-data` parâmetro:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos para que a VM ser criada, os pacotes para instalar e iniciar a aplicação. Quando a VM foi criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder ao site num browser.

Para permitir o tráfego web seguro alcançar a VM, abra a porta 443 a partir da Internet com [az vm open-porta](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testar a aplicação web seguro
Agora pode abrir um browser e introduza *https://<publicIpAddress>*  na barra de endereço. Forneça o suas próprias público endereço IP da VM criar o processo. Aceite o aviso de segurança se utilizou um certificado autoassinado:

![Aceitar o aviso de segurança do browser web](./media/tutorial-secure-web-server/browser-warning.png)

O site NGINX protegido, em seguida, é apresentado como no exemplo seguinte:

![Site NGINX está em execução segura de vista](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, protegidos por um servidor de web NGINX com um certificado SSL armazenado no Cofre de chaves do Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar um cofre de chaves do Azure
> * Gerar ou carregar um certificado para o Cofre de chaves
> * Criar uma VM e instalar o servidor de web NGINX
> * Inserir o certificado para a VM e configurar NGINX com um enlace SSL

Siga esta ligação para ver os exemplos de script pré-criadas máquina virtual.

> [!div class="nextstepaction"]
> [Exemplos de script de máquina virtual do Windows](./cli-samples.md)