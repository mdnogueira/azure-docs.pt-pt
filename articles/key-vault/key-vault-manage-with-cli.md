---
title: Gerir o Cofre de chaves do Azure com a CLI | Microsoft Docs
description: Utilize este tutorial para automatizar tarefas comuns no Cofre de chaves utilizando a CLI
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: bruceper
ms.openlocfilehash: c2565a742ce4f6ab5f7639a54c4a475f00cbc260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli"></a>Gerir o Cofre de chaves ao utilizar a CLI

O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução

Utilize este tutorial para ajudá-lo com o Cofre de Chaves do Azure para criar um contentor protegido (cofre) no Azure para armazenar e gerir chaves criptográficas e segredos no Azure. -Explica o processo de utilização da Interface de linha de comandos de plataforma do Azure para criar um cofre que contém uma chave ou a palavra-passe que, em seguida, pode utilizar com uma aplicação do Azure. Em seguida, mostra como uma aplicação, em seguida, pode utilizar essa chave ou a palavra-passe.

**Tempo estimado para concluir:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever a aplicação Azure que um dos passos inclui, que mostra como autorizar uma aplicação para utilizar uma chave ou segredo no Cofre de chaves.
> 
> Atualmente, não é possível configurar o Cofre de Chaves do Azure no Portal do Azure. Em vez disso, utilize estas instruções de Interface de linha de comandos de várias plataformas. Ou, para obter instruções do Azure PowerShell, consulte [tutorial equivalente](key-vault-get-started.md).
> 
> 

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

Também pode ler os tutoriais seguintes para se familiarizar com o Azure Resource Manager na Interface de linha de comandos de plataforma do Azure:

* [Como instalar e configurar a Interface de linha de comandos de plataforma do Azure](../cli-install-nodejs.md)
* [Utilizar a Interface de linha de comandos de plataforma do Azure com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições

Inicie sessão com uma conta institucional, utilize o seguinte comando:

    azure login -u username -p password

ou se pretende iniciar sessão, escrevendo interativamente

    azure login

> [!NOTE]
> O método de início de sessão só funciona com a conta organizacional. Uma conta institucional é um utilizador que é gerido pela sua organização e definido no inquilino do Azure Active Directory da sua organização.
> 
> 

Se não tem atualmente uma conta institucional e estiver a utilizar uma conta Microsoft para iniciar sessão sua subscrição do Azure, pode criar facilmente uma através dos seguintes passos.

1. Início de sessão para o início de sessão para o [Azure Management Portal](https://manage.windowsazure.com/)e clique em Active Directory.
2. Não se existir nenhum diretório, selecione a criar o diretório e forneça as informações pedidas.
3. Selecione o diretório e adicione um novo utilizador. Este novo utilizador é uma conta organizacional. Durante a criação do utilizador, irá ser fornecido com ambos os endereços de e-mail para o utilizador e uma palavra-passe temporária. Guarde estas informações como é utilizado em outro passo.
4. No portal, selecione as definições e, em seguida, selecione os administradores. Selecionar adicionar e adicione o novo utilizador como coadministrador. Isto permite que a conta institucional gerir a sua subscrição do Azure.
5. Por fim, termine sessão no portal do Azure e, em seguida, volte a iniciar sessão utilizando a nova conta organizacional. Se este for o primeiro registo de tempo com esta conta, será solicitado para alterar a palavra-passe.

Para obter mais informações sobre como utilizar uma conta institucional com o Microsoft Azure, consulte [inscrever-se no Microsoft Azure como uma organização](../active-directory/sign-up-organization.md).

Se tiver várias subscrições e pretender especificar uma subscrição particular para utilizar com o Cofre de Chaves do Azure, introduza o seguinte para ver as subscrições da sua conta:

    azure account list

Em seguida, para especificar a subscrição a utilizar, introduza:

    azure account set <subscription name>

Para obter mais informações sobre a configuração de Interface de linha de comandos de plataforma do Azure, consulte [como instalar e configurar a Interface de linha de comandos de várias plataformas de Azure](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Mudar para utilizar o Azure Resource Manager
Requer que o Cofre de chaves do Azure Resource Manager, por isso, escreva o seguinte para mudar para modo Azure Resource Manager:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos
Ao utilizar o Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Iremos criar um novo grupo de recursos 'ContosoResourceGroup' para este tutorial.

    azure group create 'ContosoResourceGroup' 'East Asia'

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é a localização. Para a localização, utilize o comando `azure location list` para identificar como especificar uma localização alternativa neste exemplo. Se precisar de mais informações, escreva:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registar o fornecedor de recursos do Cofre de chaves
Certifique-se de que fornecedor de recursos do Cofre de chaves está registado na sua subscrição:

`azure provider register Microsoft.KeyVault`

Isto só deverá ser efetuada uma vez por subscrição.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Utilize o `azure keyvault create` comando para criar um cofre de chaves. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome do Cofre de chaves e a localização geográfica.

Por exemplo, se utilizar o nome do Cofre de ContosoKeyVault, o nome do grupo de recursos de ContosoResourceGroup e a localização do leste asiático, escreva:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

O resultado deste comando mostra as propriedades do Cofre de chaves que acabou de criar. As duas propriedades mais importantes são:

* **Nome**: no exemplo é ContosoKeyVault. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **vaultUri**: no exemplo é https://contosokeyvault.vault.azure.net. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Por enquanto, não foi autorizado mais ninguém.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao cofre de chaves

Se pretender que o Cofre de chaves do Azure para criar uma chave protegida por software para si, utilize o `azure key create` de comandos e escreva o seguinte:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

No entanto, se tiver uma chave existente num ficheiro. pem guardado como ficheiro local num ficheiro denominado softkey.pem que pretende carregar para o Cofre de chaves do Azure, escreva o seguinte para importar a chave da. Ficheiro PEM, que protege a chave por software no serviço Cofre de chaves:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Agora, pode referenciar a chave criou ou carregou no Cofre de chaves do Azure, utilizando o seu respetivo URI. Utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** sempre obter a versão atual e utilizar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/ cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar um segredo no cofre, que é uma palavra-passe designada SQLPassword e que tem o valor de Pa$ $ w0rd no Cofre de chaves do Azure, escreva o seguinte:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Agora, pode referenciar esta palavra-passe que adicionou ao Cofre de Chaves do Azure utilizando o seu respetivo URI. Aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão mais recente e aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vejamos a chave ou segredo que acabou de criar:

* Para ver a sua chave, escreva: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Para ver o seu segredo, escreva: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registar uma aplicação com o Azure Active Directory

Geralmente, este passo deve ser realizado por um programador, num computador à parte. Não é específico ao Cofre de chaves do Azure, mas está incluído aqui por questões de exaustividade.

> [!IMPORTANT]
> Para concluir o tutorial, a sua conta, o cofre e a aplicação que irá registar neste passo devem todos estar no mesmo diretório do Azure.
> 
> 

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory. Para tal, o proprietário da aplicação deve primeiro registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:

* Um **ID da Aplicação** (também conhecido como um ID de Cliente) e a **chave de autenticação** (também conhecida como segredo partilhado). A aplicação deve apresentar ambos estes valores ao Azure Active Directory, para obter um token. A maneira como a aplicação é configurada para o fazer depende da aplicação. Para a aplicação de exemplo do Cofre de Chaves, o proprietário da aplicação define estes valores no ficheiro app.config.

Registar uma aplicação no Azure Active Directory:

1. Inicie sessão no Portal do Azure.
2. À esquerda, clique em **Active Directory** e, em seguida, selecione o diretório em que irá registar a sua aplicação. <br> <br> 

>[!NOTE] 
> Tem de selecionar o mesmo diretório que contém a subscrição do Azure com a qual criou o seu Cofre de chaves. Se não souber de que diretório se trata, clique em **Definições**, identifique a subscrição com a qual criou o seu cofre de chaves e tome nota do nome do diretório apresentado na última coluna.

3. Clique em **APLICAÇÕES**. Se não existem aplicações foram adicionadas ao seu diretório, esta página mostrará apenas a **adicionar uma aplicação** ligação. Clique na ligação ou, em alternativa, pode clicar no **adicionar** na barra de comandos.
4. No assistente **ADICIONAR APLICAÇÃO**, na página **O que pretende fazer?**, clique em **Adicionar uma aplicação que a minha organização está a desenvolver**.
5. No **conte-na sua aplicação** , especifique um nome para a sua aplicação e da página selecionar **aplicação WEB e/ou API WEB** (predefinição). Clique no ícone seguinte.
6. Na página **Propriedades da aplicação**, especifique o **URL DE INÍCIO DE SESSÃO** e **URI DO ID DA APLICAÇÃO** para a sua aplicação Web. Se a sua aplicação não tiver estes valores, pode aplicá-los para este passo (por exemplo, pode especificar http://test1.contoso.com para ambas as caixas). Não importa se estes sites existem; o que é importante é que o URI ID é diferente para cada aplicação do seu diretório. O diretório utiliza esta cadeia para identificar a sua aplicação.
7. Clique no ícone concluído para guardar as alterações no assistente.
8. Na página de início rápido, clique em **configurar**.
9. Desloque-se para a secção **chaves**, selecione a duração e, em seguida, clique em **GUARDAR**. A página é atualizada e mostra agora um valor de chave. Configure a sua aplicação com este valor de chave e valor **ID DE CLIENTE**. (As instruções para esta configuração serão específicas para a aplicação).
10. Copie o valor do ID de cliente desta página, que irá utilizar no passo seguinte para definir as permissões do seu cofre.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar a aplicação a utilizar a chave ou o segredo
Para autorizar a aplicação a aceder à chave ou segredo no cofre, utilize o `azure keyvault set-policy` comando.

Por exemplo, se o nome do seu Cofre ContosoKeyVault e a aplicação que pretende autorizar tem um ID de cliente de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e se pretende autorizar a aplicação a desencriptar e assinar com chaves no seu Cofre, em seguida, execute o seguinte:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'

> [!NOTE]
> Se estiver a executar numa linha de comandos do Windows, deve substituir plicas com aspas duplas e também como escape as aspas duplas internas. Por exemplo: "[\"desencriptar\",\"sessão\"]".
> 
> 

Se pretender autorizar essa mesma aplicação a ler os segredos no seu cofre, execute o seguinte procedimento:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se pretender utilizar um módulo de segurança de hardware (HSM)
Para mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda a [Eliminar o cofre de chaves e as chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar estas chaves protegidas de HSM, tem de ter uma subscrição do cofre que suporta chaves protegidas de HSM.

Quando cria o keyvault, adicione o parâmetro de "sku":

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro de destino para 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro. pem no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [como utilizar chaves de HSM-Protected com o Cofre de chaves do Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminar o cofre de chaves e as chaves e segredos associados
Se já não precisar do Cofre de chaves e a chave ou segredo que contiver, pode eliminar o Cofre de chaves utilizando o comando de eliminação de keyvault do azure:

    azure keyvault delete --vault-name 'ContosoKeyVault'

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos de Interface de linha de comandos de plataforma do Azure
Outros comandos que que poderá ser útil para gerir o Cofre de chaves do Azure.

Este comando lista uma tabela de apresentação de todas as chaves e propriedades selecionadas:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Este comando apresenta uma lista completa das propriedades para a chave especificada:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Este comando lista uma tabela de apresentação de todos os nomes secretos e propriedades selecionadas:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Eis um exemplo de como remover uma chave específica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Eis um exemplo de como remover um segredo específico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Passos seguintes
Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).

