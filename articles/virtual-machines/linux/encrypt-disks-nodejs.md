---
title: Encriptar discos numa VM com Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Como encriptar discos numa VM com Linux utilizando a CLI do Azure 1.0 e o modelo de implementação Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: b436f2d43c41000f4385889edb3fa3983d4a8c66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Encriptar discos numa VM com Linux utilizando a CLI do Azure 1.0
Para segurança avançada máquina virtual (VM) e conformidade, os discos virtuais no Azure podem ser encriptados inativos. Discos estão encriptados com as chaves criptográficas que são protegidas um cofre de chaves do Azure. Pode controla estas chaves criptográficas e pode auditar a sua utilização. Este artigo fornece detalhes sobre como encriptar discos virtuais numa VM com Linux utilizando a CLI do Azure 1.0 e o modelo de implementação Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#quick-commands) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, os seguintes detalhes de secção na base de comandos para encriptar os discos virtuais na VM. Mais informações e o contexto para cada passo é possível encontrar o resto do documento [Iniciar aqui](#overview-of-disk-encryption).

É necessário o [mais recente do Azure CLI 1.0](../../xplat-cli-install.md) instalado e inicia sessão utilizando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `myKeyVault`, e `myVM`.

Em primeiro lugar, ative o fornecedor do Cofre de chaves do Azure na sua subscrição do Azure e criar um grupo de recursos. O exemplo seguinte cria um nome de grupo de recursos `myResourceGroup` no `WestUS` localização:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Crie um cofre de chaves do Azure. O exemplo seguinte cria um cofre de chaves com o nome `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Criar uma chave criptográfica no seu Cofre de chaves e ative-a para a encriptação de disco. O exemplo seguinte cria uma chave denominada `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Crie um ponto final com o Azure Active Directory para processar a autenticação e troca de chaves criptográficas do Cofre de chaves. O `--home-page` e `--identifier-uris` não precisam de ser endereços encaminháveis real. Para o nível mais elevado de segurança, os segredos do cliente devem ser utilizados em vez de palavras-passe. A CLI do Azure atualmente não é possível gerar dos segredos do cliente. Só podem ser gerados dos segredos do cliente no portal do Azure. O exemplo seguinte cria um ponto de final do Azure Active Directory com o nome `myAADApp` e utiliza uma palavra-passe `myPassword`. Especifique a sua própria palavra-passe da seguinte forma:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Tenha em atenção o `applicationId` apresentados no resultado do comando anterior. Este ID de aplicação é utilizado nos passos seguintes:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Adicione um disco de dados para uma VM existente. O exemplo seguinte adiciona um disco de dados para uma VM chamada `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Reveja os detalhes para o seu Cofre de chaves e a chave que criou. É necessário o ID de Cofre de chave, o URI e a chave URL no passo final. O exemplo seguinte revê os detalhes para um cofre de chaves com o nome `myKeyVault` e chave denominada `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Encriptar os discos da seguinte forma, introduzir os seus próprios nomes de parâmetro ao longo de:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

A CLI do Azure não fornece verbosos erros durante o processo de encriptação. Para informações adicionais de resolução de problemas, consulte `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Como executar o comando anterior tem muitas variáveis e podem não obter muito indicação por que motivo o processo de falha, um exemplo de comando completo seria o seguinte:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Por fim, reveja o estado de encriptação novamente para confirmar que os discos virtuais agora foram encriptados. O exemplo seguinte verifica o estado de uma VM chamada `myVM` no `myResourceGroup` grupo de recursos:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Descrição geral de encriptação de disco
Discos virtuais em VMs do Linux são encriptados em rest utilizando [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Não há sem encargos de encriptação de discos virtuais no Azure. As chaves criptográficas são armazenadas no Cofre de chaves do Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves nos módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 normas de nível 2. Manter o controlo destas chaves criptográficas e pode auditar a sua utilização. Estas chaves criptográficas são utilizados para encriptar e desencriptar discos virtuais anexados à VM. Um ponto final do Azure Active Directory fornece um mecanismo seguro para emitir estas chaves criptográficas como VMs estão ligados à corrente ou desligar.

O processo de encriptação de uma VM é o seguinte:

1. Crie uma chave criptográfica num cofre de chaves do Azure.
2. Configure a chave criptográfica para ser utilizada para encriptação de discos.
3. Para ler a chave criptográfica no Cofre de chaves do Azure, crie um ponto final com o Azure Active Directory com as permissões adequadas.
4. Emita o comando para encriptar os discos virtuais, especificando o ponto final do Azure Active Directory e a chave criptográfica adequada para ser utilizado.
5. O ponto final do Azure Active Directory pedidos a chave criptográfica necessária a partir do Cofre de chaves do Azure.
6. Os discos virtuais estão encriptados com a chave criptográfica fornecida.

## <a name="supporting-services-and-encryption-process"></a>Processo de encriptação e dos serviços de suporte
Encriptação de disco baseia-se nos seguintes componentes adicionais:

* **O Cofre de chaves do Azure** - utilizado para salvaguardar as chaves criptográficas e segredos utilizados para o processo de encriptação/desencriptação de disco.
  * Se existir, pode utilizar um cofre de chaves do Azure existente. Não dispõe de dedique um cofre de chaves para encriptação de discos.
  * Separar os limites administrativos e visibilidade chave, pode criar um cofre de chaves dedicado.
* **Azure Active Directory** -processa segura troca de chaves criptográficas necessárias e a autenticação para ações de pedido.
  * Normalmente, pode utilizar uma instância existente do Azure Active Directory para o alojamento da sua aplicação.
  * A aplicação é mais de um ponto final para os serviços do Cofre de chaves e a Máquina Virtual pedir e obter emitido as chaves criptográficas adequadas. Não estiver a desenvolver uma aplicação real, que se integra com o Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
Cenários suportados e os requisitos para encriptação de disco de:

* O seguinte servidor Linux SKUs - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
* Todos os recursos (por exemplo, o Cofre de chaves, a conta de armazenamento e a VM) tem de ser na mesma região do Azure e subscrição.
* Um padrão, D, DS, G e GS série VMs.

Encriptação de disco não é atualmente suportada nos seguintes cenários:

* Escalão básico VMs.
* VMs criadas utilizando o modelo de implementação clássica.
* A desativação da encriptação de disco de SO em VMs do Linux.
* A atualizar as chaves criptográficas numa VM com Linux já encriptados.

## <a name="create-the-azure-key-vault-and-keys"></a>Criar o Cofre de chaves do Azure e as chaves
Para concluir o resto deste guia, terá do [mais recente do Azure CLI 1.0](../../xplat-cli-install.md) instalado e inicia sessão utilizando o modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Em todos os exemplos de comando, substitua todos os parâmetros de exemplo com os seus próprios nomes, localização e valores de chave. Os exemplos seguintes utilizam uma convenção de `myResourceGroup`, `myKeyVault`, `myAADApp`, etc.

O primeiro passo é criar um cofre de chaves do Azure para armazenar as chaves criptográficas. O Cofre de chaves do Azure pode armazenar as chaves, os segredos ou palavras-passe que permitem-lhe implementá-los em segurança nos seus serviços e aplicações. Para a encriptação de disco virtual, pode utilizar o Cofre de chaves para armazenar uma chave criptográfica utilizada para encriptar ou desencriptar os discos virtuais.

Ativar o fornecedor do Cofre de chaves do Azure na sua subscrição do Azure e, em seguida, criar um grupo de recursos. O exemplo seguinte cria um grupo de recursos denominado `myResourceGroup` no `WestUS` localização:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

O Cofre de chaves do Azure que contém as chaves criptográficas e os recursos de computação associados, como o armazenamento e a própria VM tem de residir na mesma região. O exemplo seguinte cria um cofre de chaves do Azure com o nome `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Pode armazenar as chaves criptográficas utilizando software ou proteção de modelo de segurança de Hardware (HSM). Utilizar um HSM necessita de um cofre de chaves de premium. Não há um custos adicionais para criar um premium Cofre de chaves em vez de padrão Cofre de chaves que armazena as chaves protegidas por software. Para criar um cofre de chaves de premium, no passo anterior adicionar `--sku Premium` ao comando. O exemplo seguinte utiliza as chaves protegidas de software, uma vez que criámos um cofre de chaves padrão.

Para ambos os modelos de proteção, a plataforma do Azure tem de ser concedido acesso ao pedir as chaves criptográficas quando arranca a VM para desencriptar os discos virtuais. Criar uma chave de encriptação no seu Cofre de chaves e, em seguida, ativá-la para utilização com a encriptação de disco virtual. O exemplo seguinte cria uma chave denominada `myKey` e, em seguida, ativa-o para a encriptação de disco:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Criar a aplicação do Azure Active Directory
Quando são encriptados ou desencriptados discos virtuais, pode utilizar um ponto final para processar a autenticação e a troca de chaves criptográficas do Cofre de chaves. Este ponto final, uma aplicação do Azure Active Directory, permite que a plataforma do Azure pedir as chaves criptográficas adequadas em nome da VM. Uma instância do Azure Active Directory predefinida está disponível na sua subscrição, apesar de muitas organizações têm dedicado diretórios do Azure Active Directory.

Como não estiver a criar uma aplicação completa do Azure Active Directory, o `--home-page` e `--identifier-uris` parâmetros no exemplo seguinte não precisam de ser endereços encaminháveis real. O exemplo seguinte também especifica um segredo baseada em palavra-passe em vez de gerar chaves de dentro do portal do Azure. Como neste momento, não é possível efetuar a geração de chaves da CLI do Azure.

Crie a sua aplicação do Azure Active Directory. O exemplo seguinte cria uma aplicação com o nome `myAADApp` e utiliza uma palavra-passe `myPassword`. Especifique a sua própria palavra-passe da seguinte forma:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Anote o `applicationId` que é devolvido na saída do comando anterior. Este ID de aplicação é utilizado em alguns dos passos restantes. Em seguida, crie um nome principal de serviço (SPN) para que a aplicação está acessível no seu ambiente. Para encriptar ou desencriptar discos virtuais com sucesso, as permissões a chave criptográfica armazenada no Cofre de chaves tem de ser definidas para permitir a aplicação do Azure Active Directory para ler as chaves.

Criar o SPN e definir as permissões adequadas da seguinte forma:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Adicionar um disco virtual e rever o estado de encriptação
Para encriptar, na verdade, de alguns discos virtuais, permite adicionar um disco a uma VM existente. Adicione um disco de dados de 5Gb para uma VM existente da seguinte forma:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Atualmente, os discos virtuais não estão encriptados. Reveja o atual estado de encriptação da sua VM da seguinte forma:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Encriptar discos virtuais
Para encriptar agora os discos virtuais, pode reunir todos os componentes anteriores:

1. Especifique a aplicação Azure Active Directory e a palavra-passe.
2. Especifique o Cofre de chaves para armazenar os metadados para os discos encriptados.
3. Especifique as chaves criptográficas a utilizar para a encriptação real e a desencriptação.
4. Especifique se pretende encriptar o disco do SO, os discos de dados ou todos.

Permite que reveja os detalhes para o seu Cofre de chaves do Azure e a chave que criou, à medida que precisa do ID de Cofre de chave, o URI e, em seguida, chave URL no último passo:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Encriptar os discos virtuais utilizando o resultado do `azure keyvault show` e `azure keyvault key show` comandos da seguinte forma:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como executar o comando anterior tem muitas variáveis, o exemplo seguinte é o comando completo de referência:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

A CLI do Azure não fornece verbosos erros durante o processo de encriptação. Para informações adicionais de resolução de problemas, consulte `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` na VM estiver a encriptar.

Por fim, permite rever o estado de encriptação novamente para confirmar que os discos virtuais agora foram encriptados:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Assim que encriptou os discos de dados, pode adicionar mais tarde discos virtuais adicionais para a VM e também encriptá-las. Quando executa o `azure vm enable-disk-encryption` comando, incrementar a versão de sequência utilizando o `--sequence-version` parâmetro. Este parâmetro de versão sequência permite-lhe efetuar operações repetidas na VM do mesma.

Por exemplo, permite adicionar um disco virtual segundo à sua VM, da seguinte forma:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Volte a executar o comando para encriptar os discos virtuais, este tempo, adicionar o `--sequence-version` parâmetro e incrementando o valor do nosso primeiro executado da seguinte forma:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como gerir o Cofre de chaves do Azure, incluindo a eliminação de chaves criptográficas e os cofres, consulte [gerir o Cofre de chaves ao utilizar a CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Para obter mais informações sobre a encriptação de disco, tais como preparar uma VM personalizada encriptada para carregar para o Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
