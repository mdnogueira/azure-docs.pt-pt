---
ms.assetid: 
title: "O Cofre de chaves do Azure - como utilizar a eliminação de forma recuperável com a CLI"
description: "Utilizar maiúsculas e exemplos de eliminação de forma recuperável com o CLI recortes de código"
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.openlocfilehash: 3ee2c5dfb99d734cde25894174466b8e49823c67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Como utilizar a eliminação de forma recuperável do Cofre de chaves com a CLI

Funcionalidade de eliminação de forma recuperável do Azure do Cofre de chaves permite a recuperação de cofres eliminados e objetos do cofre. Especificamente, configuração soft-eliminar endereços os seguintes cenários:

- Suporte para eliminação recuperável de um cofre de chaves
- Suporte para a eliminação de objetos do Cofre de chaves; recuperável chaves de segredos e, de certificados

## <a name="prerequisites"></a>Pré-requisitos

- CLI do Azure 2.0 - se não tiver esta configuração para o seu ambiente, consulte [gerir o Cofre de chaves a utilizar o CLI 2.0](key-vault-manage-with-cli2.md).

Para informações de referência específica do Cofre de chaves para a CLI, consulte [referência do Cofre de chaves do Azure CLI 2.0](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permissões necessárias

Operações do Cofre de chaves separadamente geridas através de permissões de controlo (RBAC) de acesso baseado em funções da seguinte forma:

| Operação | Descrição | Permissão de utilizador |
|:--|:--|:--|
|Lista|Apresenta uma lista de eliminados cofres de chaves.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura um cofre de chaves foi eliminado.|Microsoft.KeyVault/vaults/write|
|Remover|Remove permanentemente um cofre de chaves eliminado e todo o respetivo conteúdo.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Para obter mais informações sobre o controlo de acesso e permissões, consulte [proteger o seu Cofre de chaves](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Ativar a eliminação de forma recuperável

Para conseguir recuperar um cofre de chaves eliminado ou objetos armazenados no Cofre de chaves, tem de ativar primeiro a eliminação de forma recuperável para esse cofre de chaves.

### <a name="existing-key-vault"></a>Cofre de chaves

Para um cofre de chaves com o nome ContosoVault, ative a eliminação de forma recuperável da seguinte forma. 

>[!NOTE]
>Atualmente tem de utilizar a manipulação de recursos do Azure Resource Manager diretamente a escrever o *enableSoftDelete* propriedade para o recurso do Cofre de chaves.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Novo cofre de chaves

Ativar a eliminação de forma recuperável para um novo cofre de chaves é efetuada no momento da criação, adicionando o sinalizador de ativação de eliminação de forma recuperável para o comando de criar.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Certifique-se a ativação da eliminação de forma recuperável

Para verificar se um cofre de chaves tem ativada de eliminação de forma recuperável, execute o *mostrar* de comandos e procure o "recuperável eliminar Enabled?" atributo e a respetiva definição, true ou false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Eliminar um cofre de chaves protegido por eliminar de forma recuperável

O comando para eliminar (ou remover) um cofre de chaves permanece o mesmo, mas o respetivo comportamento muda dependendo se ativou a eliminação de forma recuperável ou não.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Se executar o comando para um cofre de chaves que não tenha a eliminação de forma recuperável ativada anterior, irá eliminar permanentemente este Cofre de chaves e todo o respetivo conteúdo sem quaisquer opções de recuperação.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Como eliminar de forma recuperável protege os seus cofres de chaves

Com a eliminação de forma recuperável ativada:

- Quando é eliminado um cofre de chaves, é removido do respetivo grupo de recursos e colocada num espaço de nomes reservado que só está associado a localização onde foi criado. 
- Objetos de uma chave eliminado cofre, tal como chaves, os segredos e certificados, não estão acessíveis e permanecem, enquanto o seu Cofre de chaves que contêm está no Estado eliminado. 
- O nome DNS para um cofre de chaves num Estado eliminado está ainda reservado, portanto, não é possível criar um novo cofre de chaves com o mesmo nome.  

Pode ver os cofres de chaves de estado eliminado, associados à subscrição, utilizando o seguinte comando:

```azurecli
az keyvault list-deleted
```

O *ID de recurso* na saída refere-se no ID de recurso original de neste cofre. Uma vez que este Cofre de chaves agora num Estado eliminado, nenhum recurso existe com esse ID de recurso. O *Id* campo pode ser utilizado para identificar o recurso quando recuperar ou remoção. O *data de remoção agendada* campo indica quando o Cofre será permanentemente eliminado (removidos) se foi efetuada nenhuma ação para este cofre foi eliminado. O período de retenção predefinido, utilizado para calcular o *data de remoção agendada*, é de 90 dias.

## <a name="recovering-a-key-vault"></a>Recuperar um cofre de chaves

Para recuperar um cofre de chaves, tem de especificar o nome do Cofre de chaves, o grupo de recursos e a localização. Tenha em atenção a localização e o grupo de recursos do Cofre de chaves eliminado conforme necessário estes para um processo de recuperação do Cofre de chaves.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Quando um cofre de chaves é recuperado, o resultado é um novo recurso com original ID de recurso. o Cofre de chaves Se tiver sido removido do grupo de recursos em que o Cofre de chaves existente, um novo grupo de recursos com o mesmo nome tem de ser criado para que o Cofre de chaves que pode ser recuperado.

## <a name="key-vault-objects-and-soft-delete"></a>Objetos do Cofre de chaves e eliminação de forma recuperável

Para uma chave, 'ContosoFirstKey', num cofre de chaves com o nome 'ContosoVault' com eliminação de forma recuperável ativada, aqui forma como deveria eliminá essa chave.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Com o seu Cofre de chaves ativado para eliminação de forma recuperável, uma chave eliminada continua a aparecer como é eliminado, exceto, ao listar explicitamente ou obter chaves eliminadas. A maioria das operações numa chave no Estado eliminado irão falhar, exceto para listar uma chave eliminada, recuperação ou remoção-lo. 

Por exemplo, para pedir a chaves de lista de eliminados no Cofre de chaves, utilize o seguinte comando:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transição 

Quando elimina uma chave de um cofre de chaves com eliminação de forma recuperável ativada, poderá demorar alguns segundos a transição para concluir. Durante este estado de transição, podem parecer que a chave não está no estado de Active Directory ou estado eliminado. Este comando irá listar todas as chaves eliminadas no seu Cofre de chaves com o nome 'ContosoVault'.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilizar a eliminação de forma recuperável com objetos de Cofre de chaves

Apenas como cofres de chaves, uma chave eliminada, segredo ou certificado permanecerá no Estado eliminado até 90 dias, exceto se recuperar ou removê-lo. 

#### <a name="keys"></a>Chaves

Para recuperar uma chave de eliminados:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para eliminar permanentemente uma chave:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Remover uma chave irá eliminá-la permanentemente, que significa que não serão recuperável.

O **recuperar** e **remover** ações permissões os seus próprios associado a uma política de acesso do Cofre de chaves. Para um principal de serviço ou utilizador conseguir executar um **recuperar** ou **remover** ação têm de ter a respetiva permissão para esse objeto (chave ou segredo) na política de acesso de Cofre de chaves. Por predefinição, o **remover** permissão não é adicionada a política de acesso de um cofre de chaves quando o atalho 'tudo' é utilizado para conceder todas as permissões a um utilizador. Tem de conceder explicitamente **remover** permissão. Por exemplo, o seguinte comando concede user@contoso.com permissão para efetuar várias operações de chaves no *ContosoVault* incluindo **remover**.

#### <a name="set-a-key-vault-access-policy"></a>Definir uma política de acesso do Cofre de chaves

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Se tiver um cofre de chaves que apenas tenha tido a eliminação de forma recuperável ativada, poderá não ter **recuperar** e **remover** permissões.

#### <a name="secrets"></a>Segredos

Como as chaves, os segredos no Cofre de chaves são manipulados com os seus próprios comandos. A seguir, são os comandos para a eliminar, listar, recuperar e remover segredos.

- Elimine um segredo designado SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Lista de eliminados todos os segredos no Cofre de chaves: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Recupere um segredo no Estado eliminado: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Remover um segredo no estado de eliminação: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Remover um segredo irá eliminá-la permanentemente, que significa que não serão recuperável.

## <a name="purging-and-key-vaults"></a>Cofres de chaves e remoção

### <a name="key-vault-objects"></a>Objetos do Cofre de chaves

A remoção de uma chave de segredo ou certificado irá eliminá-la permanentemente, que significa que não serão recuperável. O Cofre de chaves que continha o objeto eliminado contudo permanecerão intacto como serão todos os outros objetos no Cofre de chaves. 

### <a name="key-vaults-as-containers"></a>Chave de cofres dos como contentores
Quando um cofre de chaves é removido, todo o conteúdo, incluindo as chaves, os segredos e certificados, são eliminados permanentemente. Para remover um cofre de chaves, utilize o `az keyvault purge` comando. Pode encontrar a localização eliminado cofres de chaves da sua subscrição utilizando o comando `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Remover um cofre de chaves será permanentemente eliminada, que significa que não serão recuperável.

### <a name="purge-permissions-required"></a>Remover as permissões necessárias
- Para remover um cofre de chaves eliminado de forma a que o Cofre e todos os respetivos conteúdos são removidos permanentemente, o utilizador tem permissão RBAC para efetuar um *Microsoft.KeyVault/locations/deletedVaults/purge/action* operação. 
- Para listar a chave foi eliminada, o Cofre de um utilizador tem permissão RBAC para efetuar *Microsoft.KeyVault/deletedVaults/read* permissão. 
- Por predefinição, apenas um administrador de subscrição tem as permissões. 

### <a name="scheduled-purge"></a>Remoção agendada

Listar os objetos eliminados Cofre de chaves mostra quando estão schedled a ser removido pelo Cofre de chaves. O *data de remoção agendada* campo indica quando um objeto do Cofre de chaves serão permanentemente eliminado, se foi efetuada nenhuma ação. Por predefinição, o período de retenção para um objeto eliminado Cofre de chaves é de 90 dias.

>[!NOTE]
>Um objeto de cofre removidos, acionado pela respetiva *data de remoção agendada* campo, é eliminado permanentemente. Não é recuperável.

## <a name="other-resources"></a>Outros recursos

- Para obter uma descrição geral da funcionalidade de eliminação de forma recuperável do Cofre de chaves, consulte [descrição geral da eliminação de forma recuperável do Cofre de chaves do Azure](key-vault-ovw-soft-delete.md).
- Para obter uma descrição geral da utilização do Cofre de chaves do Azure, consulte [introdução ao Cofre de chaves do Azure](key-vault-get-started.md).

