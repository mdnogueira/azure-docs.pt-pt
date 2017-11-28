---
title: Gerir o Cofre de chaves do Azure com a CLI | Microsoft Docs
description: Utilize este tutorial para automatizar tarefas comuns no Cofre de chaves utilizando o CLI 2.0
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: barclayn
ms.openlocfilehash: eaeb50ca8a83fcfee6689acf549f20ba5d44c51d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Gerir o Cofre de chaves a utilizar o CLI 2.0

Este artigo abrange como começar a trabalhar com o Cofre de chaves do Azure utilizando o 2.0 CLI do Azure. Pode ver informações sobre:
- Como criar um contentor protegido (cofre) no Azure
- Como armazenar e gerir chaves criptográficas e segredos no Azure. 
- O processo de utilizar a Interface de linha de comandos de plataforma do Azure para criar um cofre que contém uma chave ou a palavra-passe que, em seguida, pode utilizar com uma aplicação do Azure. 
- Como uma aplicação, em seguida, pode utilizar essa chave ou a palavra-passe.

O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

**Tempo estimado para concluir:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever a aplicação Azure que um dos passos inclui, que mostra como autorizar uma aplicação para utilizar uma chave ou segredo no Cofre de chaves.
>

Para obter uma descrição geral do Cofre de chaves do Azure, consulte [que é o Cofre de chaves do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, deve ter o seguinte:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de linha de comandos versão 2.0 ou posterior. Para instalar a versão mais recente e ligar à sua subscrição do Azure, consulte [instalar e configurar o 2.0 de Interface de linha de comandos de plataforma do Azure](/cli/azure/install-azure-cli).
* Uma aplicação que será configurada para utilizar a chave ou a palavra-passe que criar neste tutorial. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o ficheiro Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obter ajuda com Interface de linha de comandos de plataforma do Azure
Este tutorial parte do princípio de que está familiarizado com a interface de linha de comandos (Bash, Terminal, linha de comandos)

O – ajuda ou -h parâmetro pode ser utilizado para ver a ajuda para comandos específicos. Em alternativa, a azure ajuda [comando] formato [opções] também pode ser utilizado para devolver as mesmas informações. Por exemplo, todos os seguintes comandos devolvem as mesmas informações:

```azurecli-interactive
az account set --help
az account set -h
```

Quando em dúvida sobre os parâmetros necessários para um comando, consulte a ajuda para utilizar - ajuda, -h ou az ajudar [comando].

Também pode ler os tutoriais seguintes para se familiarizar com o Azure Resource Manager na Interface de linha de comandos de plataforma do Azure:

* [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
* [Introdução ao Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições
Inicie sessão com uma conta institucional, utilize o seguinte comando:

```azurecli-interactive
az login -u username@domain.com -p password
```

ou se pretende iniciar sessão, escrevendo interativamente

```azurecli-interactive
az login
```

Se tiver várias subscrições e pretender especificar uma subscrição particular para utilizar com o Cofre de Chaves do Azure, introduza o seguinte para ver as subscrições da sua conta:

```azurecli-interactive
az account list
```

Em seguida, para especificar a subscrição a utilizar, introduza:

```azurecli-interactive
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre a configuração de Interface de linha de comandos de plataforma do Azure, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos
Ao utilizar o Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Iremos criar um novo grupo de recursos 'ContosoResourceGroup' para este tutorial.

```azurecli-interactive
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é a localização. Para obter uma lista de todas as localizações possíveis tipo:

```azurecli-interactive
az account list-locations
``` 

Se precisar de mais informações, escreva: 

```azurecli-interactive
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>Registar o fornecedor de recursos do Cofre de chaves
Ao tentar criar um novo cofre de chaves, poderá ver o erro "a subscrição não está registada para utilizar o espaço de nomes 'Keyvault'". Se essa mensagem for apresentada, certifique-se de que o fornecedor de recursos do Cofre de chaves está registado na sua subscrição:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
Isto só deverá ser efetuada uma vez por subscrição.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Utilize o `az keyvault create` comando para criar um cofre de chaves. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome do Cofre de chaves e a localização geográfica.

Por exemplo:

- Se utilizar o nome do Cofre de **ContosoKeyVault**
- O nome do grupo de recursos de **ContosoResourceGroup**
- A localização do **Ásia Oriental**

teria de escrever:

```azurecli-interactive
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

O resultado deste comando mostra as propriedades do Cofre de chaves que acabou de criar. As duas propriedades mais importantes são:

* **nome**: no exemplo é ContosoKeyVault. Irá utilizar este nome para outros comandos do Cofre de chaves.
* **vaultUri**: no exemplo é https://contosokeyvault.vault.azure.net. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Por enquanto, não foi autorizado mais ninguém.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao cofre de chaves

Se pretender que o Cofre de chaves do Azure para criar uma chave protegida por software para si, utilize o `az key create` de comandos e escreva o seguinte:
```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
No entanto, se tiver uma chave existente num ficheiro. pem guardado como ficheiro local num ficheiro denominado softkey.pem que pretende carregar para o Cofre de chaves do Azure, escreva o seguinte para importar a chave da. Ficheiro PEM, que protege a chave por software no serviço Cofre de chaves:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

Agora, pode referenciar a chave criou ou carregou no Cofre de chaves do Azure, utilizando o seu respetivo URI. Utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** sempre obter a versão atual e utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/ cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar um segredo no cofre, que é uma palavra-passe designada SQLPassword e que tem o valor de Pa$ $ w0rd no Cofre de chaves do Azure, escreva o seguinte:

```azurecli-interactive
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Agora, pode referenciar esta palavra-passe que adicionou ao Cofre de Chaves do Azure utilizando o seu respetivo URI. Aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão mais recente e aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vejamos a chave ou segredo que acabou de criar:

* Para ver a sua chave, escreva: 

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Para ver o seu segredo, escreva: 

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registar uma aplicação com o Azure Active Directory
Geralmente, este passo deve ser realizado por um programador, num computador à parte. -Não é específico ao Cofre de chaves do Azure, embora esteja incluído aqui, para deteção.

> [!IMPORTANT]
> Para concluir o tutorial, a sua conta, o cofre e a aplicação que irá registar neste passo devem todos estar no mesmo diretório do Azure.
>
>

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory. Para tal, o proprietário da aplicação deve primeiro registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:

- **ID da Aplicação** 
- **Chave de autenticação** (também conhecida como segredo partilhado). 

Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. A maneira como a aplicação é configurada para o fazer depende da aplicação. Na [aplicação de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário da aplicação define estes valores no ficheiro app.config.

Para obter passos detalhados sobre como registar uma aplicação no Azure Active Directory que deve consultar o artigo intitulado [integrar aplicações com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) ou [portal de utilização para criar um Active Directory do Azure Aplicação de diretório e um principal de serviço que pode aceder aos recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para registar a aplicação no Azure Active Directory:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo, clique em **Registos de aplicações**. Se não vir registos de aplicações, clique em **mais serviços** e encontre-as aí.  
[!NOTE]
Tem de selecionar o mesmo diretório que contém a subscrição do Azure com a qual criou o seu cofre de chaves. 
3. Clique em **Novo registo de aplicação**.
4. No painel **Criar**, indique um nome para a aplicação e selecione **APLICAÇÃO WEB E/OU API WEB** (a predefinição) e especifique o **URL DE INÍCIO DE SESSÃO** da sua aplicação Web. Se não tiver estas informações neste momento, pode inventá-las para este passo (por exemplo, pode indicar http://test1.contoso.com). Não interessa se estes sites existem. 

    ![Novo registo de aplicação](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Confirme que escolhe **APLICAÇÃO WEB E/OU API WEB**; se não o tiver feito, não verá a opção **chaves** nas definições.

5. Clique no botão **Criar**.
6. Quando o registo da aplicação for concluído, pode ver a lista de aplicações registadas. Encontre a aplicação que acabou de registar e clique na mesma.
7. Clique no painel **Aplicação registada** e copie o **ID de Aplicação**
8. Clique em **Todas as definições**
9. No painel **Definições**, clique em **Chaves**
9. Escreva uma descrição na caixa **Descrição da chave**, selecione uma duração e clique em **GUARDAR**. A página é atualizada e mostra agora um valor de chave. 
10. Vai utilizar as informações de **ID da Aplicação** e de **chave** no passo seguinte para definir as permissões do seu cofre.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar a aplicação a utilizar a chave ou o segredo
Para autorizar a aplicação a aceder à chave ou segredo no cofre, utilize o `az keyvault set-policy` comando.

Por exemplo, se o nome do seu Cofre ContosoKeyVault e a aplicação que pretende autorizar tem um ID de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e se pretende autorizar a aplicação a desencriptar e assinar com chaves no seu Cofre, em seguida, execute o seguinte:

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Se pretender autorizar essa mesma aplicação a ler os segredos no seu cofre, execute o seguinte procedimento:

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se pretender utilizar um módulo de segurança de hardware (HSM)
Para mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda a [Eliminar o cofre de chaves e as chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar estas chaves protegidas de HSM, tem de ter uma subscrição do cofre que suporta chaves protegidas de HSM.

Quando cria o keyvault, adicione o parâmetro de "sku":

```azurecli-interactive
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro de destino para 'HSM':

```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro. pem no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [como utilizar chaves de HSM-Protected com o Cofre de chaves do Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminar o cofre de chaves e as chaves e segredos associados
Se já não precisar do Cofre de chaves e a chave ou segredo que contiver, pode eliminar o Cofre de chaves utilizando o `az keyvault delete` comando:

```azurecli-interactive
az keyvault delete --name 'ContosoKeyVault'
```

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

```azurecli-interactive
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos de Interface de linha de comandos de plataforma do Azure
Outros comandos que que poderá ser útil para gerir o Cofre de chaves do Azure.

Este comando lista uma tabela de apresentação de todas as chaves e propriedades selecionadas:

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

Este comando apresenta uma lista completa das propriedades para a chave especificada:

```azurecli-interactive
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Este comando lista uma tabela de apresentação de todos os nomes secretos e propriedades selecionadas:

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Eis um exemplo de como remover uma chave específica:

```azurecli-interactive
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Eis um exemplo de como remover um segredo específico:

```azurecli-interactive
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Passos seguintes

- Para referência CLI do Azure completa para comandos do Cofre de chaves, consulte [referência da CLI do Cofre de chave](/cli/azure/keyvault).

- Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).

- Para obter informações sobre o Cofre de chaves do Azure e HSMs, consulte [como utilizar chaves de HSM-Protected com o Cofre de chaves do Azure](key-vault-hsm-protected-keys.md).
