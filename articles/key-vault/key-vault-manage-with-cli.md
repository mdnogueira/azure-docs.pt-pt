---
title: Gerir o Cofre de chaves do Azure com a CLI | Microsoft Docs
description: Utilize este tutorial para automatizar tarefas comuns no Cofre de chaves utilizando a CLI
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>Gerir o Cofre de chaves ao utilizar a CLI

Utilize este tutorial para ajudá-lo com o Cofre de chaves do Azure para criar um contentor protegido (cofre) no Azure. O Cofre de chaves do Azure é utilizado para armazenar e gerir chaves criptográficas e segredos. Este artigo explica o processo de utilização da Interface de linha de comandos de plataforma do Azure para criar um cofre. Em seguida, irá interagir com o cofre para desempenhar várias operações comuns. 

O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

**Tempo estimado para concluir:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever a aplicação Azure que inclui um dos passos. Este exemplo de aplicação é utilizado para mostrar como uma aplicação pode estar autorizada a utilizar uma chave ou segredo armazenados no Cofre de chaves.
> Este artigo foca-se na configuração do Cofre de chaves do Azure utilizando a Interface de linha de comandos entre plataformas. Para obter instruções do Azure PowerShell, consulte [tutorial equivalente](key-vault-get-started.md).

Para obter informações gerais sobre o Cofre de Chaves do Azure, consulte o artigo [O que é o Cofre de Chaves do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se para obter um [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de linha de comandos versão 0.9.1 ou posterior. Para instalar a versão mais recente e ligar à sua subscrição do Azure, consulte [instale e Configure a Interface de linha de comandos de plataforma do Azure](../cli-install-nodejs.md).
* Uma aplicação que será configurada para utilizar a chave ou a palavra-passe que criar neste tutorial. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o ficheiro Readme.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obter ajuda com Interface de linha de comandos de plataforma do Azure

Este tutorial parte do princípio de que está familiarizado com a interface de linha de comandos (Bash, Terminal, linha de comandos)

O – ajuda ou -h parâmetro pode ser utilizado para ver a ajuda para comandos específicos. Em alternativa, a azure ajuda [comando] formato [opções] também pode ser utilizado para devolver as mesmas informações. Por exemplo, todos os seguintes comandos devolvem as mesmas informações:

    azure account set --help

    azure account set -h

    azure help account set

Quando em dúvida sobre os parâmetros necessários para um comando, consulte a ajuda para utilizar - ajuda, -h ou do azure ajuda [comando].

Também pode ler os tutoriais seguintes para se familiarizar com o modelo da implementação do Azure Resource Manager na Interface de linha de comandos de plataforma do Azure:

* [Como instalar e configurar a Interface de linha de comandos de plataforma do Azure](../cli-install-nodejs.md)
* [Utilizar a Interface de linha de comandos de plataforma do Azure com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições

Para iniciar sessão no Azure utilizando o seguinte comando:

```azurecli-interactive
azure login -u username -p password
```

ou se pretende iniciar sessão, escrevendo interativamente

```azurecli-interactive
azure login
```

Se tiver várias subscrições e pretender especificar uma subscrição particular para utilizar com o Cofre de Chaves do Azure, introduza o seguinte para ver as subscrições da sua conta:

```azurecli-interactive
azure account list
```

Em seguida, para especificar a subscrição a utilizar, introduza:

```azurecli-interactive
azure account set <subscription name>
```

Para obter mais informações sobre a configuração de Interface de linha de comandos de plataforma do Azure, consulte [como instalar e configurar a Interface de linha de comandos de várias plataformas de Azure](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Mudar para utilizar o Azure Resource Manager
Requer que o Cofre de chaves do Azure Resource Manager, por isso, escreva o seguinte para mudar para modo Azure Resource Manager:

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos
Ao utilizar o Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Iremos criar um novo grupo de recursos 'ContosoResourceGroup' para este tutorial.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é a localização. Para a localização, utilize o comando `azure location list` para identificar como especificar uma localização alternativa neste exemplo. Se precisar de mais informações, escreva:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registar o fornecedor de recursos do Cofre de chaves
Certifique-se de que fornecedor de recursos do Cofre de chaves está registado na sua subscrição:

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

Isto só deverá ser efetuada uma vez por subscrição.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Utilize o `azure keyvault create` comando para criar um cofre de chaves. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome do Cofre de chaves e a localização geográfica.

Por exemplo:
- Se utilizar o nome do Cofre de **ContosoKeyVault**
- O nome do grupo de recursos de **ContosoResourceGroup**
- A localização do **Oriental** tipo:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

O resultado deste comando mostra as propriedades do Cofre de chaves que acabou de criar. As duas propriedades mais importantes são:

* **Nome**: no exemplo é ContosoKeyVault. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **vaultUri**: no exemplo é https://contosokeyvault.vault.azure.net. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Por enquanto, não foi autorizado mais ninguém.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao cofre de chaves

Se pretender que o Cofre de chaves do Azure para criar uma chave protegida por software para si, utilize o `azure key create` de comandos e escreva o seguinte:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

No entanto, se tiver uma chave existente num ficheiro. pem guardado como ficheiro local num ficheiro denominado softkey.pem que pretende carregar para o Cofre de chaves do Azure, escreva o seguinte para importar a chave da. Ficheiro PEM, que protege a chave por software no serviço Cofre de chaves:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

Agora, pode referenciar a chave criou ou carregou no Cofre de chaves do Azure, utilizando o seu respetivo URI. Utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** sempre obter a versão atual e utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/ cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar um segredo no cofre, que é uma palavra-passe designada SQLPassword e que tem o valor de Pa$ $ w0rd no Cofre de chaves do Azure, escreva o seguinte:

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

Agora, pode referenciar esta palavra-passe que adicionou ao Cofre de Chaves do Azure utilizando o seu respetivo URI. Aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão mais recente e aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vejamos a chave ou segredo que acabou de criar:

* Para ver a sua chave, escreva: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Para ver o seu segredo, escreva: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registar uma aplicação com o Azure Active Directory

Geralmente, este passo deve ser realizado por um programador, num computador à parte. Não é específico ao Cofre de chaves do Azure, mas está incluído aqui por questões de exaustividade.

> [!IMPORTANT]
> Para concluir o tutorial, a sua conta, o Cofre e a aplicação que irá registar neste passo devem todos estar no mesmo diretório do Active Directory do Azure.

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory. Para tal, o proprietário da aplicação deve primeiro registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:

- Um **ID da aplicação** 
- Um **chave de autenticação** (também conhecido como o segredo partilhado). 

Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. A maneira como a aplicação é configurada para o fazer depende da aplicação. Para o [aplicação de exemplo do Cofre de chaves](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário da aplicação define estes valores no ficheiro App. config.

Para obter passos detalhados sobre como registar uma aplicação no Azure Active Directory que deve consultar o artigo intitulado [integrar aplicações com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) ou [portal de utilização para criar um Active Directory do Azure Aplicação de diretório e um principal de serviço que pode aceder aos recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md) para registar a aplicação no Azure Active Directory:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo, clique em **registos de aplicação**. Se não vir a registos de aplicação, clique em **mais serviços** e achar não existe.  
    >[!NOTE]
    Tem de selecionar o mesmo diretório que contém a subscrição do Azure com a qual criou o seu Cofre de chaves. 
3. Clique em **novo registo de aplicação**.
4. No **criar** painel forneça um nome para a sua aplicação e, em seguida, selecione **aplicação WEB e/ou API WEB** (predefinição) e especifique o **URL de início de sessão no** para web aplicação. Se não tiver estas informações neste momento, pode tornar para este passo (por exemplo, pode especificar http://test1.contoso.com). Não interessa se estes sites existem. 

   ![Novo registo de aplicação](./media/key-vault-manage-with-cli/new-application-registration.png)

5. Clique no botão **Criar**.
6. Quando o registo de aplicações for concluído pode ver a lista de aplicações registadas. Localize a aplicação que acabou de registado e clique na mesma.
7. Clique em de **aplicação registada** cópia do painel a **ID da aplicação**
8. Clique em **todas as definições**
9. No **definições** painel clique em **chaves**
10. Escreva uma descrição no **descrição da chave** e selecione uma duração e, em seguida, clique em **guardar**. A página é atualizada e mostra agora um valor de chave. 
11. Irá utilizar o **ID da aplicação** e **chave** informações no passo seguinte para definir as permissões do seu cofre.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar a aplicação a utilizar a chave ou o segredo
Para autorizar a aplicação a aceder à chave ou segredo no cofre, utilize:

```azurecli-interactive
azure keyvault set-policy
```

Por exemplo, se o nome do seu Cofre ContosoKeyVault e a aplicação que pretende autorizar tem um ID de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e se pretende autorizar a aplicação a desencriptar e assinar com chaves no seu Cofre, em seguida, execute o seguinte:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Se estiver a executar numa linha de comandos do Windows, deve substituir plicas com aspas duplas e também como escape as aspas duplas internas. Por exemplo: "[\"desencriptar\",\"sessão\"]".
> 

Se pretender autorizar essa mesma aplicação a ler os segredos no seu cofre, execute o seguinte procedimento:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se pretender utilizar um módulo de segurança de hardware (HSM)
Para mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda a [Eliminar o cofre de chaves e as chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar estas chaves protegidas de HSM, tem de ter uma subscrição do cofre que suporta chaves protegidas de HSM.

Quando cria o keyvault, adicione o parâmetro de "sku":

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro de destino para 'HSM':

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro. pem no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [como utilizar chaves de HSM-Protected com o Cofre de chaves do Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminar o cofre de chaves e as chaves e segredos associados
Se já não precisar do Cofre de chaves e a chave ou segredo que contiver, pode eliminar o Cofre de chaves utilizando o comando de eliminação de keyvault do azure:

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos de Interface de linha de comandos de plataforma do Azure
Outros comandos que que poderá ser útil para gerir o Cofre de chaves do Azure.

Este comando lista uma tabela de apresentação de todas as chaves e propriedades selecionadas:

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

Este comando apresenta uma lista completa das propriedades para a chave especificada:

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Este comando lista uma tabela de apresentação de todos os nomes secretos e propriedades selecionadas:

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

Eis um exemplo de como remover uma chave específica:

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Eis um exemplo de como remover um segredo específico:

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>Passos seguintes
- Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).
- Para obter informações gerais sobre o Cofre de Chaves do Azure, consulte o artigo [O que é o Cofre de Chaves do Azure?](key-vault-whatis.md)
- Como trabalhar com o Cofre de chaves do Azure com o Powershell [começar a utilizar o Cofre de chaves do Azure](key-vault-get-started.md).


