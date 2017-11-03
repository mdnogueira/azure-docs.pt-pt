---
title: "Notas de versão do Kit de desenvolvimento de pilha do Microsoft Azure | Microsoft Docs"
description: "Melhoramentos, correções e problemas conhecidos do Kit de desenvolvimento de pilha do Azure."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: twooley
ms.openlocfilehash: 81ccb4a731b71f87bccb2f2a0e333443428f32ee
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Notas de versão do Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Estas notas de versão fornecem informações sobre os melhoramentos, correções e problemas conhecidos no Kit de desenvolvimento de pilha do Azure. Se não tiver a certeza de qual é a versão que está a executar, pode [utilizar o portal para verificar](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201710201"></a>Compilação 20171020.1

### <a name="improvements-and-fixes"></a>Melhoramentos e correções

Para ver a lista de melhoramentos e correções na compilação 20171020.1, consulte o [melhoramentos e correções](azure-stack-update-1710.md#improvements-and-fixes) secção as notas de versão de 1710 para pilha do Azure integrado sistemas. Alguns dos itens listados na secção "melhoramentos adicionais qualidade e correções" são relevantes apenas para os sistemas integrados.

Além disso, foram efetuadas as correções seguintes:
- Foi corrigido um problema onde o fornecedor de recursos de computação apresentar um Estado desconhecido.
- Foi corrigido um problema onde quotas podem não aparecer no portal do administrador depois de criá-los e, em seguida, tente mais tarde ver os detalhes de plano.

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="powershell"></a>PowerShell
- A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações de última hora. Para obter informações sobre a atualização a partir de 1.2.10 versão, consulte o [guia de migração](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Implementação
- Tem de especificar um servidor de tempo por endereço IP durante a implementação.

#### <a name="infrastructure-management"></a>Gestão de infraestrutura
- Não ative a cópia de segurança de infraestrutura no **cópia de segurança da infraestrutura** painel.
- O endereço IP do bmc gestão controlador BMC e o modelo não são apresentadas as informações essenciais de um nó de unidade de escala. Este comportamento é esperado no Kit de desenvolvimento de pilha do Azure.

#### <a name="portal"></a>Portal
- Poderá ver um dashboard em branco no portal. Para recuperar o dashboard, selecione o ícone de equipamento no canto superior direito do portal e, em seguida, selecione **restaurar predefinições**.
- Ao visualizar as propriedades de um grupo de recursos, o **mover** botão está desativado. Este comportamento é esperado. Mover grupos de recursos entre subscrições não é atualmente suportado.
-  Para qualquer fluxo de trabalho onde selecionou uma subscrição, o grupo de recursos ou a localização numa lista pendente, pode deparar-se um ou mais dos seguintes problemas:

   - Poderá ver uma linha em branco no topo da lista. Deve ainda poderá selecionar um item conforme esperado.
   - Se a lista de itens na lista pendente é pequeno, poderá não conseguir ver algum dos nomes de item.
   - Se tiver várias subscrições do utilizador, a lista de lista pendente do grupo de recursos pode estar vazia. 

   Para contornar os dois últimos problemas, pode escrever o nome da subscrição ou grupo de recursos (se sabe que este) ou pode utilizar em vez disso, o PowerShell.

- Verá uma **ativação necessária** alerta de aviso que indica a registar o Kit de desenvolvimento de pilha do Azure. Este comportamento é esperado.
- No **ativação necessária** detalhes do alerta de aviso, não clique na ligação para o **AzureBridge** componente. Se o fizer, o **descrição geral** painel irá tentar carregar, sem êxito e não o tempo limite.
- No portal do administrador, poderá ver um **erro ao obter os inquilinos** erro no **notificações** área. Pode ignorar este erro.
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos completo e, em seguida, eliminar subscrições de utilizador.
- Não é possível ver permissões à sua subscrição através da utilização de portais de pilha do Azure. Como solução, pode verificar as permissões com o PowerShell.
 
#### <a name="marketplace"></a>Marketplace
- Quando tentar adicionar itens para o mercado de pilha do Azure utilizando o **adicionar a partir do Azure** opção, nem todos os itens podem ser visíveis para transferência.
- Os utilizadores podem procurar o mercado completo sem uma subscrição e podem ver itens administrativos como planos e ofertas. Estes itens estão não funcional para os utilizadores.
 
#### <a name="compute"></a>Computação
- Os utilizadores recebem a opção para criar uma máquina virtual com armazenamento georredundante. Esta configuração provoca a falha da criação máquina virtual. 
- Pode configurar uma conjunto apenas com um domínio de falhas de um e um domínio de atualização de um de disponibilidade de máquina virtual.
- Não há nenhum experiência marketplace para criar conjuntos de dimensionamento de máquina virtual. Pode criar um conjunto, utilizando um modelo de dimensionamento.
- Definições de dimensionamento para conjuntos de dimensionamento de máquina virtual não estão disponíveis no portal. Como solução, pode utilizar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Devido às diferenças de versão do PowerShell, tem de utilizar o `-Name` parâmetro em vez de `-VMScaleSetName`.

#### <a name="networking"></a>Redes
- Não é possível criar um balanceador de carga com um endereço IP público utilizando o portal. Como solução, pode utilizar o PowerShell para criar o Balanceador de carga.
- Tem de criar uma regra de tradução (NAT) de endereço de rede quando cria um balanceador de carga de rede. Caso contrário, receberá um erro ao tentar adicionar uma regra NAT após a criação do Balanceador de carga.
- Em **redes**, se clicar em **ligação** para configurar uma ligação VPN, **VNet a VNet** está listado como um tipo de ligação possíveis. Não selecione esta opção. Atualmente, apenas o **Site a site (IPsec)** opção é suportada.
 
#### <a name="sqlmysql"></a>SQL Server/MySQL 
- Pode demorar até uma hora até os inquilinos podem criar bases de dados num novo SQL Server ou MySQL SKU. 
- Criação de itens diretamente no SQL Server e o MySQL servidores que não são executadas pelo fornecedor de recursos de alojamento não é suportada e pode resultar num Estado não correspondentes.

#### <a name="app-service"></a>Serviço de Aplicações
- Um utilizador tem de registar o fornecedor de recursos de armazenamento antes de poderem criarem a sua primeira função do Azure na subscrição.
 
#### <a name="usage-and-billing"></a>Utilização e faturação
- Públicos dados de medição de utilização a endereços IP mostram o mesmo *EventDateTime* valor para cada registo em vez do *TimeDate* carimbo que mostra que o registo foi criado. Atualmente, não é possível utilizar estes dados para efetuar exata contabilização da utilização de endereços IP pública.

## <a name="build-201709283"></a>Compilação 20170928.3

### <a name="known-issues"></a>Problemas conhecidos

#### <a name="powershell"></a>PowerShell
- A versão do módulo do PowerShell AzureRM 1.2.11 vem com uma lista de alterações de última hora. Para obter informações sobre a atualização a partir de 1.2.10 versão, consulte o [guia de migração](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Implementação
- Tem de especificar um servidor de tempo por endereço IP durante a implementação.

 #### <a name="infrastructure-management"></a>Gestão de infraestrutura
- Não ative a cópia de segurança de infraestrutura no **cópia de segurança da infraestrutura** painel.
- O fornecedor de recursos de computação apresenta um Estado desconhecido.
- O endereço IP do bmc gestão controlador BMC e o modelo não são apresentadas as informações essenciais de um nó de unidade de escala. Este comportamento é esperado no Kit de desenvolvimento de pilha do Azure. 
   
#### <a name="portal"></a>Portal
- Poderá ver um dashboard em branco no portal. Para recuperar o dashboard, selecione o ícone de equipamento no canto superior direito do portal e, em seguida, selecione **restaurar predefinições**.
- Ao visualizar as propriedades de um grupo de recursos, o **mover** botão está desativado. Este comportamento é esperado. Mover grupos de recursos entre subscrições não é atualmente suportado.
- Verá uma **ativação necessária** alerta de aviso que indica a registar o Kit de desenvolvimento de pilha do Azure. Este comportamento é esperado.
- No **ativação necessária** detalhes do alerta de aviso, não clique na ligação para o **AzureBridge** componente. Se o fizer, o **descrição geral** painel irá tentar carregar, sem êxito e não o tempo limite.
- Quotas podem não aparecer no portal do administrador depois de criá-los e, em seguida, tente vista posterior os detalhes de plano. Como solução, no **serviços e quotas**, clique em **adicionar**e adicione uma nova entrada.
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos completo e, em seguida, eliminar subscrições de utilizador.
- Não é possível ver permissões à sua subscrição utilizando os portais de pilha do Azure. Como solução, pode verificar as permissões com o Powershell.
  
#### <a name="marketplace"></a>Marketplace
- Os utilizadores podem procurar o mercado completo sem uma subscrição e podem ver itens administrativos como planos e ofertas. Estes itens estão não funcional para os utilizadores.
 
#### <a name="compute"></a>Computação
- Os utilizadores recebem a opção para criar uma máquina virtual com armazenamento georredundante. Esta configuração provoca a falha da criação máquina virtual.
- Pode configurar uma conjunto apenas com um domínio de falhas de um e um domínio de atualização de um de disponibilidade de máquina virtual.
- Não há nenhum experiência marketplace para criar conjuntos de dimensionamento de máquina virtual. Pode criar um conjunto, utilizando um modelo de dimensionamento.

#### <a name="networking"></a>Redes
- Não é possível criar um balanceador de carga com um endereço IP público utilizando o portal. Como solução, pode utilizar o PowerShell para criar o Balanceador de carga.
- Tem de criar uma regra de tradução (NAT) de endereço de rede quando cria um balanceador de carga de rede. Caso contrário, receberá um erro ao tentar adicionar uma regra NAT após a criação do Balanceador de carga.
- Em **redes**, se clicar em **ligação** para configurar uma ligação VPN, **VNet a VNet** está listado como um tipo de ligação possíveis. Não selecione esta opção. Atualmente, apenas o **Site a site (IPsec)** opção é suportada.


#### <a name="sqlmysql"></a>SQL Server/MySQL
- Pode demorar até uma hora até os inquilinos podem criar bases de dados num novo SQL Server ou MySQL SKU. 
- Criação de itens diretamente no SQL Server e o MySQL servidores que não são executadas pelo fornecedor de recursos de alojamento não é suportada e pode resultar num Estado não correspondentes.

#### <a name="app-service"></a>Serviço de Aplicações
- Um utilizador tem de registar o fornecedor de recursos de armazenamento antes de poderem criarem a sua primeira função do Azure na subscrição.
