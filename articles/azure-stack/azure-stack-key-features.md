---
title: A chave de funcionalidades e conceitos na pilha do Azure | Microsoft Docs
description: Saiba mais sobre as principais funcionalidades e conceitos na pilha do Azure.
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: fd16748e1369b8abcab38ce1945f72c681c344b8
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="key-features-and-concepts-in-azure-stack"></a>As principais funcionalidades e conceitos na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Se estiver familiarizado com o Microsoft Azure pilha, estes termos de licenciamento e descrições das funcionalidades poderão ser úteis.

## <a name="personas"></a>Pessoas fictícias
Existem dois varieties de utilizadores para a pilha do Microsoft Azure, o operador da nuvem (fornecedor) e de inquilino (consumidor).

* A **operador da nuvem** pode configurar a pilha do Azure e gerir ofertas, esquemas, serviços, quotas e preços para fornecer recursos para os seus inquilinos.  Além disso, os operadores da nuvem capacidade de gerir e respondem a alertas.  
* A **inquilino** (também referido como um utilizador) consome serviços que o administrador da nuvem oferece. Os inquilinos podem aprovisionar, monitorizar e gerir serviços que são subscritos, tais como Web Apps, armazenamento e máquinas virtuais.

## <a name="portal"></a>Portal
Os métodos principais da interação com o Microsoft Azure pilha são o portal de administrador, o portal de utilizador e o PowerShell.

Os portais de pilha do Azure são apoiados cada por instâncias separadas do Gestor de recursos do Azure.  Um operador da nuvem utiliza o portal de administrador para gerir a pilha do Azure e para fazer coisas como criar ofertas de inquilino.  O portal de utilizador (também referido como o portal de inquilinos) fornece uma experiência self-service para consumo dos recursos de nuvem, como máquinas virtuais, contas de armazenamento e as aplicações Web. Para obter mais informações, consulte [utilizando os portais de administrador e utilizador da pilha de Azure](azure-stack-manage-portals.md).

## <a name="identity"></a>Identidade 
Pilha do Azure utiliza o Azure Active Directory (AAD) ou serviços de Federação do Active Directory (AD FS) como um fornecedor de identidade.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory é o fornecedor de identidade baseada na cloud, multi-inquilino da Microsoft.  A maioria dos cenários híbridos utilizam o Azure Active Directory como o arquivo de identidade.

### <a name="active-directory-federation-services"></a>Serviços de Federação do Active Directory
Pode optar por utilizar os serviços de Federação do Active Directory (AD FS) para implementações desligadas da pilha do Azure.  Pilha do Azure, fornecedores de recursos e outras aplicações funcionam muito da mesma forma com o AD FS, tal como com o Azure Active Directory. Pilha do Azure inclui a sua própria instância do AD FS e o Active Directory e uma API de gráfico do Active Directory. Kit de desenvolvimento de pilha do Azure suporta os seguintes cenários de AD FS:

- Iniciar sessão para a implementação através da utilização do AD FS.
- Criar uma máquina virtual com segredos no Cofre de chaves
- Criar um cofre para armazenar/aceder segredos
- Criar funções de RBAC personalizadas na subscrição
- Atribuir utilizadores a funções RBAC em recursos
- Criar funções RBAC de todo sistema através do PowerShell do Azure
- Inicie sessão como um utilizador através do PowerShell do Azure
- Criar serviço principais utilizá-los para iniciar sessão no Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiões, serviços, esquemas, ofertas e subscrições
Na pilha do Azure, os serviços são fornecidos aos inquilinos utilizando regiões, subscrições, ofertas e planos. Podem subscrever oferece vários inquilinos. Ofertas podem ter um ou mais planos e planos podem ter um ou mais serviços.

![](media/azure-stack-key-features/image4.png)

Exemplo de hierarquia das subscrições de um inquilino para ofertas, cada um com vários planos e serviços.

### <a name="regions"></a>Regiões
Regiões de pilha do Azure são um elemento básico de escala e gestão. Uma organização pode ter várias regiões com recursos disponíveis em cada região. Regiões podem também ter ofertas de serviço diferentes disponíveis. No Kit de desenvolvimento de pilha do Azure, é suportada apenas uma única região e automaticamente o nome *local*.

### <a name="services"></a>Serviços
Pilha do Microsoft Azure permite aos fornecedores de fornecer uma ampla variedade de aplicações e serviços, tais como máquinas virtuais, SQL Server bases de dados, SharePoint, Exchange e muito mais.

### <a name="plans"></a>Planos
Planos são agrupamentos de um ou mais serviços. Como um fornecedor, criar planos para oferecer aos seus inquilinos. Por sua vez, os seus inquilinos subscrevem as suas ofertas para utilizar os planos e os serviços incluídos.

Cada adicionado a um plano de serviço pode ser configurado com as definições de quota para o ajudar a gerir a sua capacidade de nuvem. Quotas podem incluir restrições, como os limites VM, CPU e RAM e são aplicadas por subscrição de utilizador. Quotas podem ser diferenciadas por localização. Por exemplo, um plano de serviços de computação da região A ter uma quota de duas máquinas virtuais, 4 GB de RAM e 10 núcleos de CPU.

Ao criar uma oferta, o administrador de serviços pode incluir um **planos base**. Estes esquemas de base são incluídas por predefinição, quando um inquilino subscreve que oferta. Quando um utilizador subscreve (e é criada a subscrição), o utilizador tem acesso a todos os fornecedores de recursos especificado esses planos base (com as quotas correspondentes).

O administrador de serviços também pode incluir **planos de suplemento** numa oferta. Planos de suplemento não estão incluídos por predefinição na subscrição. Planos de suplemento estão disponíveis numa oferta, que pode adicionar um proprietário da subscrição para as suas subscrições de adicionais planos (as quotas).

### <a name="offers"></a>Ofertas
Ofertas são grupos de um ou mais planos que Fornecedores aos inquilinos comprar (subscrever). Por exemplo, oferecem Alpha pode conter um plano de que contém um conjunto de serviços de computação e planear B, que contém um conjunto de serviços de armazenamento e rede.

Uma oferta inclui um conjunto de planos base e os administradores de serviço podem criar planos de suplemento inquilinos podem adicionar à respetiva subscrição.

### <a name="subscriptions"></a>Subscrições
Uma subscrição é como inquilinos comprar a sua ofertas. Uma subscrição é uma combinação de um inquilino com uma oferta. Um inquilino pode ter subscrições para vários ofertas. Cada subscrição aplica-se a oferta apenas um. Subscrições de um inquilino determinam que planos/serviços que podem aceder.

Subscrições ajudam fornecedores organizar e aceder aos serviços e recursos de nuvem.

Para o administrador, uma subscrição do fornecedor predefinido é criada durante a implementação. Esta subscrição pode ser utilizada para gerir a pilha do Azure, implementar mais fornecedores de recursos e criar planos e as ofertas para os inquilinos. Não deve ser utilizada para executar aplicações e cargas de trabalho do cliente. 

## <a name="azure-resource-manager"></a>Azure Resource Manager
Ao utilizar o Azure Resource Manager, pode trabalhar com os recursos de infraestrutura de um modelo declarativo, com base no modelo.   Fornece uma única interface que pode utilizar para implementar e gerir os componentes da solução. Para informações completas e orientações, consulte o [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupos de recursos
Grupos de recursos são colecções de recursos, serviços e aplicações — e cada recurso tem um tipo, tais como máquinas virtuais, redes virtuais, IPs públicos, contas de armazenamento e Web sites. Cada recurso tem de estar num grupo de recursos e para ajudam a grupos de recursos logicamente organizar os recursos, como por carga de trabalho ou localização.  Na pilha do Microsoft Azure, recursos, tais como planos e oferece também são geridos em grupos de recursos.
 
### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Com o Azure Resource Manager, pode criar um modelo (no formato JSON) que define a implementação e configuração da sua aplicação. Este modelo é conhecido como um modelo Azure Resource Manager e fornece uma forma declarativa de definir a implementação. Ao utilizar um modelo, pode implementar repetidamente a aplicação durante todo o ciclo de vida da mesma e ter a confiança de que os recursos são implementados num estado consistente.

## <a name="resource-providers-rps"></a>Fornecedores de recursos (RPs)
Fornecedores de recursos são serviços de PaaS e serviços web que formam a base para todos os IaaS baseado no Azure. O Azure Resource Manager depende do RPs diferentes para fornecer acesso aos serviços.

Existem quatro RPs dos: rede, armazenamento, computação e KeyVault. Cada um destes RPs ajuda-o a configurar e a controlar os respetivos recursos. Os administradores de serviços também podem adicionar novos fornecedores de recursos personalizados.

### <a name="compute-rp"></a>RP de computação
O fornecedor de recursos de computação (CRP) permite aos inquilinos de pilha do Azure criar as suas próprias máquinas virtuais. O CRP inclui a capacidade para criar máquinas virtuais, bem como as extensões de Máquina Virtual. O serviço de extensão da Máquina Virtual ajuda a fornecer capacidades de IaaS para máquinas virtuais Windows e Linux.  Por exemplo, pode utilizar o CRP para Aprovisionar uma máquina virtual Linux e executar scripts de Bash durante a implementação para configurar a VM.

### <a name="network-rp"></a>Rede RP
O fornecedor de recursos de rede (NRP) fornece uma série de funcionalidades definido rede Software (SDN) e a virtualização de função de rede (NFV) para a nuvem privada.  Pode utilizar o NRP para criar recursos como software carga balanceadores, os IPs públicos, grupos de segurança, redes virtuais.

### <a name="storage-rp"></a>Armazenamento RP
O armazenamento RP oferece quatro serviços de armazenamento do Azure consistente: blob, tabela, fila e gestão de contas. Também oferece um serviço de administração do armazenamento em nuvem para facilitar a administração de fornecedor de serviço dos serviços de armazenamento do Azure consistente. Storage do Azure fornece a flexibilidade para armazenar e obter grandes quantidades de dados não estruturados, como documentos e ficheiros de multimédia com Blobs do Azure, e NoSQL estruturado com base em dados em tabelas do Azure. Para obter mais informações sobre o Storage do Azure, consulte [introdução ao Storage do Microsoft Azure](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Armazenamento de blobs
O blob storage armazena qualquer conjunto de dados. Um blob pode ser qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro multimédia ou o instalador da aplicação. O Table storage armazena conjuntos de dados estruturados. O Table Storage é um arquivo de dados do atributo chave NoSQL, que permite um rápido desenvolvimento e um acesso rápido às grandes quantidades de dados. Armazenamento de filas fornece mensagens fiáveis para processamento de fluxo de trabalho e para a comunicação entre os componentes dos serviços em nuvem.

Cada blob está organizado num contentor. Os contentores também fornecem uma forma útil de atribuir políticas de segurança a grupos de objetos. Uma conta do Storage pode conter qualquer número de contentores e um contentor pode conter qualquer número de blobs, até ao limite de capacidade de 500 TB da conta do Storage. O Blob Storage oferece três tipos de blobs: blobs de blocos, blobs de acréscimo e blobs de página (discos). Os blobs de blocos estão otimizados para transmitir em fluxo e armazenar os objetos da nuvem e são uma boa opção para armazenar documentos, ficheiros multimédia, cópias de segurança, etc. Os blobs de acréscimo são semelhantes aos blobs de blocos, mas estão otimizados para as operações de acréscimo. Um blob de acréscimo apenas pode ser atualizado adicionando um novo bloco ao final. Os blobs de acréscimo são uma boa opção para cenários como o registo, onde novos dados têm de ser escritos apenas no final do blob. Os blobs de página estão otimizados para representar discos IaaS e suportar aleatório escreve e pode ter até 1 TB. Um disco IaaS anexado a uma rede de Virtual Machines do Azure é um VHD armazenado como um blob de páginas.

#### <a name="table-storage"></a>Table Storage
O Table storage é o arquivo de chaves/atributos NoSQL da Microsoft – tem um design sem esquemas, que o diferencia das tradicionais bases de dados relacionais. Uma vez que os esquemas de falta de armazenamentos de dados, é fácil adaptar os dados que as necessidades da sua aplicação evoluem. O Table Storage é fácil de utilizar para que os programadores possam criar aplicações rapidamente. O Table Storage é um arquivo de atributo chave, o que significa que cada valor numa tabela é armazenado com um nome de propriedade escrito. O nome da propriedade pode ser utilizado para filtrar e especificar critérios de seleção. Uma coleção de propriedades e os respetivos valores compõem uma entidade. Dado que os esquemas de falta de armazenamento de tabela, duas entidades na mesma tabela podem conter diferentes coleções de propriedades e essas propriedades podem ser de diferentes tipos. Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela e uma conta do Storage pode conter quantas tabelas pretender, até ao limite de capacidade da conta do Storage.

#### <a name="queue-storage"></a>Armazenamento de filas
O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Ao conceber aplicações para o dimensionamento, os componentes da aplicação, muitas vezes, são desacoplados para um dimensionamento independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

### <a name="keyvault"></a>KeyVault
O KeyVault RP fornece gestão e auditoria de segredos, tais como palavras-passe e certificados. Por exemplo, um inquilino pode utilizar o RP KeyVault para fornecer palavras-passe de administrador ou de chaves durante a implementação de VM.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso (RBAC) baseado em funções
Pode utilizar o RBAC para conceder acesso de sistema para que os utilizadores autorizados, grupos e serviços ao atribuir-lhes funções uma subscrição, o grupo de recursos ou o nível de recursos individuais. Cada função define o nível de acesso que um utilizador, grupo ou serviço tem sobre recursos de pilha do Microsoft Azure.

RBAC do Azure tem três funções básicas que se aplicam a todos os tipos de recurso: proprietário, leitor e contribuinte. O proprietário tem acesso total a todos os recursos, incluindo o direito para delegar o acesso a outras pessoas. Contribuidor pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas. Leitor só pode ver os recursos do Azure existentes. O resto das funções do RBAC do Azure permite a gestão de recursos do Azure específicos. Por exemplo, a função de contribuinte de Máquina Virtual permite a criação e gestão de máquinas virtuais, mas não permite a gestão de rede virtual ou que a máquina virtual estabelece ligação à sub-rede.

## <a name="usage-data"></a>Dados de utilização
Pilha do Microsoft Azure recolhe e agrega dados de utilização através de todos os fornecedores de recursos e transmite-lo para o Azure para o processamento de comércio do Azure. Os dados de utilização recolhidos na pilha do Azure podem ser visualizados através de uma API REST. Há uma API de inquilinos consistentes com o Azure, bem como fornecedor e o delegado APIs do fornecedor para obter dados de utilização em todas as subscrições de inquilino. Estes dados podem ser utilizados para integrar com uma ferramenta externa ou o serviço de faturação ou encargos. Depois de utilização tenha sido processada pela comércio do Azure, podem ser visualizado no portal de faturação do Azure.

## <a name="in-development-build-of-azure-stack-development-kit"></a>Compilação no desenvolvimento do Kit de desenvolvimento de pilha do Azure
Compilações em desenvolvimento permitem adotantes iniciais avaliar a versão mais recente do Kit de desenvolvimento de pilha do Azure. Se estiver a incrementais compilações com base na versão mais recente principais. Embora versões principais irão continuar a ser libertado cada alguns meses, o baseiam-se no desenvolvimento vai lançar ligados intermitentemente entre as versões principais.

Compilações em desenvolvimento irão fornecer as seguintes vantagens:
- Correções de erros
- Novas funcionalidades
- Outras melhorias

## <a name="next-steps"></a>Passos seguintes
[Pré-requisitos de implementação de pilha do Azure](azure-stack-deploy.md)

