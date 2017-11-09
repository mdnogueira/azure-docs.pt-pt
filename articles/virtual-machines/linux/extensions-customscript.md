---
title: Executar scripts personalizados em VMs do Linux no Azure | Microsoft Docs
description: "Automatizar tarefas de configuração de VM com Linux utilizando a extensão de Script personalizado"
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 3f8eeb71027d9bdd5c72570bd07d673136646e6c
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2017
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Com a extensão de Script personalizado do Azure com máquinas virtuais do Linux
A extensão de Script personalizado transfere e executa os scripts em máquinas virtuais do Azure. Esta extensão é útil para configuração de implementação de post, instalação de software ou qualquer outra configuração / tarefas de gestão. Scripts podem ser transferidos a partir do armazenamento do Azure ou outra localização de internet acessível ou fornecidos para a extensão de tempo de execução. A extensão de Script personalizado se integra com modelos Azure Resource Manager e também pode ser executada utilizando a CLI do Azure, o PowerShell, o portal do Azure ou a API de REST de Máquina Virtual do Azure.

Este documento fornece detalhes sobre como utilizar a extensão de Script personalizado na CLI do Azure e um modelo Azure Resource Manager e também fornece detalhes sobre os passos de resolução de problemas nos sistemas Linux.

## <a name="extension-configuration"></a>Configuração de extensão
A configuração de extensão de Script personalizado especifica coisas como a localização do script e o comando ser executado. Esta configuração pode ser armazenada em ficheiros de configuração, especificado na linha de comandos ou num modelo Azure Resource Manager. Os dados confidenciais podem ser armazenados numa configuração protegida, que é encriptada e desencriptada apenas dentro da máquina virtual. A configuração protegida é útil quando o comando de execução inclui segredos como uma palavra-passe.

### <a name="public-configuration"></a>Configuração pública
Esquema:

**Tenha em atenção** -estes nomes de propriedade são sensíveis a maiúsculas e minúsculas. Utilize os nomes como mostrado abaixo para evitar problemas de implementação.

* **commandToExecute**: (necessário, da cadeia) o script de ponto de entrada para executar
* **fileUris**: (opcional, matriz de cadeia) os URLs para os ficheiros a ser transferido.
* **Timestamp** (opcional, número inteiro) Utilize este campo apenas para acionar uma volte a executar o script alterando o valor deste campo.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Configuração protegida
Esquema:

**Tenha em atenção** -estes nomes de propriedade são sensíveis a maiúsculas e minúsculas. Utilize os nomes como mostrado abaixo para evitar problemas de implementação.

* **commandToExecute**: (opcional, cadeia) o script de ponto de entrada para executar. Utilize este campo em vez disso, se o comando contém segredos como palavras-passe.
* **storageAccountName**: (opcional, cadeia) o nome da conta de armazenamento. Se especificar credenciais do armazenamento, todas as fileUris tem de ser URLs de Blobs do Azure.
* **storageAccountKey**: (opcional, cadeia) a chave de acesso da conta de armazenamento.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>CLI do Azure
Quando utilizar a CLI do Azure para executar a extensão de Script personalizado, crie um ficheiro de configuração ou ficheiros que contenham no mínimo, o uri de ficheiro e o comando de execução do script.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Opcionalmente, as definições podem ser especificadas no comando como uma cadeia de formatação JSON. Isto permite que a configuração ser especificado durante a execução e sem um ficheiro de configuração diferente.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Exemplos da CLI do Azure

**Exemplo 1** -configuração pública com o ficheiro de script.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exemplo 2** -configuração pública com nenhum ficheiro de script.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exemplo 3** - um ficheiro de configuração público é utilizado para especificar o URI do ficheiro de script e um ficheiro de configuração protegido é utilizado para especificar o comando para ser executada.

Ficheiro de configuração pública:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Ficheiro de configuração protegido:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Comando CLI do Azure:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager
A extensão de Script personalizado do Azure pode ser executado no momento da implementação de Máquina Virtual com um modelo do Resource Manager. Para tal, adicione corretamente formatada JSON para o modelo de implementação.

### <a name="resource-manager-examples"></a>Exemplos de Gestor de recursos
**Exemplo 1** -configuração pública.

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

**Exemplo 2** -comando execução numa configuração protegida.

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Consulte o .net Core música arquivo de demonstração para obter um exemplo completo - [demonstração da loja de música](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Resolução de problemas
Quando é executada a extensão de Script personalizado, o script é criado ou transferido para um diretório semelhante ao seguinte exemplo. A saída do comando também é guardada para este diretório no `stdout` e `stderr` ficheiro.

```bash
/var/lib/waagent/custom-script/download/0/
```

A extensão de Script do Azure produz um registo, o que pode ser encontrado aqui.

```bash
/var/log/azure/custom-script/handler.log
```

Também é possível obter o estado de execução da extensão de Script personalizado com a CLI do Azure.

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

A saída aparente ser o seguinte texto:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras extensões de Script de VM, consulte [descrição geral de extensão de Script do Azure para Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

