---
title: Proteger zonas DNS e os registos | Microsoft Docs
description: Como proteger zonas DNS e os conjuntos de registos no DNS do Microsoft Azure.
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: 0b7040d6273b3a6b85cd55850d596807226b87fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-protect-dns-zones-and-records"></a>Como pretende proteger os registos e zonas DNS

Zonas DNS e registos são recursos críticos. Eliminação de uma zona DNS ou mesmo apenas um único registo DNS pode resultar numa interrupção total do serviço.  Consequentemente, é importante que críticos zonas DNS e registos estão protegidos contra utilizadores não autorizadas ou acidentais de alterações.

Este artigo explica como o DNS do Azure lhe permite proteger os seus zonas DNS e registos em relação a essas alterações.  Iremos aplicar dois segurança poderosas das funcionalidades fornecidas pelo Gestor de recursos do Azure: [controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) e [bloqueios de recurso](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Azure baseada em funções controlo de acesso (RBAC) permite a gestão de acesso detalhado para utilizadores do Azure, grupos e recursos. Utilizar o RBAC, pode conceder precisamente a quantidade de acesso que os utilizadores precisam desempenhar as suas funções. Para obter mais informações sobre como o RBAC Ajuda a gerir o acesso, consulte [que é o controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>A função 'Contribuinte da zona de DNS'

A função 'Contribuinte da zona de DNS' é uma função incorporada fornecida pelo Azure para gerir recursos DNS.  Atribuir permissões de contribuinte de zona de DNS para um utilizador ou grupo permite desse grupo Gerir recursos DNS, mas não os recursos de qualquer outro tipo.

Por exemplo, suponha que o grupo de recursos 'myzones' contém cinco zonas para Contoso Corporation. Conceder permissões de 'Contribuinte da zona de DNS' nesse grupo de recursos de administrador de DNS, permite controlo total sobre as zonas DNS. Também evita a concessão de permissões desnecessárias, por exemplo, o administrador DNS não é possível criar ou parar máquinas virtuais.

É a forma mais simples para atribuir permissões de RBAC [através do portal do Azure](../active-directory/role-based-access-control-configure.md).  Abrir o painel 'Controlo de acesso (IAM)' para o grupo de recursos, em seguida, clique em "Adicionar", em seguida, selecione a função 'Contribuinte da zona de DNS' e selecione necessários utilizadores ou grupos para conceder permissões.

![Nível de grupo de recursos RBAC através do portal do Azure](./media/dns-protect-zones-recordsets/rbac1.png)

Também podem ser permissões [concedido com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

O comando equivalente também é [disponível através da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>Nível de zona RBAC

As regras RBAC do Azure podem ser aplicadas a uma subscrição, um grupo de recursos ou a um recurso individual. No caso de DNS do Azure, esse recurso pode ser uma zona DNS individuais, ou até mesmo um conjunto de registos individuais.

Por exemplo, suponha que o grupo de recursos 'myzones' contém a zona "contoso.com" e um subzone 'customers.contoso.com' em que os registos CNAME são criados para cada conta de cliente.  A conta utilizada para gerir estes registos CNAME deve receber permissões para criar registos na zona 'customers.contoso.com' apenas, não deve ter acesso para as outras zonas.

Permissões de RBAC de nível de zona podem ser concedidas através do portal do Azure.  Abrir o painel 'Controlo de acesso (IAM)' para a zona, em seguida, clique 'Add', em seguida, selecione a função 'Contribuinte da zona de DNS' e selecione necessários utilizadores ou grupos para conceder permissões.

![RBAC de nível de zona DNS através do portal do Azure](./media/dns-protect-zones-recordsets/rbac2.png)

Também podem ser permissões [concedido com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

O comando equivalente também é [disponível através da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>Ao nível do RBAC do conjunto de registos

Iremos pode aceder um passo adicional. Considere o administrador de correio para Contoso Corporation, o que precisa de acesso para os registos MX e TXT no vértice da zona "contoso.com".  A Joana não necessita de acesso a quaisquer outros registos MX ou TXT, ou para quaisquer registos de qualquer outro tipo.  O DNS do Azure permite-lhe atribuir permissões ao nível do conjunto de registos, para precisamente os registos que o administrador do correio tem acesso.  O administrador de correio é controlo precisamente a ela precisa e não pode efetuar outras alterações.

Permissões de RBAC ao nível do conjunto de registos podem ser configuradas através do portal do Azure, com o botão de "Utilizadores" no painel do conjunto de registos:

![Conjunto de registos ao nível do RBAC através do portal do Azure](./media/dns-protect-zones-recordsets/rbac3.png)

Também podem ser o conjunto de registos permissões ao nível da RBAC [concedido com o Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

O comando equivalente também é [disponível através da CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Funções personalizadas

A função 'Contribuinte da zona de DNS' incorporada permite controlo total sobre um recurso DNS. Também é possível construir o seu próprio cliente funções do Azure, para fornecer controlo de mesmo bloqueio.

Tenha em conta novamente o exemplo em que um registo CNAME na zona 'customers.contoso.com' é criado para cada conta de cliente Contoso Corporation.  A conta utilizada para gerir estes CNAMEs deverá ser concedida permissão para gerir registos CNAME apenas.  Em seguida, é não é possível modificar os registos de outros tipos (tais como a alteração registos MX) ou realizar operações de nível de zona, tais como eliminar de zona.

O exemplo seguinte mostra uma definição de função personalizada para gerir registos CNAME apenas:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

A propriedade de ações define as seguintes permissões de DNS específicos:

* `Microsoft.Network/dnsZones/CNAME/*`concede o controlo total sobre registos CNAME
* `Microsoft.Network/dnsZones/read`concede permissão de leitura de zonas DNS, mas não modificá-las, permitindo-lhe ver a zona no qual o CNAME está a ser criado.

As restantes ações são copiadas do [função incorporada de contribuinte de zona DNS](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> Utilizar uma função personalizada do RBAC para evitar a eliminação de conjuntos de registos enquanto ainda permitindo-lhes ser atualizada não é um controlo Efetivo. Impede que os conjuntos de registo que está a ser eliminada, mas não impedi-los de que está a ser modificado.  Permitidas modificações incluem adicionar e remover registos do conjunto de registos, incluindo a remover todos os registos para deixar um conjunto de registos 'empty'. Isto tem o mesmo efeito que a eliminar o conjunto de uma perspetiva de resolução DNS de registos.

Atualmente não não possível definir as definições de função personalizada através do portal do Azure. Uma função personalizada com base nesta definição de função pode ser criada com o Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

Também podem ser criada através da CLI do Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

Em seguida, pode ser atribuída a função da mesma forma como as funções incorporadas, como descrito anteriormente neste artigo.

Para obter mais informações sobre como criar, gerir e atribuir funções personalizadas, consulte [funções personalizadas no Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Bloqueios de recursos

Para além do RBAC, do Azure Resource Manager suporta a outro tipo de controlo de segurança, nomeadamente a capacidade de recursos 'lock'. Em que as regras de RBAC permitem controlar as ações de utilizadores e grupos específicos, recurso bloqueios são aplicadas ao recurso e são eficazes em todos os utilizadores e funções. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

Existem dois tipos de bloqueio de recurso: **DoNotDelete** e **ReadOnly**. Estes podem ser aplicadas a uma zona DNS ou para um conjunto de registos individuais.  As secções seguintes descrevem os vários cenários comuns e como suportá-los utilizando as bloqueios de recursos.

### <a name="protecting-against-all-changes"></a>Proteger contra todas as alterações

Para impedir qualquer alteração que está a ser efetuada, aplica um bloqueio de só de leitura para a zona.  Isto impede que novos conjuntos de registos que está a ser criados e existentes conjuntos de registos de que está a ser modificado ou eliminado.

Bloqueios de recurso de nível de zona podem ser criados através do portal do Azure.  No painel de zona DNS, clique em 'Bloqueios', 'Adicione':

![Bloqueios de recurso de nível de zona através do portal do Azure](./media/dns-protect-zones-recordsets/locks1.png)

Nível de zona de recursos bloqueios também podem ser criados através do PowerShell do Azure:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

A configuração de bloqueios de recursos do Azure não é atualmente suportada através da CLI do Azure.

### <a name="protecting-individual-records"></a>Proteger registos individuais

Para impedir que um registo DNS existente definido em relação a modificação, aplica um bloqueio de só de leitura para o conjunto de registos.

> [!NOTE]
> Aplicar um bloqueio DoNotDelete para um conjunto de registos não é um controlo Efetivo. Impede o registo de definir a ser eliminado, mas não impedi-lo de que está a ser modificado.  Permitidas modificações incluem adicionar e remover registos do conjunto de registos, incluindo a remover todos os registos para deixar um conjunto de registos 'empty'. Isto tem o mesmo efeito que a eliminar o conjunto de uma perspetiva de resolução DNS de registos.

Bloqueios de recurso de nível de conjunto de registos atualmente podem apenas ser configurado com o Azure PowerShell.  Não são suportados no portal do Azure ou a CLI do Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Proteger contra eliminação de zona

Quando uma zona é eliminada no DNS do Azure, também são eliminados todos os conjuntos de registos na zona.  Esta operação não pode ser anulada.  Eliminar acidentalmente uma zona crítica tem o potencial de ter um impacto significativo comercial.  Consequentemente, é muito importante proteger contra eliminação acidental de zona.

Aplicar um bloqueio DoNotDelete a uma zona impede que a zona que está a ser eliminado.  No entanto, uma vez que os bloqueios são herdados pelo recursos subordinados, impede também que os conjuntos de registos na zona que está a ser eliminado, que poderá ser indesejável.  Além disso, conforme descrito na nota acima, também é ineficaz, uma vez que os registos ainda podem ser removidos dos conjuntos de registos existentes.

Como alternativa, considere aplicar um bloqueio DoNotDelete para um conjunto de zona, como o conjunto de registos SOA de registos.  Uma vez que não é possível eliminar a zona sem eliminar também os conjuntos de registos, esta ação protege contra a eliminação de zona, permitindo ainda que os conjuntos de registos na zona de ser modificados gratuitamente. Se é efetuada uma tentativa de eliminar a zona, o Gestor de recursos do Azure Deteta Isto iria também eliminar o conjunto de registos SOA e bloqueia a chamada porque o SOA está bloqueado.  Não existem conjuntos de registos são eliminados.

O seguinte comando do PowerShell cria um bloqueio DoNotDelete contra o registo SOA da zona especificado:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Outra forma de impedir que a eliminação acidental de zona é através da utilização de uma função personalizada para garantir que o operador e utilizadas para gerir as suas zonas de contas de serviço não tem zona eliminar as permissões. Quando tem de eliminar uma zona, pode impor um eliminar de dois passos, primeiro conceder zona delete (no âmbito de zona, para impedir a eliminar a zona errada) e permissões segundo para eliminar a zona.

Esta abordagem segundo tem a vantagem que funciona para todas as zonas acedidas por essas contas, sem ter de memorizar criar as bloqueios. Tem a desvantagem que as contas com permissões de eliminação de zona, tais como o proprietário da subscrição, acidentalmente ainda podem eliminar uma zona crítica.

É possível utilizar ambas as abordagens - bloqueios de recursos e funções personalizadas, ao mesmo tempo, como uma abordagem de defesa em profundidade para proteção de zona DNS.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como trabalhar com RBAC, consulte [introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).
* Para obter mais informações sobre como trabalhar com as bloqueios de recursos, consulte [bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

