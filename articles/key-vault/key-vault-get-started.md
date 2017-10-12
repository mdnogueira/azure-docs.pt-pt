---
title: "Introdução ao Cofre de Chaves do Azure | Microsoft Docs"
description: "Utilize este tutorial para ajudá-lo com o Cofre de Chaves do Azure para criar um contentor protegido no Azure para armazenar e gerir chaves criptográficas e segredos no Azure."
services: key-vault
documentationcenter: 
author: cabailey
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: cabailey
ms.openlocfilehash: 0299d931c5bf21775b68069afaa106279270226a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-key-vault"></a>Introdução Cofre de Chaves do Azure
O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
Utilize este tutorial para ajudá-lo com o Cofre de Chaves do Azure para criar um contentor protegido (cofre) no Azure para armazenar e gerir chaves criptográficas e segredos no Azure. Este tutorial explica-lhe como utilizar o Azure PowerShell para criar um cofre que contém uma chave ou uma palavra-passe que poderá utilizar posteriormente numa aplicação do Azure. Em seguida, mostra-lhe como uma aplicação pode utilizar essa chave ou palavra-passe.

**Tempo estimado para concluir:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever a aplicação do Azure incluída num dos passos, nomeadamente como autorizar uma aplicação a utilizar uma chave ou um segredo do cofre de chaves.
>
> Este tutorial utiliza o Azure PowerShell. Para obter instruções sobre a Interface de Linha de Comandos de Várias Plataformas, veja [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
>

Para obter informações gerais sobre o Cofre de Chaves do Azure, consulte o artigo [O que é o Cofre de Chaves do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, deve ter o seguinte:

* Uma subscrição ao Microsoft Azure. Se não tiver uma conta, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell, **versão mínima 1.1.0**. Para instalar o Azure PowerShell e associá-lo à sua subscrição do Azure, consulte o artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se já tiver instalado o Azure PowerShell e não souber a versão, a partir da consola do Azure PowerShell, introduza `(Get-Module azure -ListAvailable).Version`. Quando tiver instalado as versões 0.9.1 a 0.9.8 do Azure PowerShell, pode continuar a utilizar neste tutorial com algumas pequenas alterações. Por exemplo, tem de utilizar o comando `Switch-AzureMode AzureResourceManager` e alguns dos comandos do Cofre de Chaves do Azure foram alterados. Para obter uma lista dos cmdlets do Cofre de Chaves para versões 0.9.1 a 0.9.8, consulte o artigo [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).
* Uma aplicação que será configurada para utilizar a chave ou a palavra-passe que criar neste tutorial. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obter instruções, consulte o ficheiro Readme.

Este tutorial foi concebido para principiantes do Azure PowerShell, embora parta do princípio de que compreende os conceitos básicos, tais como módulos, cmdlets e sessões. Para obter mais informações, consulte [ Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada para qualquer cmdlet que visualiza neste tutorial, utilize o cmdlet **Get-Help**.

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet **Login-AzureRmAccount**, introduza:

    Get-Help Login-AzureRmAccount -Detailed

Pode ainda ler os seguintes tutoriais para familiarizar-se com o Azure Resource Manager no Azure PowerShell:

* [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)
* [Utilizar o Azure PowerShell com o Resource Manager](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Ligar às suas subscrições
Abra uma sessão no Azure PowerShell e inicie sessão na sua conta do Azure com o seguinte comando:  

    Login-AzureRmAccount

Tenha em atenção que se estiver a utilizar uma instância específica do Azure, por exemplo, o Azure Government, utilize o parâmetro Ambiente com este comando. Por exemplo: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. O Azure PowerShell obtém todas as subscrições associadas a esta conta e, por defeito, utiliza a primeira.

Se tiver várias subscrições e pretender especificar uma subscrição particular para utilizar com o Cofre de Chaves do Azure, introduza o seguinte para ver as subscrições da sua conta:

    Get-AzureRmSubscription

Em seguida, para especificar a subscrição a utilizar, introduza:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obter mais informações sobre como configurar o Azure PowerShell, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a id="resource"></a>Criar um novo grupo de recursos
Quando utilizar o Azure Resource Manager, todos os recursos relacionados são criados no interior de um grupo de recursos. Para este tutorial, iremos criar um novo grupo de recursos designado **ContosoResourceGroup** :

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Criar um cofre de chaves
Utilize o cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) para criar um cofre de chaves. Este cmdlet tem três parâmetros obrigatórios: um **nome do grupo de recursos**, um **nome do cofre de chaves** e a **localização geográfica**.

Por exemplo, se utilizar o nome do cofre do **ContosoKeyVault**, o nome do grupo de recursos do **ContosoResourceGroup** e a localização **Ásia Oriental**, introduza:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

O resultado deste cmdlet mostra as propriedades do cofre de chaves que acabou de criar. As duas propriedades mais importantes são:

* **Nome do Cofre**: No exemplo, o nome é **ContosoKeyVault**. Irá utilizar este nome para outros cmdlets do Cofre de Chaves.
* **URI do Cofre**: No exemplo, trata-se de https://contosokeyvault.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Por enquanto, não foi autorizado mais ninguém.

> [!NOTE]
> Se for apresentado o erro **A subscrição não está registada para utilizar o espaço de nomes 'Microsoft.KeyVault'** quando tentar criar o seu novo cofre de chaves, execute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` e, em seguida, execute novamente o comando AzureRmKeyVault. Para obter mais informações, veja [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Adicionar uma chave ou segredo ao cofre de chaves
Se pretender que o Cofre de Chaves do Azure crie uma chave protegida por software para si, utilize o cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) e escreva o seguinte:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

No entanto, se já tiver uma chave protegida por software num ficheiro .PFX guardado na sua unidade C:\ com o nome softkey.pfx que pretende carregar para o Cofre de Chaves do Azure, escreva o seguinte para definir a variável **securepfxpwd** para uma palavra-passe de **123** para o ficheiro .PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Em seguida, escreva o seguinte para importar a chave do ficheiro .PFX, que protege a chave por software no serviço do Cofre de Chaves:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Agora, pode referenciar esta chave que criou ou carregou no Cofre de Chaves do Azure utilizando o seu respetivo URI. Aceda a **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão mais recente e aceda a **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.  

Para apresentar o URI para esta chave, escreva:

    $Key.key.kid

Para adicionar um segredo ao cofre que é uma palavra-passe designada SQLPassword e tem o valor Pa$$w0rd no Cofre de Chaves do Azure, comece por converter o valor Pa$$w0rd numa cadeia segura ao escrever o seguinte:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Em seguida, escreva o seguinte:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Agora, pode referenciar esta palavra-passe que adicionou ao Cofre de Chaves do Azure utilizando o seu respetivo URI. Aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão mais recente e aceda a **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Para apresentar o URI para este segredo, escreva:

    $secret.Id

Vejamos a chave ou o segredo que acabou de criar:

* Para ver a sua chave, escreva: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
* Para ver o seu segredo, escreva: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Agora, o seu cofre de chaves e chave ou segredo estão prontos a ser utilizados em aplicações. Deve autorizar as aplicações a utilizá-los.  

## <a id="register"></a>Registar uma aplicação com o Azure Active Directory
Geralmente, este passo deve ser realizado por um programador, num computador à parte. Não se trata de um passo específico ao Cofre de Chaves do Azure, embora esteja incluído aqui por questões de exaustividade.

> [!IMPORTANT]
> Para concluir o tutorial, a sua conta, o cofre e a aplicação que irá registar neste passo devem todos estar no mesmo diretório do Azure.
>
>

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory. Para tal, o proprietário da aplicação deve primeiro registar a aplicação no seu Azure Active Directory. No final do registo, o proprietário da aplicação obtém os seguintes valores:

* Um **ID da Aplicação** (também conhecido como um ID de Cliente) e a **chave de autenticação** (também conhecida como segredo partilhado). Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. A maneira como a aplicação é configurada para o fazer depende da aplicação. Para a aplicação de exemplo do Cofre de Chaves, o proprietário da aplicação define estes valores no ficheiro app.config.

Registar uma aplicação no Azure Active Directory:

1. Inicie sessão no Portal Clássico do Azure.
2. À esquerda, clique em **Active Directory** e, em seguida, selecione o diretório em que irá registar a sua aplicação. <br> <br> **Nota:** Deve selecionar o mesmo diretório que contém a subscrição ao Azure com a qual criou o seu cofre de chaves. Se não souber de que diretório se trata, clique em **Definições**, identifique a subscrição com a qual criou o seu cofre de chaves e tome nota do nome do diretório apresentado na última coluna.
3. Clique em **APLICAÇÕES**. Se nenhuma aplicação tiver sido adicionada ao seu diretório, esta página apenas irá mostrar a ligação **Adicionar uma Aplicação**. Clique na ligação ou, em alternativa, pode clicar em **ADICIONAR** na barra de comandos.
4. No assistente **ADICIONAR APLICAÇÃO**, na página **O que pretende fazer?**, clique em **Adicionar uma aplicação que a minha organização está a desenvolver**.
5. Na página **Forneça mais informações sobre a sua aplicação**, especifique um nome para a sua aplicação e, em seguida, selecione **APLICAÇÃO WEB E/OU API WEB** (predefinição). Clique no ícone **Seguinte**.
6. Na página **Propriedades da aplicação**, especifique o **URL DE INÍCIO DE SESSÃO** e **URI DO ID DA APLICAÇÃO** para a sua aplicação Web. Se a sua aplicação não tiver estes valores, pode aplicá-los para este passo (por exemplo, pode especificar http://test1.contoso.com para ambas as caixas). Não interessa se estes sites existem. O que é importante é que o URI do ID seja diferente para cada aplicação do seu diretório. O diretório utiliza esta cadeia para identificar a sua aplicação.
7. Clique no ícone **Concluído** para guardar as suas alterações no assistente.
8. Na página **Início Rápido**, clique em **CONFIGURAR**.
9. Desloque-se para a secção **chaves**, selecione a duração e, em seguida, clique em **GUARDAR**. A página é atualizada e mostra agora um valor de chave. Configure a sua aplicação com este valor de chave e valor **ID DE CLIENTE**. (As instruções para esta configuração são específicas para a aplicação).
10. Copie o valor do ID de cliente desta página, que irá utilizar no passo seguinte para definir as permissões do seu cofre.

## <a id="authorize"></a>Autorizar a aplicação a utilizar a chave ou o segredo
Para autorizar a aplicação a aceder à chave ou ao segredo no cofre, utilize o cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Por exemplo, se o nome do seu cofre for **ContosoKeyVault** e a aplicação que pretender autorizar tiver como ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e se pretender autorizar a aplicação a desencriptar e assinar com chaves no seu cofre, execute o seguinte procedimento:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se pretender autorizar essa mesma aplicação a ler os segredos no seu cofre, execute o seguinte procedimento:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Se quiser utilizar um módulo de hardware de segurança (HSM)
Para mais segurança, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda a [Eliminar o cofre de chaves e as chaves e segredos associados](#delete).

Para criar estas chaves protegidas por HSM, tem de utilizar a [camada de serviços do Cofre de Chaves do Azure para suportar chaves protegidas por HSM](https://azure.microsoft.com/pricing/free-trial/). Além disso, tenha em atenção que esta funcionalidade não está disponível para o Azure China.

Quando cria o cofre de chaves, adicione o parâmetro **-SKU**:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Neste cofre de chaves, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro **-Destino** para 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro .PFX no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Este cenário permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte o artigo [Como gerar e transferir as chaves protegidas por HSM para o Cofre de Chaves do Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminar o cofre de chaves e as chaves e segredos associados
Se já não precisar do Cofre de Chaves e da chave e do segredo que contiver, pode eliminar o Cofre de Chaves utilizando cmdlet [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Outros Cmdlets do Azure PowerShell
Outros comandos que poderão ser úteis para a gestão do Cofre de Chaves do Azure:

* `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Este comando obtém uma tabela de apresentação de todas as chaves e propriedades selecionadas.
* `$Keys[0]`: Este comando apresenta uma lista completa das propriedades da chave especificada
* `Get-AzureKeyVaultSecret`: Este comando lista uma tabela de apresentação de todos os nomes secretos e propriedades selecionadas.
* `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exemplo de como remover uma chave específica.
* `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exemplo de como remover um segredo específico.

## <a id="next"></a>Passos seguintes
Para um tutorial de seguimento que utiliza o Cofre de Chaves do Azure numa aplicação Web, consulte o artigo [Utilizar o Cofre de Chaves do Azure a partir de uma Aplicação Web](key-vault-use-from-web-application.md).

Para ver como o seu cofre de chaves está a ser utilizado, consulte o artigo [Registo do Cofre de Chaves do Azure](key-vault-logging.md).

Para obter uma lista dos cmdlets mais recentes do Azure PowerShell para o Cofre de Chaves do Azure, consulte o artigo [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).

Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](key-vault-developers-guide.md).
