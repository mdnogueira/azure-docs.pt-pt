---
title: "Criar funções de controlo de acesso baseado em funções personalizadas e atribua a utilizadores internos e externos no Azure | Microsoft Docs"
description: "Atribuir funções RBAC personalizadas criadas com o PowerShell e da CLI para utilizadores internos e externos"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: bb9b89d087cfb62efe63cf0ff600d7faa58a7b8b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
## <a name="intro-on-role-based-access-control"></a>Introdução no controlo de acesso baseado em funções

Controlo de acesso baseado em funções é uma funcionalidade de apenas portal do Azure, permitindo os proprietários de uma subscrição atribuir funções granulares a outros utilizadores que podem gerir âmbitos de recurso específico no respetivo ambiente.

RBAC permite um melhor gestão de segurança para organizações grandes e para PMEs trabalhar com colaboradores externos, fornecedores ou freelancers que necessitam de aceder a recursos específicos no seu ambiente, mas não necessariamente para toda a infraestrutura ou de qualquer âmbitos relacionadas com a faturação. RBAC permite a flexibilidade de proprietário de uma subscrição do Azure gerida pela conta de administrador (função de administrador de serviço a um nível de subscrição) e têm vários utilizadores convidados funcione na mesma subscrição, mas sem quaisquer direitos administrativos para o mesmo. Da perspetiva de faturação e de gestão, a funcionalidade RBAC comprova ser uma opção eficiente hora e a gestão para utilizar o Azure em vários cenários.

## <a name="prerequisites"></a>Pré-requisitos
Utilizar o RBAC no ambiente do Azure requer:

* Ter uma autónoma subscrição do Azure atribuída ao utilizador definir como proprietário (função de subscrição)
* Tem a função de proprietário da subscrição do Azure
* Tem acesso para o [portal do Azure](https://portal.azure.com)
* Certifique-se de que tem os seguintes fornecedores de recursos registado para a subscrição de utilizador: **Microsoft.Authorization**. Para obter mais informações sobre como registar os fornecedores de recursos, consulte [Gestor de recursos de fornecedores, regiões, versões de API e esquemas](/azure-resource-manager/resource-manager-supported-services.md).
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found---->

> [!NOTE]
> Subscrições do Office 365 ou licenças do Azure Active Directory (por exemplo: o acesso ao Azure Active Directory) aprovisionado a partir do Office 365 portal não qualidade utilizar o RBAC.

## <a name="how-can-rbac-be-used"></a>Como o RBAC pode ser utilizado
RBAC pode ser aplicada em três âmbitos diferentes no Azure. Do âmbito mais elevado ao mais baixo, estas são as seguintes:

* Subscrição (mais alta)
* Grupo de recursos
* Âmbito de recursos (nível mais baixo acesso oferta permissões direcionadas para um âmbito de recursos do Azure individual)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Atribuir funções RBAC no âmbito de subscrição
Existem dois exemplos comuns quando RBAC é utilizada (mas não se limitando a):

* (Que não faz parte do inquilino do Azure Active Directory do utilizador de admin) com utilizadores externos das organizações convidou para gerir determinados recursos ou a subscrição completa
* Trabalhar com os utilizadores dentro da organização (que fazem parte do inquilino do Azure Active Directory do utilizador) mas faz parte equipas diferentes ou grupos que precisam de acesso granular para a subscrição completa ou para determinados grupos de recursos ou âmbitos de recurso no ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acesso a um nível de subscrição de um utilizador fora do Azure Active Directory
Funções do RBAC podem ser concedidas apenas pelo **proprietários** da subscrição, por conseguinte, o utilizador de admin deve ter sessão iniciado com um nome de utilizador que tem a esta função previamente atribuída ou tiver criado a subscrição do Azure.

No portal do Azure, depois de a iniciar sessão como administrador, selecione "Subscrições" e escolha um pretendido.
![Painel de subscrição no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) por predefinição, se o utilizador de administrador tiver comprado a subscrição do Azure, o utilizador irá aparecer como **administrador da conta**, isto ser a função de subscrição. Para obter mais detalhes sobre as funções da subscrição do Azure, consulte [adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou serviços](/billing/billing-add-change-azure-subscription-administrator.md).

Neste exemplo, o utilizador "alflanigan@outlook.com" é o **proprietário** de "Avaliação gratuita" subscrição do AAD inquilino "Predefinido inquilino do Azure". Uma vez que este utilizador é o criador da subscrição do Azure com a Account Microsoft inicial "Outlook" (Microsoft Account = Outlook, etc. Live) o nome de domínio predefinido para todos os outros utilizadores adicionado neste inquilino será **"@alflaniganuoutlook.onmicrosoft.com"**. Por predefinição, a sintaxe do novo domínio está formada por colocando, em conjunto, o nome de domínio e nome de utilizador do utilizador que criou o inquilino e adicionar a extensão **". c o m"**.
Além disso, os utilizadores podem iniciar sessão com um nome de domínio personalizado no inquilino depois de adicionar e verificá-lo para o novo inquilino. Para obter mais detalhes sobre a forma de verificar um nome de domínio personalizado num inquilino do Azure Active Directory, consulte [adicionar um nome de domínio personalizado ao seu diretório](/active-directory/active-directory-add-domain).

Neste exemplo, o diretório de "Predefinição inquilino do Azure" contém apenas os utilizadores com o nome de domínio "@alflanigan.onmicrosoft.com".

Depois de selecionar a subscrição, o utilizador de administrador tem de clicar em **controlo de acesso (IAM)** e, em seguida, **adicionar uma nova função**.





![funcionalidade de IAM de controlo de acesso no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Adicionar novo utilizador na funcionalidade IAM de controlo de acesso no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

O passo seguinte consiste em selecionar qual será atribuída a função RBAC para a função a ser atribuída e o utilizador. No **função** menu pendente do utilizador de admin vê apenas as RBAC funções incorporadas que estão disponíveis no Azure. Para obter uma explicação de cada função e os respetivos âmbitos atribuíveis mais detalhadas, consulte [funções incorporadas para controlo de acesso em funções do Azure](/active-directory/role-based-access-built-in-roles.md).
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found---->

O utilizador de administrador, em seguida, tem de adicionar o endereço de e-mail do utilizador externo. É o comportamento esperado para o utilizador externo não apareçam no inquilino existente. Depois do utilizador externo tem foi convidado, ele vai estar visível em **subscrições > controlo de acesso (IAM)** com todos os utilizadores atuais que estão atualmente atribuídos uma função RBAC no âmbito de subscrição.





![adicionar permissões a nova função RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![lista de funções do RBAC ao nível da subscrição](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

O utilizador "chessercarlton@gmail.com" tem foi convidado para ser um **proprietário** para a subscrição de "Avaliação gratuita". Depois de enviar o convite, o utilizador externo irá receber uma confirmação de e-mail com uma ligação de ativação.
![e-mail de convite para a função RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Ser externas à organização, o novo utilizador não tem quaisquer atributos existentes no diretório "Predefinição inquilino do Azure". Estes serão criados depois do utilizador externo forneceu consentimento para serem gravados no diretório que está associado à subscrição que ele foi atribuído uma função para.





![mensagem de convite de correio eletrónico para a função RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

Mostra o utilizador externo no inquilino do Azure Active Directory a partir agora no como utilizador externo e isto pode ser visualizado no portal do Azure e no portal clássico.





![portal do Azure do Active Directory do azure do painel de utilizadores](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![os utilizadores do azure do painel do Active Directory portal clássico do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

No **utilizadores** vista em ambos os portais os utilizadores externos podem ser reconhecidos por:

* O tipo de ícone diferente no portal do Azure
* O ponto de sourcing diferentes no portal clássico

No entanto, conceder **proprietário** ou **contribuinte** acesso a um utilizador externo no **subscrição** âmbito, não permite o acesso ao diretório do utilizador administrador, a menos que o **Administrador Global** permite. No proprieties utilizador, o **tipo de utilizador** que tem dois parâmetros comuns, **membro** e **convidado** podem ser identificados. Um membro é um utilizador que está registado no diretório, enquanto um convidado é um utilizador convidado para o diretório a partir de uma origem externa. Para obter mais informações, consulte [como os administradores do Azure Active Directory adicionar utilizadores de colaboração do B2B](/active-directory/active-directory-b2b-admin-add-users).
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> Certifique-se de que, depois de introduzir as credenciais no portal, o utilizador externo seleciona o diretório correto para início de sessão para. O mesmo utilizador pode ter acesso a vários diretórios e selecione um dos mesmos, clicando em nome de utilizador no lado superior direito no portal do Azure e, em seguida, selecione o diretório adequado na lista pendente.

Ao ser convidado no diretório, o utilizador externo pode gerir todos os recursos da subscrição do Azure, mas não é possível aceder ao diretório.





![acesso restringido ao portal do Azure do azure do Active Directory](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory e uma subscrição do Azure não tem uma relação principal-subordinado como outros recursos do Azure (por exemplo: máquinas virtuais, redes virtuais, as aplicações web de armazenamento, etc.) com uma subscrição do Azure. Todos os última opção é criada, geridos e faturada numa subscrição do Azure, enquanto uma subscrição do Azure é utilizada para gerir o acesso a um diretório do Azure. Para obter mais detalhes, consulte [subscrição como um Azure está relacionada com o Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

De todos os as RBAC funções incorporadas, **proprietário** e **contribuinte** oferecem acesso de gestão completo a todos os recursos no ambiente, a diferença é que um contribuinte não é possível criar e eliminar funções RBAC novo. As outras funções incorporadas, como **contribuinte de Máquina Virtual** oferecem acesso completa de gestão apenas para os recursos indicados pelo nome, independentemente do **grupo de recursos** estão a ser criadas numa.

Atribuir a função incorporada do RBAC do **contribuinte de Máquina Virtual** a um nível de subscrição, significa que a função atribuída ao utilizador:

* Pode ver todas as máquinas virtuais, independentemente respetiva data da implementação e os grupos de recursos que fazem parte do
* Tem acesso de gestão completa para as máquinas virtuais na subscrição
* Não é possível ver outros tipos de recursos na subscrição
* Não é possível operar quaisquer alterações de uma perspetiva de faturação

> [!NOTE]
> RBAC que está a ser uma funcionalidade apenas portal do Azure, não conceda acesso para o portal clássico.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Atribuir uma função incorporada do RBAC para um utilizador externo
Para um cenário de diferentes neste teste, o utilizador externo "alflanigan@gmail.com" é adicionado como um **contribuinte de Máquina Virtual**.




![função incorporada de contribuinte de máquina virtual](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

É o comportamento normal para este utilizador externo com esta função incorporada para ver e gerir apenas máquinas virtuais e os respetivos adjacentes Gestor de recursos apenas recursos necessário durante a implementação. Por predefinição, estas funções limitadas oferecem acesso apenas aos respetivos recursos correspondente criado no portal do Azure, independentemente de já algumas podem ainda ser implementadas no portal clássico (por exemplo: máquinas virtuais).





![Descrição geral da função de contribuinte de máquina virtual no portal do azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Conceder acesso a um nível de subscrição de um utilizador no mesmo diretório
O fluxo de processo é idêntico ao adicionar um utilizador externo, da perspetiva de administração concedendo a função RBAC, bem como o utilizador ser concedido acesso à função. A diferença aqui é que o utilizador convidado não receberão qualquer convites para correio eletrónico como todos os âmbitos de recursos dentro da subscrição estará disponíveis no dashboard, depois de iniciar sessão.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Atribuir funções RBAC no âmbito do grupo de recursos
Atribuir uma função do RBAC um **grupo de recursos** âmbito tem um processo idêntico para atribuir a função ao nível da subscrição, para ambos os tipos de utilizadores - internos ou externos (parte do mesmo diretório). Os utilizadores que estão atribuídos a função RBAC é ver no seu ambiente, apenas o grupo de recursos foram atribuídos acesso a partir de **grupos de recursos** ícone no portal do Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Atribuir funções RBAC no âmbito do recurso
Atribuir uma função RBAC num âmbito de recursos no Azure tem um processo idêntico para atribuir a função ao nível da subscrição ou ao nível do grupo de recursos, seguindo o mesmo fluxo de trabalho para ambos os cenários. Novamente, os utilizadores que estão atribuídos a função RBAC podem ver apenas os itens que tenham sido atribuídos acesso a, tem de **todos os recursos** separador ou diretamente no seu dashboard.

Um aspeto importante para RBAC tanto no âmbito do grupo de recursos ou âmbito de recursos é para os utilizadores para se certificar de início de sessão para o diretório correto.





![início de sessão de diretório no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Atribuir funções RBAC para um grupo do Azure Active Directory
Todos os cenários utilizando o RBAC nos três âmbitos diferentes no Azure oferecem o privilégio de gerir, implementar e administrar vários recursos como um utilizador atribuído sem a necessidade de gerir uma subscrição pessoal. Independentemente de já está atribuída a função RBAC para uma subscrição, o grupo de recursos ou o âmbito de recursos, todos os recursos criados no adicionais pelos utilizadores atribuídos são faturados sob a uma subscrição do Azure onde os utilizadores têm acesso a. Desta forma, os utilizadores que têm permissões de administrador para essa subscrição do Azure completa de faturação não tem uma descrição geral completa no consumo, independentemente que está a gerir os recursos.

Para organizações de maior, as funções do RBAC podem ser aplicadas da mesma forma para grupos do Azure Active Directory considerar que o utilizador de administrador pretende conceder o acesso granular para equipas ou departamentos de todos, não individualmente para cada utilizador, considerar, assim como extremamente hora e a gestão eficiente opção perspetiva. Para ilustrar neste exemplo, o **contribuinte** função foi adicionada a um dos grupos no inquilino ao nível da subscrição.





![adicionar a função RBAC para grupos do AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Estes grupos são grupos de segurança que são aprovisionados e geridos apenas dentro do Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Criar uma função RBAC personalizada para abrir os pedidos de suporte utilizando o PowerShell
As funções incorporadas do RBAC que estão disponíveis no Azure Certifique-se determinados níveis de permissão com base nos recursos disponíveis no ambiente. No entanto, se nenhuma destas funções conforme as necessidades do utilizador de admin, há a opção para limitar o acesso ainda mais através da criação de funções RBAC personalizadas.

A criação de funções do RBAC personalizadas requer para colocar uma função incorporada, editá-lo e, em seguida, importá-lo novamente no ambiente. A transferência e o carregamento da função são geridos através do PowerShell ou a CLI.

É importante compreender os pré-requisitos de criação de uma função personalizada que podem conceder acesso granular ao nível da subscrição e também permitir que o utilizador convidado a flexibilidade de abertura de pedidos de suporte.

Para este exemplo, a função incorporada **leitor** que permite aos utilizadores acesso para ver todos os âmbitos de recursos, mas não para editá-los ou criar novos tiver sido personalizado para permitir ao utilizador a opção de abrir os pedidos de suporte.

A primeira ação de exportar o **leitor** função tem de ser concluídas por PowerShell foi executada com permissões elevadas como administrador.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Captura de ecrã do PowerShell para a função de leitor RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

Em seguida, terá de extrair o modelo JSON da função.





![Modelo JSON para a função de leitor RBAC personalizada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Uma função RBAC típica é composto pelos fora três secções principais, **ações**, **NotActions** e **AssignableScopes**.

No **ação** secção são apresentadas todas as operações permitidas para esta função. É importante compreender que cada ação é atribuída de um fornecedor de recursos. Neste caso, para criar pedidos de suporte a **Microsoft. support** fornecedor de recursos tem de estar listado.

Para conseguir ver todos os recursos fornecedores disponíveis e registados na sua subscrição, pode utilizar o PowerShell.
```
Get-AzureRMResourceProvider

```
Além disso, pode verificar a existência de todos os os disponíveis cmdlets do PowerShell gerir os fornecedores de recursos.
    ![Captura de ecrã do PowerShell para gestão do fornecedor de recursos](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Para restringir a todas as ações para a função RBAC específica, os fornecedores de recursos estão listados na secção de **NotActions**.
Pela última vez, é obrigatório que a função RBAC contém a subscrição explícita IDs em que é utilizado. Os IDs de subscrição estão listados no **AssignableScopes**, caso contrário, não serão permitidas para importar a função na sua subscrição.

Depois de criar e personalizar a função RBAC, tem de ser importado fazer uma cópia do ambiente.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

Neste exemplo, o nome personalizado para esta função RBAC é "Leitor suporte bilhetes nível de acesso" permitir que o utilizador para ver tudo na subscrição e também para abrir a pedidos de suporte.

> [!NOTE]
> As funções RBAC de incorporada apenas dois, permitindo que a ação de abertura dos pedidos de suporte são **proprietário** e **contribuinte**. Para um utilizador poderá abrir os pedidos de suporte, ele tem de ser atribuído uma função RBAC apenas no âmbito de subscrição, porque todos os pedidos de suporte são criados com base numa subscrição do Azure.

Esta nova função personalizada foi atribuída a um utilizador do mesmo diretório.





![captura de ecrã da função RBAC personalizada importada no portal do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![captura de ecrã de atribuição de função RBAC importada personalizada ao utilizador no mesmo diretório](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![captura de ecrã de permissões para a função RBAC importada personalizada](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

O exemplo tem sido mais detalhado para destacar os limites desta função RBAC personalizada da seguinte forma:
* Pode criar novos pedidos de suporte
* Não é possível criar novos âmbitos de recursos (por exemplo: máquina virtual)
* Não é possível criar novos grupos de recursos





![captura de ecrã da função RBAC personalizada criar pedidos de suporte](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![captura de ecrã da função RBAC personalizada não é possível criar VMs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![captura de ecrã da função RBAC personalizada não é possível criar o novo RGs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Criar uma função RBAC personalizada para abrir os pedidos de suporte utilizando a CLI do Azure
Em execução num Mac e sem ter acesso para o PowerShell, a CLI do Azure é a forma de aceder.

Os passos para criar uma função personalizada são os mesmos, com a exceção única que utilizar a CLI a função não pode ser transferido num modelo JSON, mas podem ser visualizado na CLI.

Para este exemplo posso escolheu a função incorporada de **leitor de cópia de segurança**.

```

azure role show "backup reader" --json

```





![Mostrar CLI captura de ecrã da função de leitor de cópia de segurança](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Editar a função no Visual Studio depois de copiar os proprieties num modelo JSON, o **Microsoft. support** foi adicionado no fornecedor de recursos a **ações** secções para que este utilizador pode abrir pedidos de suporte ao continuar a ser um leitor para os cofres de cópia de segurança. Novamente é necessário adicionar o ID de subscrição em que esta função será utilizada o **AssignableScopes** secção.

```

azure role create --inputfile <path>

```





![CLI captura de ecrã da importação de função personalizada do RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

A nova função está agora disponível no portal do Azure e o processo de assignation é o mesmo, tal como nos exemplos anteriores.





![Captura de ecrã portal do Azure de função RBAC personalizada criada utilizando a CLI 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

A partir de 2017 de compilação mais recente, a Shell de nuvem do Azure estiver geralmente disponível. Shell de nuvem do Azure é um complemento IDE e o Portal do Azure. Com este serviço, obtenha uma shell baseada no browser, que é autenticada e está lojada no Azure e pode utilizá-lo em vez de CLI instalado no seu computador.





![Shell de nuvem do Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
