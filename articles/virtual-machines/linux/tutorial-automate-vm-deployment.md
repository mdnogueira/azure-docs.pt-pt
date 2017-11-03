---
title: Personalizar uma VM com Linux no primeiro arranque no Azure | Microsoft Docs
description: Saiba como utilizar init de nuvem e o Cofre de chaves para customze VMs com Linux a primeira vez que estes arrancam no Azure
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
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e2d07a03902a8c837150da8d50ab9abec8d1c95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Como personalizar uma máquina virtual do Linux no primeiro arranque
Um tutorial anterior, aprendeu como para SSH para uma máquina virtual (VM) e manualmente instalar NGINX. Para criar as VMs de uma forma rápida e consistente, alguma forma de automatização é normalmente assim o desejar. Uma abordagem comum para personalizar uma VM no primeiro arranque é utilizar [nuvem init](https://cloudinit.readthedocs.io). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um ficheiro de configuração de nuvem init
> * Criar uma VM que utiliza um ficheiro de init de nuvem
> * Ver uma aplicação Node.js em execução depois da VM é criada
> * Utilizar o Cofre de chaves para armazenar em certificados
> * Automatizar implementações seguras de NGINX com init de nuvem


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli).  



## <a name="cloud-init-overview"></a>Descrição geral da nuvem init
[Nuvem init](https://cloudinit.readthedocs.io) é uma abordagem amplamente utilizada para personalizar uma VM com Linux como efetua o arranque pela primeira vez. Pode utilizar a cloud init para instalar pacotes e escrever em ficheiros, ou para configurar utilizadores e de segurança. Como é executado na nuvem init durante o processo de arranque inicial, existem não existem passos adicionais ou agentes necessários para aplicar a configuração.

Nuvem init também funciona em distribuições. Por exemplo, não utilize **apt get instalação** ou **yum instalar** para instalar um pacote. Em vez disso, pode definir uma lista dos pacotes para instalar. Nuvem init utiliza automaticamente a ferramenta de gestão do pacote nativo para distro que selecionar.

Estamos a trabalhar com os nossos parceiros de obter init cloud incluídos e a funcionar nas imagens que fornecem ao Azure. A tabela seguinte descreve a disponibilidade de nuvem init atual nas imagens de plataforma do Azure:

| Alias | Publicador | Oferta | SKU | Versão |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canónico |UbuntuServer |16.04 LTS |mais recente |
| UbuntuLTS |Canónico |UbuntuServer |14.04.5-LTS |mais recente |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |


## <a name="create-cloud-init-config-file"></a>Criar ficheiro de configuração de nuvem init
Para ver a nuvem-init em ação, crie uma VM que instala NGINX e executa uma simples "Olá, mundo" aplicação Node.js. A seguinte configuração de nuvem init instala os pacotes necessários, cria uma aplicação Node.js, em seguida, inicializar e inicia a aplicação.

Na sua shell atual, crie um ficheiro denominado *nuvem init.txt* e cole a seguinte configuração. Por exemplo, crie o ficheiro na Shell na nuvem não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de toda a nuvem-init é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Para obter mais informações sobre as opções de configuração de nuvem init, consulte [exemplos de configuração de nuvem init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Criar a máquina virtual
Antes de poder criar uma VM, crie um grupo de recursos com [criar grupo az](/cli/azure/group#create). O exemplo seguinte cria um grupo de recursos denominado *myResourceGroupAutomate* no *eastus* localização:

```azurecli-interactive 
az group create --name myResourceGroupAutomate --location eastus
```

Agora criar uma VM com [az vm criar](/cli/azure/vm#create). Utilize o `--custom-data` parâmetro para passar o ficheiro de configuração de nuvem init. Forneça o caminho completo para o *nuvem init.txt* configuração se guardou o ficheiro fora do diretório de trabalho presente. O exemplo seguinte cria uma VM chamada *myAutomatedVM*:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Demora alguns minutos para que a VM ser criada, os pacotes para instalar e iniciar a aplicação. Existem tarefas em segundo plano que continuam a ser executado após a CLI do Azure devolve ao pedido. Pode ser outro alguns minutos antes de poder aceder a aplicação. Quando a VM foi criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder à aplicação Node.js através de um browser web.

Para permitir o tráfego da web alcançar a VM, abra a porta 80 da Internet com [az vm open-porta](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Aplicação do teste web
Agora pode abrir um browser e introduza *http://<publicIpAddress>*  na barra de endereço. Forneça o suas próprias público endereço IP da VM criar o processo. A aplicação Node.js é apresentada como no exemplo seguinte:

![Ver o site NGINX em execução](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Inserir certificados a partir do Cofre de chaves
Esta secção opcional mostra como pode em segurança armazenar certificados no Cofre de chaves do Azure e inseri-los durante a implementação de VM. Em vez de utilizar uma imagem personalizada que inclui os certificados integrada-in, este processo garante que os certificados mais atualizadas à sua são injetados uma VM no primeiro arranque. Durante o processo, o certificado nunca deixa a plataforma do Azure ou está exposto num script, histórico de linha de comandos ou modelo.

O Cofre de chaves do Azure salvaguarda as chaves criptográficas e segredos, tais como certificados ou palavras-passe. O Cofre de chaves ajuda a simplificar o processo de gestão de chaves e permite-lhe manter o controlo de teclas que acede e encripta os seus dados. Este cenário apresenta alguns conceitos do Cofre de chaves para criar e utilizar um certificado, embora não é exaustiva geral sobre como utilizar o Cofre de chaves.

Os passos seguintes mostram como pode:

- Criar um cofre de chaves do Azure
- Gerar ou carregar um certificado para o Cofre de chaves
- Criar um segredo do certificado ao inserir na uma VM
- Criar uma VM e inserir o certificado

### <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves do Azure
Em primeiro lugar, crie um cofre de chaves com [az keyvault criar](/cli/azure/keyvault#create) e ative-a para utilização quando implementar uma VM. Cada Cofre de chaves requer um nome exclusivo e deve ser todas as letras maiúsculas e minúsculas. Substitua *mykeyvault* no exemplo seguinte, com o seu próprio nome exclusivo do Cofre de chaves:

```azurecli-interactive 
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Gerar certificado e armazenar no Cofre de chaves
Para utilização em produção, deve importar um certificado válido assinado por um fornecedor fidedigno com [importação de certificados de keyvault az](/cli/azure/keyvault/certificate#import). Para este tutorial, o exemplo seguinte mostra como pode gerar um certificado autoassinado com [Criar certificado de keyvault az](/cli/azure/keyvault/certificate#create) que utiliza a política de certificado predefinido:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparar o certificado para utilização com a VM
Para utilizar o certificado durante a VM processo de criar, obter o ID do certificado com [az keyvault lista-as versões do segredo](/cli/azure/keyvault/secret#list-versions). A VM tem do certificado num formato para injetá-lo no arranque, por isso, converta o certificado com [az vm formato-secret](/cli/azure/vm#format-secret). O seguinte exemplo atribui o resultado destes comandos variáveis facilidade de utilização nos passos:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Criar a configuração de nuvem init para proteger NGINX
Quando cria uma VM, certificados e chaves são armazenadas no protegida */var/lib/waagent/* diretório. Para automatizar a adicionar o certificado para a VM e configurar NGINX, pode utilizar uma configuração de nuvem-init atualizado do exemplo anterior.

Crie um ficheiro denominado *nuvem-init-secured.txt* e cole a seguinte configuração. Novamente, se utilizar a Shell de nuvem, crie o ficheiro de configuração de nuvem init há e não no seu computador local. Utilize `sensible-editor cloud-init-secured.txt` para criar o ficheiro e ver uma lista de editores disponíveis. Certifique-se de que o ficheiro de toda a nuvem-init é copiado corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Criar a VM segura
Agora criar uma VM com [az vm criar](/cli/azure/vm#create). Os dados de certificado são injetados a partir do Cofre de chaves com o `--secrets` parâmetro. O exemplo anterior, que também passa a configuração de nuvem init com o `--custom-data` parâmetro:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Demora alguns minutos para que a VM ser criada, os pacotes para instalar e iniciar a aplicação. Existem tarefas em segundo plano que continuam a ser executado após a CLI do Azure devolve ao pedido. Pode ser outro alguns minutos antes de poder aceder a aplicação. Quando a VM foi criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. Este endereço é utilizado para aceder à aplicação Node.js através de um browser web.

Para permitir o tráfego web seguro alcançar a VM, abra a porta 443 a partir da Internet com [az vm open-porta](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Aplicação do teste web seguro
Agora pode abrir um browser e introduza *https://<publicIpAddress>*  na barra de endereço. Forneça o suas próprias público endereço IP da VM criar o processo. Aceite o aviso de segurança se utilizou um certificado autoassinado:

![Aceitar o aviso de segurança do browser web](./media/tutorial-automate-vm-deployment/browser-warning.png)

O NGINX site protegida e o Node.js aplicação, em seguida, é apresentada como no exemplo seguinte:

![Site NGINX está em execução segura de vista](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, configurou as VMs no primeiro arranque com init de nuvem. Aprendeu a:

> [!div class="checklist"]
> * Criar um ficheiro de configuração de nuvem init
> * Criar uma VM que utiliza um ficheiro de init de nuvem
> * Ver uma aplicação Node.js em execução depois da VM é criada
> * Utilizar o Cofre de chaves para armazenar em certificados
> * Automatizar implementações seguras de NGINX com init de nuvem

Avançar para o próximo tutorial para saber como criar imagens VM personalizadas.

> [!div class="nextstepaction"]
> [Criar imagens de VM personalizadas](./tutorial-custom-images.md)
