---
title: Proteger o seu cofre de chaves | Microsoft Docs
description: "Administre as permissões de acesso ao cofre de chaves para gerir cofres, chaves e segredos. Modelo de autenticação e autorização para o cofre de chaves e como protegê-lo"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: b81791f0bce7e6f57782dfe7bc5fb5fc21369e7d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="secure-your-key-vault"></a>Proteger o seu cofre de chaves
O Cofre de Chaves do Azure é um serviço em nuvem que protege chaves e segredos de encriptação (como certificados, cadeias de ligação e palavras-passe) das suas aplicações na nuvem. Uma vez que estes dados são confidenciais e vitais para a sua atividade, é fundamental proteger o acesso aos seus cofres de chaves, de modo a que só os utilizadores e as aplicações com autorização possam aceder aos mesmos. Este artigo disponibiliza uma descrição geral do modelo de acesso do cofre de chaves, explica a autenticação e a autorização e descreve como proteger o acesso ao cofre de chaves das suas aplicações com um exemplo.

## <a name="overview"></a>Descrição geral
O controlo aos cofres de chaves é controlado através de duas interfaces separadas - o plano de gestão e o plano de dados. Ambos os planos requerem autenticação e autorização antes de um chamador (um utilizador ou uma aplicação) poder obter acesso ao cofre de chaves. A autenticação estabelece a identidade do chamador, ao passo que a autorização determina as operações que aquele pode fazer.

Tanto o plano de gestão, como o de dados, utilizam o Azure Active Directory na autenticação. Relativamente à autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza a política de acesso ao cofre de chaves.

Eis uma breve descrição geral dos tópicos abrangidos:

[Autenticação com o Azure Active Directory](#authentication-using-azure-active-directory) - esta secção explica como um chamador se autentica com o Azure Active Directory para aceder a um cofre de chaves através dos planos de gestão e de dados. 

[Plano de gestão e plano de dados](#management-plane-and-data-plane) - o plano de gestão e o plano de dados são dois planos de acesso utilizados para aceder aos seus cofres de chaves. Cada plano de acesso suporta operações específicas. Esta secção descreve os pontos finais de acesso, as operações suportadas e o método de controlo de acesso que cada plano utiliza. 

[Controlo de acesso do plano de gestão](#management-plane-access-control) - nesta secção, vamos ver como permitir acesso a operações do plano de gestão com o controlo de acesso baseado em funções.

[Controlo de acesso do plano de dados](#data-plane-access-control) - esta secção descreve como utilizar a política de acesso ao cofre de chaves para controlar o acesso ao plano de dados.

[Exemplo](#example) - este exemplo descreve como configurar o controlo de acesso para o seu cofre de chaves, de modo a permitir que três equipas diferentes (equipa de segurança, programadores/operadores e auditores) desempenhem tarefas específicas para desenvolver, gerir e monitorizar aplicações no Azure.

## <a name="authentication-using-azure-active-directory"></a>Autenticação com o Azure Active Directory
Quando cria um cofre de chaves numa subscrição do Azure, este é automaticamente associado ao inquilino do Azure Active Directory dessa subscrição. Todos os autores de chamadas (utilizadores e aplicações) têm de estar registados neste inquilino para aceder a este cofre de chaves. Para aceder ao cofre de chaves, o utilizador ou a aplicação tem de se autenticar com o Azure Active Directory. Isto aplica-se quer ao acesso do plano de gestão, quer do plano de dados. Em ambos os casos, as aplicações podem aceder a um cofre de chaves de duas formas:

* **acesso de utilizador+aplicação** - normalmente, destina-se a aplicações que acedem a cofres de chaves em nome de um utilizador com sessão iniciada. O Azure PowerShell e o Portal do Azure são dois exemplos deste tipo de acesso. Existem duas formas de conceder acesso aos utilizadores. Uma é conceder-lhes acesso para que possam aceder ao cofre de chaves a partir de qualquer aplicação e a outra é conceder-lhes acesso ao cofre apenas quando utilizam uma determinada aplicação (naquela que é a chamada identidade composta). 
* **acesso só de aplicação** - para as aplicações que executem serviços de daemon, tarefas em segundo plano, etc. É concedido acesso ao cofre de chaves à identidade da aplicação.

Em ambos os tipos de aplicações, a aplicação autentica-se com o Azure Active Directory através de um dos [métodos de autenticação suportados](../active-directory/active-directory-authentication-scenarios.md) e recebe um token. O método de autenticação utilizado depende do tipo de aplicação. Depois, a aplicação utiliza esse token e envia um pedido à API REST para o cofre de chaves. Em caso de acesso do plano de gestão, os pedidos são encaminhados através do ponto final do Azure Resource Manager. Ao aceder ao plano de gestão, a aplicação fala diretamente com um ponto final do cofre de chaves. Veja mais detalhes sobre o [fluxo de autenticação completo](../active-directory/active-directory-protocols-oauth-code.md). 

O nome do recurso relativamente ao qual a aplicação pede um token é diferente, consoante a aplicação esteja a aceder ao plano de gestão ou de dados. Por conseguinte, o nome do recurso é o ponto final do plano de gestão ou do plano de dados, descrito na tabela numa secção anterior, dependendo do ambiente do Azure.

Ter um mecanismo único para autenticação nos dois planos tem as suas vantagens:

* As organizações podem controlar centralmente o acesso a todos os cofres de chaves das mesmas.
* Se um utilizador sair da organização, perde imediatamente o acesso a todos os cofres da organização.
* As organizações podem personalizar a autenticação através das opções disponíveis no Azure Active Directory (por exemplo, podem ativar a autenticação multifator para aumentar a segurança).

## <a name="management-plane-and-data-plane"></a>Plano de gestão e plano de dados
O Cofre de Chaves do Azure é um serviço do Azure que está disponível através do modelo de implementação Azure Resource Manager. Quando cria um cofre de chaves, recebe um contentor virtual dentro do qual pode criar outros objetos, como chaves, segredos e certificados. Depois, pode utilizar o plano de gestão e o plano de dados para aceder ao cofre de chaves e fazer operações específicas. A interface de plano de gestão é utilizada para gerir o cofre de chaves propriamente dito, como, por exemplo, criar, eliminar e atualizar atributos do mesmo e definir políticas de acesso para o plano de dados. A interface de plano de dados é utilizada para adicionar, eliminar, modificar e utilizar as chaves, os segredos e os certificados armazenados no cofre.

O acesso às interfaces de plano de gestão e de plano de dados é feito por meio de pontos finais diferentes (ver tabela). A segunda coluna da tabela descreve os nomes DNS destes pontos finais em diferentes ambientes do Azure. A terceira coluna descreve as operações que pode fazer a partir de cada plano de acesso. Cada um dos planos de acesso tem também o seu próprio mecanismo de controlo de acesso. No plano de gestão, o controlo de acesso é definido com o Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager, ao passo que, no plano de dados, o controlo de acesso é definido através da política de acesso do cofre de chaves.

| Plano de acesso | Pontos finais de acesso | Operações | Mecanismo de controlo de acesso |
| --- | --- | --- | --- |
| Plano de gestão |**Global:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 |Criar/Ler/Atualizar/Eliminar cofre de chaves <br> Definir políticas de acesso ao cofre de chaves<br>Definir etiquetas para o cofre de chaves |Controlo de Acesso Baseado em Funções (RBAC) do Azure Resource Manager |
| Plano de dados |**Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |Em chaves: desencriptar, encriptar, unwrapKey, wrapKey, verificar, assinar, obter, listar, atualizar, criar, importar, eliminar, fazer cópia de segurança, restaurar<br><br> Em segredos: obter, listar, definir, eliminar |Política de acesso do cofre de chaves |

Os controlos de acesso do plano de gestão e do plano de dados funcionam de forma independente. Por exemplo, se quiser conceder a uma aplicação acesso para utilizar as chaves num cofre de chaves, basta conceder permissões de acesso do plano de dados através das políticas de acesso do cofre de chaves e a aplicação não vai precisar do acesso do plano de gestão. Por outro lado, se quiser que um utilizador possa ler propriedades e etiquetas do cofre, mas que não tenha acesso às chaves, aos segredos ou aos certificados, pode conceder-lhe acesso de “leitura” com o RBAC e não vai ser preciso o plano de dados.

## <a name="management-plane-access-control"></a>Controlo de acesso do plano de gestão
O plano de gestão consiste em operações que afetam o próprio cofre de chaves. Por exemplo, pode criar ou eliminar um cofre de chaves. Pode ver uma lista dos cofres numa subscrição. Pode obter as propriedades do cofre de chaves (como o SKU e as etiquetas) e definir políticas de acesso do cofre de chaves que controlem os utilizadores e aplicações que podem aceder a chaves e segredos do cofre de chaves. O controlo de acesso do plano de gestão utiliza o RBAC. Veja a lista completa das operações de cofres de chaves que podem ser feitas através do plano de gestão na tabela da secção anterior. 

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Cada subscrição do Azure tem um Azure Active Directory. Os utilizadores, grupos e aplicações deste diretório podem receber acesso para gerir recursos na subscrição do Azure que utilizem o modelo de implementação Azure Resource Manager. Este tipo de controlo de acesso tem o nome de Controlo de Acesso Baseado em Funções (Role-Based Access Control, RBAC). Para gerir este acesso, pode utilizar o [portal do Azure](https://portal.azure.com/), as [ferramentas da CLI do Azure](../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Com o modelo Azure Resource Manager, o cofre de chaves é criado num grupo de recursos e o acesso ao plano de gestão desse cofre é controlado com o Azure Active Directory. Por exemplo, pode conceder a utilizadores ou grupos a capacidade de gerir cofres de chave num grupo de recursos específico.

Pode conceder acesso a utilizadores, grupos e aplicações com um âmbito específico através da atribuição de funções RBAC adequadas. Por exemplo, para conceder acesso a um utilizador para gerir cofres de chaves, seria atribuída uma função predefinida “Contribuinte do cofre de chaves” a esse utilizador com um âmbito específico. Neste caso, o âmbito seria uma subscrição, um grupo de recursos ou apenas um cofre de chaves específico. As funções atribuídas ao nível da subscrição aplicam-se a todos os grupos de recursos e recursos nessa subscrição. As funções atribuídas ao nível do grupo de recursos aplicam-se a todos os recursos nesse grupo de recursos. As funções atribuídas a um recurso específico só se aplicam a esse recurso. Existem várias funções predefinidas (veja [RBAC: Built-in roles (RBAC: Funções Incorporadas)](../active-directory/role-based-access-built-in-roles.md)) e, se estas não satisfizerem as suas necessidades, também pode definir as suas próprias funções.

> [!IMPORTANT]
> Tenha em conta que, se um utilizador tiver as permissões de Contribuinte (RBAC) para um plano de gestão do cofre de chaves, esse utilizador pode conceder acesso a ele próprio ao plano de dados mediante a definição da política de acesso do cofre de chaves, que controla o acesso ao plano de dados. Por este motivo, é recomendado controlar rigorosamente quem tem acesso de “Contribuinte” aos seus cofres de chaves, para garantir que só pessoas autorizadas podem aceder e gerir os cofres de chaves, as chaves, os segredos e os certificados.
> 
> 

## <a name="data-plane-access-control"></a>Controlo de acesso do plano de dados
O plano de dados do cofre de chaves consiste em operações que afetam os objetos num cofre de chave, como chaves, segredos e certificados.  Incluem operações de chaves, como criar, importar, atualizar, listar, criar cópias de segurança e restaurar chaves, e operações criptográficas, como assinar, verificar, encriptar, desencriptar, encapsular, anular o encapsulamento e definir etiquetas e outros atributos de chaves. Do mesmo modo, relativamente a segredos, inclui obter, definir, listar e eliminar.

O acesso do plano de dados é concedido mediante a definição de políticas de acesso a cofres de chaves. De modo a poder definir políticas de acesso a um cofre de chaves, um utilizador, grupo ou aplicação tem de ter as permissões de Contribuinte (RBAC) para o plano de gestão desse cofre. Pode ser concedido acesso a um utilizador, grupo ou aplicação para fazer operações específicas de chaves ou segredos num cofre de chaves. Os cofres de chaves suportam até 16 entradas de políticas de acesso. Crie um grupo de segurança do Azure Active Directory e adicione utilizadores ao mesmo para conceder acesso do plano de dados a vários utilizadores a um cofre de chaves.

### <a name="key-vault-access-policies"></a>Políticas de Acesso dos cofres de chaves
As políticas de acesso dos cofres de chaves concedem permissões para chaves, segredos e certificados em separado. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Por outras palavras, a política de acesso do cofre de chaves não suporta permissões ao nível do objeto. Pode utilizar o [portal do Azure](https://portal.azure.com/), as [ferramentas da CLI do Azure](../cli-install-nodejs.md), o [PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST de Gestão dos cofres de chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx) para definir políticas de acesso para um cofre de chave.

> [!IMPORTANT]
> Tenha em atenção que as políticas de acesso do cofre de chaves são aplicadas ao nível do cofre. Por exemplo, quando é concedida permissão a um utilizador para criar e eliminar chaves, esse utilizador pode fazer essas operações em todas as chaves no cofre de chaves.
> 
> 

## <a name="example"></a>Exemplo
Imaginemos que está a desenvolver uma aplicação que utiliza um certificado para SSL, o Armazenamento do Azure para armazenar dados e uma chave RSA de 2048 bits para operações de assinatura. Imaginemos, agora, que a aplicação é executada numa VM (ou num Conjunto de Dimensionamento de VM). Pode utilizar um cofre de chaves para armazenar todos os segredos da aplicação e utilizar o cofre de chaves para armazenar o certificado do programa de arranque do sistema que a aplicação utiliza para autenticar com o Azure Active Directory.

Eis, então, um resumo de todas as chaves e segredos a serem armazenados num cofre de chaves.

* **Certificado SSL** - utilizado para SSL
* **Chave de Armazenamento** - utilizada para obter acesso à conta de Armazenamento
* **Chave RSA de 2048 bits** - utilizada para operações de assinatura
* **Certificado do programa de arranque do sistema** - utilizado para autenticar no Azure Active Directory, de modo a ter acesso ao cofre de chaves para obter a chave de armazenamento e utilizar a chave RSA para a assinatura.

Agora, vamos ver quem está a gerir, a implementar e a auditar esta aplicação. Vamos utilizar três funções neste exemplo.

* **Equipa de segurança** - regra gera, é composta por pessoal de TI, do ‘departamento do CSO (Chief Security Officer, Diretor de Segurança)’ ou equivalente, e é responsável por proteger adequadamente os segredos, como certificados SSL, chaves RSA utilizadas para assinaturas, cadeias de ligação para bases de dados e chaves da conta de armazenamento.
* **Programadores/operadores** - são as pessoas que desenvolvem a aplicação e a implementam no Azure. Normalmente, não fazem parte da equipa de segurança, pelo que não devem ter acesso a dados confidenciais, tais como certificados SSL, chaves RSA, mas a aplicação que implementam deve ter acesso aos mesmos.
* **Auditores** - regra geral, é um grupo de pessoas diferente, isolado dos programadores e da equipa de TI geral. A responsabilidade dos auditores é rever a correta utilização e manutenção dos certificados, das chaves, etc., e garantir a conformidade com as normas de segurança de dados. 

Existe uma outra função que não está incluída no âmbito desta aplicação, mas que é importante mencionar aqui, que é o administrador da subscrição (ou do grupo de recursos). O administrador da subscrição configura as permissões de acesso inicial para a equipa de segurança. Neste exemplo, partimos do princípio de que o administrador da subscrição concedeu acesso à equipa de segurança a um grupo de recursos no qual residem todos os recursos necessários para a aplicação.

Agora, vamos ver que ações são realizadas por cada função no contexto da aplicação.

* **Equipa de segurança**
  * Criar cofres de chaves
  * Ativar o registo de cofres de chaves
  * Adicionar chaves/segredos
  * Criar cópias de segurança de chaves para recuperação após desastres
  * Definir a política de acesso do cofre de chaves para conceder permissões a utilizadores e aplicações para fazerem operações específicas
  * Lançar periodicamente chaves/segredos
* **Programadores/operadores**
  * Obter referências para certificados de programa de arranque do sistema e SSL (thumbprints), chave de armazenamento (URI do segredo) e chave de assinatura (URI da chave) junto da equipa de segurança
  * Desenvolver e implementar aplicações que acedam a chaves e segredos programaticamente
* **Auditores**
  * Rever os registos de utilização para confirmar a utilização de chaves/segredos adequada e a conformidade com as normas de segurança de dados

Agora, vamos ver quais são as permissões de acesso ao cofre de chaves de que cada função (e aplicação) precisa para fazer as tarefas que lhe foram atribuídas. 

| Função de Utilizador | Permissões do plano de gestão | Permissões do plano de dados |
| --- | --- | --- |
| Equipa de Segurança |Contribuinte do cofre de chaves |Chaves: criar cópia de segurança, criar, eliminar, obter, importar, listar, restaurar <br> Segredos: todas |
| Programadores/operadores |Permissão de implementação de cofre de chaves, para que as VMs por eles implementadas possam obter segredos a partir do cofre de chaves |Nenhuma |
| Auditores |Nenhuma |Chaves: listar<br>Segredos: listar |
| Aplicação |Nenhuma |Chaves: assinar<br>Segredos: obter |

> [!NOTE]
> Os auditores precisam da permissão de listar para chaves e segredos, para que possam inspecionar atributos de chaves e segredos que não são emitidos nos registos, como etiquetas e datas de ativação e expiração.
> 
> 

Para além da permissão para o cofre de chaves, estas três funções também precisam de acesso a outros recursos. Por exemplo, para poderem implementar VMs (ou Aplicações Web, etc.) Os programadores/operadores também precisam do acesso de “Contribuinte” a esses tipos de recursos. Os auditores requerem acesso de leitura à conta de armazenamento na qual são armazenados os registos dos cofres de chaves.

Uma vez que o foco deste artigo está em proteger o acesso ao seu cofre de chaves, só vamos ilustrar as partes relevantes no âmbito deste tópico e ignorar os detalhes sobre a implementação de certificados, o acesso programático a chaves e segredos, etc. Esses detalhes já estão mencionados noutro artigo. A implementação de certificados armazenados no cofre de chaves em VMs está mencionada numa [mensagem do blogue](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) e está disponível [código de exemplo](https://www.microsoft.com/download/details.aspx?id=45343) que mostra como utilizar os certificados do programa de arranque do sistema para autenticar no Azure AD e obter acesso ao cofre de chaves.

A maioria das permissões de acesso pode ser concedida através do portal do Azure. Contudo, para conceder permissões mais detalhadas, poderá ter de utilizar o Azure PowerShell (ou a CLI do Azure) para alcançar o resultado desejado. 

Os fragmentos do PowerShell seguintes presumem que:

* O administrador do Azure Active Directory criou grupos de segurança que representam as três funções, nomeadamente, Equipa de Segurança da Contoso, Desenvolvimento de Aplicações da Contoso e Auditores de Aplicações da Contoso. O administrador também adicionou utilizadores aos grupos a que pertence.
* **ContosoAppRG** é o grupo de recursos onde residem todos os recursos. **contosologstorage** é onde são armazenados os registos. 
* O cofre de chaves **ContosoKeyVault** e a conta de armazenamento utilizada para os registos do cofre de chaves, **contosologstorage**, têm de estar na mesma localização do Azure

Primeiro, o administrador da subscrição atribui as funções “Contribuinte do cofre de chaves” e “Administrador de acesso de utilizadores” à equipa de segurança. Desta forma, a equipa pode gerir o acesso a outros recursos, bem como os cofres de chaves no grupo de recursos ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

O script que se segue ilustra como a equipa de segurança pode criar um cofre de chaves, configurar o registo e definir permissões de acesso para outras funções e para a aplicação. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

A função personalizada definida só é atribuível à subscrição na qual foi criado o grupo de recursos ContosoAppRG. Se as mesmas funções personalizadas forem utilizadas noutros projetos de outras subscrições, podem ser adicionadas mais subscrições aos respetivos âmbitos.

O âmbito da atribuição de funções personalizadas a programadores/operadores para a permissão “implementar/ação” é o grupo de recursos. Desta forma, apenas as VMs criadas no grupo de recursos “ContosoAppRG” obterão os segredos (certificado SSL e certificado do programa de arranque do sistema). Qualquer VM que um membro da equipa de programadores/operadores crie noutro grupo de recursos não conseguirá obter estes segredos, mesmo que conheça os respetivos URIs.

Este exemplo mostra um cenário simples. Os cenários da vida real podem ser mais complexos e poderá ter de ajustar as permissões para o cofre de chaves com base nas suas necessidades. No nosso exemplo, partimos do princípio de que a equipa de segurança vai disponibilizar as referências da chave e do segredo (URIs e thumbprints) que a equipa de programadores/operadores tem de referenciar nas respetivas aplicações. Assim, não tem de conceder aos programadores/operadores nenhum acesso do plano de dados. Note também que este exemplo se foca em proteger o seu cofre de chaves. Devem ser igualmente tidas considerações semelhantes ao proteger [as VMs](https://azure.microsoft.com/services/virtual-machines/security/), as [contas de armazenamento](../storage/common/storage-security-guide.md) e outros recursos do Azure.

> [!NOTE]
> Nota: este exemplo mostra como o acesso do cofre de chaves estará bloqueado na produção. Os programadores devem ter a sua própria subscrição ou grupo de recursos nos quais tenham permissões completas para gerir os cofres, as VMs e a conta de armazenamento em que desenvolveram a aplicação.
> 
> 

## <a name="resources"></a>Recursos
* [Controlo de Acesso Baseado em Funções do Azure Active Directory](../active-directory/role-based-access-control-configure.md)
  
  Este artigo explica o Controlo de Acesso Baseado em Funções do Azure Active Directory e como funciona.
* [RBAC: Funções Incorporadas](../active-directory/role-based-access-built-in-roles.md)
  
  Este artigo mostra todas as funções incorporadas disponíveis no RBAC.
* [Compreender a implementação do Resource Manager e a implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Este artigo explica os modelos de implementação Resource Manager e de implementação clássica e explica as vantagens de utilizar o Resource Manager e os grupos de recursos.
* [Gerir o Controlo de Acesso Baseado em Funções com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  
  Este artigo explica como gerir o controlo de acesso baseado em funções com o Azure PowerShell
* [Gerir o Controlo de Acesso Baseado em Funções com a API REST](../active-directory/role-based-access-control-manage-access-rest.md)
  
  Este artigo mostra como utilizar a API REST para gerir o RBAC.
* [Controlo de Acesso Baseado em Funções para o Microsoft Azure no Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Esta é uma ligação para um vídeo do Channel 9 da conferência Ignite 2015 MS. Nesta sessão, fala-se sobre as capacidades de gestão de acesso e de criação de relatórios no Azure e são exploradas as melhores práticas relativamente à proteção do acesso às subscrições do Azure com o Azure Active Directory.
* [Autorizar o acesso a aplicações Web com OAuth 2.0 e o Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)
  
  Este artigo descreve o fluxo completo de OAuth 2.0 para autenticar com o Azure Active Directory.
* [APIs REST de Gestão do cofre de chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Este documento é a referência para as APIs REST para gerir o seu cofre de chaves programaticamente, incluindo para definir a política de acesso do cofre de chaves.
* [APIs REST do cofre de chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Ligação para a documentação de referência da API REST do Cofre de Chaves
* [Controlo de acesso a chaves](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Ligação para a documentação de referência do Controlo de acesso a segredos.
* [Controlo de acesso a segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Ligação para a documentação de referência do Controlo de acesso a chaves
* [Definir](https://msdn.microsoft.com/library/mt603625.aspx) e [Remover](https://msdn.microsoft.com/library/mt619427.aspx) a política de acesso do cofre de chaves com o PowerShell
  
  Ligações para documentação de referência sobre cmdlets do PowerShell para gerir a política de acesso do cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial introdutório para administradores, veja [Introdução ao Cofre de Chaves do Azure](key-vault-get-started.md).

Para obter mais informações sobre o registo de utilização do cofre de chave, veja [Registo do Cofre de Chaves do Azure](key-vault-logging.md).

Para obter mais informações sobre a utilização de chaves e segredos com o cofre de chave do Azure, veja [About Keys and Secrets (Sobre Chaves e Segredos)](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se tiver perguntas sobre o Cofre de Chaves, visite os [Fóruns do Cofre de Chaves do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

