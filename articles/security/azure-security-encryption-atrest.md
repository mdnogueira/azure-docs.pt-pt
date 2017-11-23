---
title: "Dados de Microsoft Azure encriptação-em-Rest | Microsoft Docs"
description: "Este artigo fornece uma descrição geral do Microsoft Azure dados encriptação em-rest, as capacidades gerais e as considerações gerais."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: b02afa77ce99f576fed76b398642ba3f3ce2ba98
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-encryption-at-rest"></a>Dados do Azure encriptação em Rest
Existem várias ferramentas dentro do Microsoft Azure para salvaguardar os dados de acordo com as necessidades de segurança e conformidade da sua empresa. Este documento centra-se em:
- Como os dados estão protegidos Inativos em todo o Microsoft Azure
- Descreve os vários componentes a demorar de parte da implementação da proteção de dados,
- Revê os profissionais de TI e contras das abordagens de proteção de gestão de chaves diferentes. 

Encriptação de Inativos é um requisito de segurança comuns. Uma vantagem do Microsoft Azure é que as organizações podem alcançar a encriptação de Inativos sem ter do custo de implementação e gestão e o risco de uma solução de gestão de chaves personalizadas. As organizações têm a opção de permitir que o Azure completamente gerir a encriptação de inativos. Além disso, as organizações têm várias opções para gerir encriptação ou chaves de encriptação.

## <a name="what-is-encryption-at-rest"></a>O que é a encriptação de Inativos?
Encriptação de Inativos refere-se para a criptografia codificação (encriptação) de dados quando é mantida. A encriptação em estruturas de Rest no Azure utilizar encriptação simétricas para encriptar e desencriptar grandes quantidades de dados rapidamente, de acordo com um modelo conceptual simple:

- Uma chave de encriptação simétrica é utilizada para encriptar os dados que é persistente 
- A mesma chave de encriptação é utilizada para desencriptar os dados, que é readied para utilização na memória
- Poderão ser particionados dados e podem ser utilizadas chaves diferentes para cada partição
- As chaves têm de ser armazenadas numa localização segura com políticas de controlo de acesso limitar o acesso a determinadas identidades e utilização de chave de registo. Chaves de encriptação de dados, muitas vezes, são encriptadas com encriptação assimétrica para limitar ainda mais o acesso (abordado o *chave hierarquia*, mais à frente neste artigo)

O procedimento acima descreve os elementos comuns de alto nível de encriptação de inativos. Na prática, cenários de gestão e o controlo de chaves, bem como oferece garantias ao rendimento dimensionamento e disponibilidade, requerem construções adicionais. Microsoft Azure a encriptação Rest conceitos e componentes são descritas abaixo.

## <a name="the-purpose-of-encryption-at-rest"></a>O objetivo de encriptação de Inativos
Encriptação de Inativos destina-se para fornecer proteção de dados para dados em-rest (conforme descrito acima.) Os ataques contra dados em rest incluem tenta obter acesso físico para o hardware no qual os dados são armazenados e, em seguida, comprometer os dados contidos. Num ataque de unidade de disco rígido de um servidor pode ter sido mishandled durante a manutenção, permitindo que um atacante remover o disco rígido. Mais tarde o atacante seria colocar o disco rígido no computador sob o seu controlo a tentar aceder os dados. 

Encriptação de Inativos foi concebida para impedir que o atacante acedam a não encriptada dados, assegurando que os dados são encriptados quando no disco. Se um atacante obter um disco rígido com tais encriptados sem acesso às chaves de encriptação e dados, o atacante não iria comprometer os dados sem dificuldade em excelente. Neste cenário, um atacante teria tentar ataques contra os dados encriptados são muito mais complexos e consumir recursos que aceder não encriptada dados num disco rígido. Por este motivo, a encriptação de Inativos é vivamente recomendável e é um requisito de alta prioridade para muitas organizações. 

Em alguns casos, a encriptação de Inativos também é necessário pelo necessidade de uma organização de esforços de governação e conformidade de dados. As normas da indústria e governamentais, como HIPAA, PCI e FedRAMP, esquematizar salvaguardas específicas sobre requisitos de encriptação e proteção de dados. Para muitos essas normas de encriptação de Inativos é uma medida de obrigatória necessária para a proteção e gestão de dados em conformidade. 

Para além dos requisitos de regulamentação e conformidade, encriptação de Inativos deve ser interpretada como uma capacidade de plataforma de defesa em profundidade. Apesar da Microsoft fornece uma plataforma em conformidade para os serviços, aplicações, e dados, das instalações abrangente e segurança física, dados de controlo de acesso e auditoria, é importante fornecer adicionais "sobrepostos" medidas de segurança no caso de uma das outras segurança mede a falhar. Encriptação de Inativos fornece essa um mecanismo de defesa adicional.

A Microsoft está empenhada em fornecer opções de rest a encriptação em serviços de nuvem e para fornecer aos clientes adequada capacidade de gestão de chaves de encriptação e acesso a registos a mostrar quando são utilizadas chaves de encriptação. Além disso, a Microsoft está a trabalhar para o objetivo de efetuar todos os dados de cliente encriptados em descanso ao, por predefinição.

## <a name="azure-encryption-at-rest-components"></a>Componentes de Rest a encriptação do Azure

Tal como descrito anteriormente, o objetivo de encriptação de Inativos é que os dados que são mantidos no disco são encriptados com uma chave de encriptação do segredo. Para atingir esse objetivo segura chave criação, armazenamento, o controlo de acesso e gestão de chaves de encriptação tem de ser fornecidos. Embora detalhes podem variar, os serviços do Azure encriptação em implementações de Rest podem ser descritos em termos do abaixo conceitos que, em seguida, são ilustrados no diagrama seguinte.

![Componentes](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

A localização de armazenamento de chaves de encriptação e controlo de acesso a essas chaves é fundamental para uma encriptação no modelo de rest. As chaves têm de ser altamente seguros, mas gerível pelos utilizadores especificados e disponível para serviços específicos. Para serviços do Azure, o Cofre de chaves do Azure é a solução de armazenamento de chave recomendado e fornece uma experiência de gestão comuns em serviços. As chaves são armazenadas e geridas no cofres de chaves, e pode ser especificado o acesso a um cofre de chaves para utilizadores ou serviços. O Cofre de chaves do Azure suporta a criação de cliente de chaves ou a importação das chaves de cliente para utilização em situações de chave de encriptação gerida pelo cliente.

### <a name="azure-active-directory"></a>Azure Active Directory

Permissões para utilizar as chaves armazenadas no Cofre de chaves do Azure para gerir ou para aceder aos mesmos para a encriptação de encriptação de inativos e desencriptação, podem ser especificadas para as contas do Azure Active Directory. 

### <a name="key-hierarchy"></a>Hierarquia de chave

Mais do que uma chave de encriptação é utilizada numa encriptação na implementação de rest. Encriptação assimétrica é útil para estabelecer a fidedignidade e o necessário para a gestão e acesso a chaves de autenticação. Encriptação simétrica é mais eficiente para encriptação em massa e de desencriptação, permitindo uma encriptação mais forte e melhor desempenho. Além disso, limitar a utilização de uma chave de encriptação único diminui o risco de que a chave serão comprometida e o custo de reencriptação quando uma chave têm de ser substituída. Para tirar partido dos benefícios de encriptação simétrica e assimétrico e limitar a utilização e a exposição de uma única chave, Azure encriptações em modelos de rest utilizam uma hierarquia de chave efetuada cópias de segurança dos seguintes tipos de chaves:

- **Chave de encriptação de dados (DEK)** – uma chave simétrica de AES256 utilizada para encriptar uma partição ou o bloco de dados.  Um único recurso pode ter partições muitas e muitas chaves de encriptação de dados. Encriptação de cada bloco de dados com uma chave diferente torna ataques de análise de criptografia mais difícil. Acesso a DEKs é necessário para a instância de aplicação ou fornecedor de recursos que está a encriptar e desencriptar um bloco específico. Quando um DEK é substituída por uma nova chave apenas os dados no respetivo bloco associado tem de ser encriptados novamente com a nova chave.
- **Chave de encriptação de chaves (KEK)** – uma chave de encriptação assimétrico utilizada para encriptar as chaves de encriptação de dados. Utilização de uma chave de encriptação de chave permite que as chaves de encriptação de dados próprios sejam encriptados e controlado. A entidade que tem acesso para a KEK pode ser diferente de entidade que requer o DEK. Isto permite uma entidade de Mediador de acesso para o DEK para fins de garantir o acesso limitado de cada DEK a partição específica. Uma vez que a KEK é necessário para desencriptar os DEKs, a KEK é efetivamente um ponto único através do qual DEKs podem ser efetivamente eliminados por eliminação da KEK.

As chaves de encriptação de dados encriptados com as chaves de encriptação de chave são armazenadas em separado e apenas uma entidade com acesso à chave de encriptação de chave pode obter as chaves de encriptação de dados encriptados com essa chave. São suportados modelos diferentes, de armazenamento de chaves. Vamos abordar cada modelo em mais detalhe posteriormente na secção seguinte.

## <a name="data-encryption-models"></a>Modelos de encriptação de dados

Noções sobre vários modelos de encriptação e os respetivos os profissionais de TI e contras é essencial para compreender a forma como os vários fornecedores de recursos no Azure implementam a encriptação de inativos. Estas definições são partilhadas entre todos os fornecedores de recursos no Azure para garantir que o idioma e taxonomia comuns. 

Existem três cenários de encriptação do lado do servidor:

- Encriptação do lado do servidor utilizando as chaves de serviço geridas
    - Fornecedores de recursos do Azure efetuarem as operações de encriptação e desencriptação
    - Microsoft gere as chaves
    - Funcionalidade completa de nuvem

- Encriptação do lado do servidor utilizando as chaves gerida pelo cliente no Cofre de chaves do Azure
    - Fornecedores de recursos do Azure efetuarem as operações de encriptação e desencriptação
    - Cliente controla chaves através do Cofre de chaves do Azure
    - Funcionalidade completa de nuvem

- Utilizar chaves gerida pelo cliente no hardware controlados pelo cliente de encriptação do lado do servidor
    - Fornecedores de recursos do Azure efetuarem as operações de encriptação e desencriptação
    - Cliente controla as chaves de hardware controlados pelo cliente
    - Funcionalidade completa de nuvem

Para a encriptação do lado do cliente, considere o seguinte:

- Serviços do Azure não podem ver dados desencriptados
- Os clientes gerir e armazenam as chaves no local (ou noutras secure arquivos). As chaves não estão disponíveis aos serviços do Azure
- Funcionalidade reduzida de nuvem

Os modelos de encriptação suportados no Azure dividida em dois grupos principais: "Encriptação de cliente" e "encriptação do lado do servidor" mencionado anteriormente. Tenha em atenção que, independente da encriptação no modelo de rest dos serviços do Azure, utilizados sempre Recomendamos a utilização de um transporte seguro, tais como TLS ou HTTPS. Por conseguinte, a encriptação no transporte deve ser resolvida pelo protocolo de transporte e não deve ser um fator principais determinar qual o modelo de rest para utilizar a encriptação.

### <a name="client-encryption-model"></a>Modelo de encriptação do cliente

Modelo de encriptação do cliente refere-se a encriptação que é executada fora do fornecedor de recursos ou do Azure, o serviço ou aplicação de chamada. Pode ser efetuada a encriptação, a aplicação de serviço no Azure ou uma aplicação em execução no Centro de dados de cliente. Em ambos os casos, ao tirar partido deste modelo de encriptação, o fornecedor de recursos do Azure, receber um blob encriptado dos dados sem a capacidade para desencriptar os dados de qualquer forma ou ter acesso às chaves de encriptação. Neste modelo, a gestão de chaves é feita à chamar/aplicação de serviço e é opaco para o serviço do Azure.

![Cliente](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Modelo de encriptação do lado do servidor

Modelos de encriptação do lado do servidor referir-se a encriptação é efetuada pelo serviço do Azure. No modelo de que o fornecedor de recursos executa as operações de encriptar e desencriptar. Por exemplo, o Storage do Azure pode receber dados em operações de texto simples e irá efetuar a encriptação e desencriptação internamente. O fornecedor de recursos poderá utilizar chaves de encriptação que são geridas pela Microsoft ou pelo cliente, consoante a configuração fornecida. 

![Servidor](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Modelos de gestão de chaves de encriptação do lado do servidor

Cada um dos modelos de rest a encriptação da lado do servidor implica distinctive características da gestão de chaves. Isto inclui onde e como as chaves de encriptação são criadas e armazenadas, bem como os modelos de acesso e os procedimentos de rotação da chave. 

#### <a name="server-side-encryption-using-service-managed-keys"></a>Com o serviço gerido chaves de encriptação do lado do servidor

Para muitos clientes, o requisito de essencial é Certifique-se de que os dados são encriptados sempre que estejam inativos. Utilizando o serviço gerido chaves de encriptação do lado do servidor permite que este modelo, permitindo que os clientes marcar o recurso específico (conta de armazenamento, base de dados SQL, etc.) para a encriptação e deixando todos os aspetos de gestão de chaves, tais como a emissão de chave, rotação e cópia de segurança para a Microsoft. Suporta a maioria dos serviços do Azure que suportam encriptação de inativos, normalmente, este modelo de descarga a gestão de chaves de encriptação para o Azure. O fornecedor de recursos do Azure cria as chaves, coloca-os no armazenamento seguro e obtém-las quando necessário. Isto significa que o serviço tem acesso total para as chaves e o serviço tem controlo total sobre a gestão de ciclo de vida de credencial.

![Gerido](./media/azure-security-encryption-atrest/azure-security-encryption-atrest-fig4.png)

Encriptação do lado do servidor a utilizar chaves de serviço gerida, por conseguinte, rapidamente aborda a necessidade de ter a encriptação Inativos com baixo overhead ao cliente. Se estiver disponível um cliente é normalmente abre o portal do Azure para a subscrição de destino e o fornecedor de recursos e verifica uma caixa com a indicação de que pretende que os dados sejam encriptados. Em alguns gestores de recursos a encriptação do lado do servidor com o serviço gerido chaves está ativada por predefinição. 

Encriptação do lado do servidor com chaves gerida pela Microsoft implica o serviço tem acesso total para armazenar e gere as chaves. Embora alguns clientes poderão querer gerir as chaves porque estes sentir que podem garantir uma maior segurança, o custo e os riscos associados uma solução de armazenamento de chaves devem ser considerados ao avaliar este modelo. Em muitos casos, uma organização pode determinar que riscos de uma solução no local ou restrições de recurso podem maior do que o risco de gestão de nuvem de encriptação de chaves de rest.  No entanto, este modelo poderá não ser suficiente para as organizações que têm requisitos para controlar a criação ou o ciclo de vida das chaves de encriptação ou ter diferentes técnico gerir chaves de encriptação de um serviço que são gerir o serviço (ou seja, segregação de gestão de chaves do modelo global de gestão para o serviço).

##### <a name="key-access"></a>Acesso a chaves

Ao utilizar a encriptação do lado do servidor com o serviço gerido chaves, a criação de chave, armazenamento e serviço de acesso são todos os geridos pelo serviço. Normalmente, os fornecedores de recursos do Azure fundamentais sobre irão armazenar as chaves de encriptação de dados num arquivo que está próximo os dados e rapidamente disponível e acessível às chaves de encriptação de chave são armazenados num arquivo seguro interno.

**Vantagens**

- Configuração simples
- Microsoft gere a rotação da chave, a cópia de segurança e a redundância
- Cliente não tem o custo associado à implementação ou o risco de um esquema de gestão de chaves personalizadas.

**Desvantagens**

- Não controla cliente as chaves de encriptação (especificação da chave, do ciclo de vida, revogação, etc.)
- Sem capacidade para segregar do modelo global de gestão para o serviço de gestão de chaves

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do lado do servidor utilizando o cliente chaves geridas no Cofre de chaves do Azure 

Para cenários em que o requisito é encriptar os dados em rest e controlo dos clientes de chaves de encriptação podem utilizar com o cliente gerido chaves no Cofre de chaves de encriptação do lado do servidor. Alguns serviços poderão armazenar apenas a chave de encriptação de chave de raiz no Cofre de chaves do Azure e armazenar a chave de encriptação de dados encriptado numa localização interna próximo aos dados. Em que os clientes de cenário podem colocar as suas próprias chaves para o Cofre de chave (BYOK – traga a sua própria chave), ou gerar novos e utilizá-los para encriptar os recursos pretendidos. Enquanto o fornecedor de recursos executa as operações de encriptação e desencriptação utiliza a chave configurada como a chave de raiz para todas as operações de encriptação. 

##### <a name="key-access"></a>Acesso a chaves

O modelo de encriptação do lado do servidor com o cliente gerido chaves no Cofre de chaves do Azure envolve o serviço de aceder às chaves de para encriptar e desencriptar conforme necessário. A encriptação chaves rest que são efetuados acessível a um serviço através de uma política de controlo de acesso. Esta política concede ao acesso de identidade de serviço para receberem a chave. Um serviço do Azure em execução em nome de uma subscrição associada pode ser configurado com uma identidade nessa subscrição. O serviço pode efetuar a autenticação do Azure Active Directory e receber um token de autenticação identificar-se automaticamente como esse serviço agir em nome da subscrição. Esse token, em seguida, pode ser apresentada ao Cofre de chaves para obter uma chave que foi indicado acesso a.

Para operações utilizando chaves de encriptação, uma identidade de serviço pode ser concedida acesso a qualquer uma das seguintes operações: desencriptar, encriptar, unwrapKey, wrapKey, certifique-se, iniciar sessão, obter, listar, atualizar, criar, importar, eliminar, cópia de segurança e restaurar.

Para obter uma chave para utilização num encriptar ou desencriptar dados Inativos a identidade de serviço que a instância de serviço do Gestor de recursos será executada como tem de ter UnwrapKey (para obter a chave de desencriptação) e WrapKey (para inserir uma chave de Cofre de chaves ao criar uma nova chave).


>[!NOTE] 
>Para mais detalhes sobre o Cofre de chaves autorização Consulte a segura página do seu Cofre de chaves no [documentação do Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). 

**Vantagens**

- Controlo total sobre as chaves utilizadas – encriptação de chaves são geridas no Cofre de chaves do cliente sob o controlo do cliente.
- Encriptar múltiplos serviços para um nó principal
- Pode segregar do modelo global de gestão para o serviço de gestão de chaves
- Pode definir o serviço e a localização da chave em regiões

**Desvantagens**

- O cliente tem responsabilidade completa de gestão de chaves de acesso
- O cliente tem responsabilidade completa de gestão do ciclo de vida das chave
- Overhead de configuração do & configuration adicional

#### <a name="server-side-encryption-using-service-managed-keys-in-customer-controlled-hardware"></a>Encriptação do lado do servidor com o serviço de chaves geridas no hardware de cliente controlado

Para cenários em que o requisito é encriptar dados inativos e gerir as chaves num repositório proprietária fora do controlo da Microsoft, alguns serviços do Azure permitem o modelo de gestão de chaves de anfitrião sua própria chave (HYOK). Neste modelo, o serviço tem de obter a chave de um site externo e, por conseguinte, as garantias de disponibilidade e desempenho são afetadas e configuração for mais complexa. Além disso, uma vez que o serviço tem acesso para o DEK durante as operações de encriptação e desencriptação de garantias de segurança geral deste modelo são semelhantes aos quando as chaves são cliente gerido no Cofre de chaves do Azure.  Como resultado, este modelo não é adequado para a maioria das organizações, a menos que têm requisitos de gestão de chaves específico, sendo necessárias-lo. Devido a estas limitações, a maioria dos serviços do Azure não suportam a encriptação do lado do servidor utilizando as chaves de servidor gerido no hardware de cliente controlado.

##### <a name="key-access"></a>Acesso a chaves

Quando é utilizada a encriptação do lado do servidor utilizando as chaves de serviço gerida no hardware de cliente controlado as chaves são mantidas num sistema configurado pelo cliente. Serviços do Azure que suportem este modelo fornecem um meio de estabelecer uma ligação segura para um cliente fornecido armazenamento de chaves.

**Vantagens**

- Controlo total sobre a chave de raiz utilizado – encriptação de chaves são geridas por um arquivo de cliente fornecido
- Encriptar múltiplos serviços para um nó principal
- Pode segregar do modelo global de gestão para o serviço de gestão de chaves
- Pode definir o serviço e a localização da chave em regiões

**Desvantagens**

- Responsabilidade completa para o armazenamento de chaves, segurança, desempenho e disponibilidade
- Responsabilidade completa de gestão de chaves de acesso
- Responsabilidade completa de gestão do ciclo de vida das chave
- Programa de configuração significativo, configuração e os custos de manutenção em curso
- Dependência de uma maior disponibilidade da rede entre o Centro de dados do cliente e os datacenters do Azure.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Encriptação de Inativos na cloud services da Microsoft

Serviços Cloud da Microsoft são utilizados em todos os modelos de nuvem três: IaaS, PaaS, SaaS. Abaixo, pode encontrar exemplos de como se ajustar a cada modelo:

- Serviços de software, referidos como Software como um servidor ou o SaaS, que têm aplicações fornecidas pela nuvem, como o Office 365.
- Serviços da plataforma que clientes tirar partido da nuvem nas respetivas aplicações, com a nuvem para coisas como a funcionalidade de barramento de armazenamento, análise e serviço.
- Serviços de infraestrutura ou de infraestrutura como serviço (IaaS) na qual cliente implementar sistemas operativos e aplicações que estão alojadas na nuvem e, possivelmente, tirar partido dos outros serviços em nuvem.

### <a name="encryption-at-rest-for-saas-customers"></a>Encriptação de Inativos para clientes de SaaS

Software como um clientes do serviço (SaaS) têm normalmente encriptação de inativos disponíveis em cada serviço ou ativada. Office 365 tem várias opções para os clientes verificar ou ativar a encriptação de inativos. Para obter informações sobre serviços do Office 365, consulte tecnologias de encriptação de dados para o Office 365.

### <a name="encryption-at-rest-for-paas-customers"></a>Encriptação de Inativos para clientes de PaaS

Plataforma como dados de um cliente de serviço (PaaS) normalmente reside num ambiente de execução da aplicação e os fornecedores de recursos do Azure utilizados para armazenar dados de cliente. Para ver a encriptação de Inativos opções disponíveis, examine a tabela abaixo para as plataformas de armazenamento e a aplicação que utiliza. Se suportado, são fornecidas hiperligações para obter instruções sobre como ativar a encriptação de Inativos para cada fornecedor de recursos. 

### <a name="encryption-at-rest-for-iaas-customers"></a>Encriptação de Inativos para clientes do IaaS

Infraestrutura como um clientes do serviço (IaaS) pode ter uma variedade de serviços e aplicações em utilização. Serviços de IaaS podem ativar a encriptação de Inativos no respetivo Azure alojada máquinas virtuais e VHDs utilizando a Azure Disk Encryption. 

#### <a name="encrypted-storage"></a>Armazenamento encriptado

Como PaaS, soluções de IaaS podem tirar partido outros serviços do Azure que armazenam dados encriptados em pausa. Nestes casos, pode ativar a encriptação no suporte de Rest conforme fornecida pelos cada serviço do Azure foram consumido. A tabela abaixo enumera o armazenamento principais, serviços e plataformas de aplicação e o modelo de encriptação de Inativos suportado. Se suportado, são fornecidas hiperligações para obter instruções sobre como ativar a encriptação de inativos. 

#### <a name="encrypted-compute"></a>Encriptados de computação

A encriptação completa uma solução de Rest requer que os dados nunca são continuados no formato não encriptado. Enquanto em utilização, num servidor de carregar os dados na memória, os dados podem ser persistente localmente de várias formas, incluindo o ficheiro de paginação do Windows, as informações de falhas e qualquer aplicação pode efetuar o registo. Para garantir que estes dados são encriptados em descanso aplicações de IaaS podem utilizar o Azure Disk Encryption uma máquina virtual de IaaS do Azure (Windows ou Linux) e o disco virtual. 

#### <a name="custom-encryption-at-rest"></a>Encriptação personalizado Inativos

Recomenda-se que sempre que possível, aplicações de IaaS tirar partido do Azure Disk Encryption e encriptação em Opções de Rest fornecidas por todos os serviços do Azure foram consumidos. Em alguns casos, tais como requisitos de encriptação de dados ou não do Azure com base em armazenamento, um programador de uma aplicação de IaaS poderá ter de implementar a encriptação rest próprios. Os programadores de IaaS soluções podem melhor integram expectativas de gestão e o cliente do Azure através do aproveitamento determinados componentes do Azure. Especificamente, os programadores devem utilizar o serviço Cofre de chaves do Azure para fornecer armazenamento de chaves seguro, bem como para fornecer os seus clientes com opções de gestão de chaves consistente com que mais Azure de serviços da plataforma. Além disso, soluções personalizadas devem utilizar identidades de serviço gerida do Azure para permitir contas de serviço aceder às chaves de encriptação. Para obter informações de Programador no Cofre de chaves do Azure e identidades de serviço geridas por ver os seus respetivos SDKs.

## <a name="azure-resource-providers-encryption-model-support"></a>Suporte de modelo de encriptação de fornecedores de recursos do Azure

Serviços do Microsoft Azure cada suporta um ou mais da encriptação em modelos de rest. Para alguns serviços, no entanto, um ou mais dos modelos de encriptação podem não ser aplicáveis. Além disso, os serviços podem de versão suporte para estes cenários em diferentes agendamentos. Esta secção descreve a encriptação no suporte de rest no momento desta redação para cada um dos serviços de armazenamento de dados do Azure principais.

### <a name="azure-disk-encryption"></a>Encriptação de disco do Azure

Qualquer cliente utilizando a infraestrutura do Azure como um serviço (IaaS) funcionalidades podem alcançar a encriptação de Inativos as respetivas VMs de IaaS e discos através do Azure Disk Encryption. Para obter mais informações sobre o disco do Azure encriptação Consulte o [documentação do Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

#### <a name="azure-storage"></a>Storage do Azure

Blob do Azure e ficheiro suporta encriptação de Inativos para cenários de encriptado do lado do servidor, bem como os dados de cliente encriptado (encriptação do lado do cliente).

- Lado do servidor: os clientes utilizam o armazenamento de Blobs do Azure podem ativar a encriptação de Inativos em cada conta de recursos de armazenamento do Azure. É efetuada uma vez ativada encriptação do lado do servidor transparente para a aplicação. Consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](https://docs.microsoft.com/azure/storage/storage-service-encryption) para obter mais informações.
- Do lado do cliente: a encriptação do lado do cliente de Blobs do Azure é suportada. Quando os clientes de encriptação do lado do cliente a utilizar encriptar os dados e carregar os dados como um blob encriptado. Gestão de chaves é feita pelo cliente. Consulte [encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) para obter mais informações.


#### <a name="sql-azure"></a>SQL Azure

Atualmente, o SQL Azure suporta encriptação de Inativos para cenários de encriptação do lado do cliente e do lado de serviço gerida pela Microsoft.

Servidor de suporte para encriptação atualmente é fornecida através da funcionalidade SQL denominada a encriptação transparente de dados. Depois de um cliente do SQL Azure permite chave TDE são automaticamente criados e geridos para os mesmos. Pode ser ativada a encriptação de Inativos nos níveis de base de dados e servidor. A partir de Junho de 2017, [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) será ativada por predefinição nas bases de dados recentemente criados.

Encriptação do lado do cliente de dados SQL Azure é suportada através de [sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx) funcionalidade. Sempre encriptado utiliza uma chave que criada e armazenada pelo cliente. Os clientes podem armazenar a chave mestra num arquivo de certificados do Windows, o Cofre de chaves do Azure ou um módulo de Hardware de segurança local. Utilizar o SQL Server Management Studio, os utilizadores do SQL Server escolhem que gostaria de utilizar para encriptar as colunas de chave.

|                                  |                |                     | **Modelo de encriptação**             |                              |        |
|----------------------------------|----------------|---------------------|------------------------------|------------------------------|--------|
|                                  |                |                     |                              |                              | **Cliente** |
|                                  | **Gestão de chaves** | **O serviço gerido chave** | **Cliente gerido no Cofre de chaves** | **Cliente gerido no local** |        |
| **Armazenamento e bases de dados**            |                |                     |                              |                              |        |
| Disco (IaaS)                      |                | -                   | Sim                          | Sim*                         | -      |
| SQL Server (IaaS)                |                | Sim                 | Sim                          | Sim                          | Sim    |
| Azure SQL (PaaS)                 |                | Sim                 | Pré-visualização                      | -                            | Sim    |
| Armazenamento do Azure (Blobs de blocos/páginas) |                | Sim                 | Pré-visualização                      | -                            | Sim    |
| Armazenamento do Azure (ficheiros)            |                | Sim                 | -                            | -                            | -      |
| Armazenamento do Azure (tabelas, filas)   |                | -                   | -                            | -                            | Sim    |
| Cosmos BD (documento DB)          |                | Sim                 | -                            | -                            | -      |
| StorSimple                       |                | Sim                 | -                            | -                            | Sim    |
| Cópia de segurança                           |                | -                   | -                            | -                            | Sim    |
| **Intelligence e análise**       |                |                     |                              |                              |        |
| Azure Data Factory               |                | Sim                 | -                            | -                            | -      |
| Azure Machine Learning           |                | -                   | Pré-visualização                      | -                            | -      |
| Azure Stream Analytics           |                | Sim                 | -                            | -                            | -      |
| HDInsights (armazenamento de Blobs do Azure)  |                | Sim                 | -                            | -                            | -      |
| HDInsights (armazenamento do Data Lake)   |                | Sim                 | -                            | -                            | -      |
| Azure Data Lake Store            |                | Sim                 | Sim                          | -                            | -      |
| Catálogo de Dados do Azure               |                | Sim                 | -                            | -                            | -      |
| Power BI                         |                | Sim                 | -                            | -                            | -      |
| **Serviços IoT**                     |                |                     |                              |                              |        |
| IoT Hub                          |                | -                   | -                            | -                            | Sim    |
| Service Bus                      |                | Sim (escalão Premium)              | -                            | -                            | Sim    |
| Event Hubs                       |                | Sim             | -                            | -                            | -      |


## <a name="conclusion"></a>Conclusão

Proteção de dados de cliente armazenados dentro de serviços do Azure é essencial da importância à Microsoft. Todos os Azure alojada serviços são consolidados para fornecer encriptação em Opções de Rest. Serviços de intelligence e serviços fundamentais sobre como o Storage do Azure, SQL Azure e análise de chave já fornecem a encriptação em Opções de Rest. Alguns destes serviços suportam chaves cliente controlada e de encriptação do lado do cliente, bem como chaves geridas e encriptação de serviço. Serviços do Microsoft Azure são amplamente Otimização da encriptação na disponibilidade de Rest e novas opções estejam planeadas para pré-visualização e disponibilidade geral nos meses futuros.

