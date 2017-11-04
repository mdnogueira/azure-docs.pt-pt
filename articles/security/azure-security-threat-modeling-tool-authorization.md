---
title: "Azure de autorização - ferramenta de modelação de ameaça Microsoft - | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 312a66544a5e64daa86b4902b57d4050f1f66af5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-authorization--mitigations"></a>Moldura de segurança: Autorização | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo](#acl-restricted-access)</li><li>[Certifique-se de que o conteúdo da aplicação de utilizadores específicos confidenciais é armazenado no diretório de perfil de utilizador](#sensitive-directory)</li><li>[Certifique-se de que as aplicações implementadas são executadas com menos privilégios](#deployed-privileges)</li></ul> |
| **Aplicação Web** | <ul><li>[Impor ordem sequencial passo durante o processamento de fluxos de lógica de negócio](#sequential-logic)</li><li>[Implementar a taxa de limitação de mecanismo para impedir a enumeração](#rate-enumeration)</li><li>[Certifique-se que autorização adequada no local e seguido do princípio de menos privilégios](#principle-least-privilege)</li><li>[Negócio lógica e os recursos acesso decisões de autorização não devem ser baseadas no parâmetros de entrada do pedido](#logic-request-parameters)</li><li>[Certifique-se esse conteúdo e de recursos não estão acessíveis através de navegação forçada ou enumeráveis](#enumerable-browsing)</li></ul> |
| **Base de Dados** | <ul><li>[Certifique-se de que o menor privilégio contas são utilizadas para ligar ao servidor de base de dados](#privileged-server)</li><li>[Implementar RLS de segurança de nível de linha para impedir que os inquilinos acedam uns dos outros dados](#rls-tenants)</li><li>[Função sysadmin deve ter apenas utilizadores necessários válidos](#sysadmin-users)</li></ul> |
| **Gateway de nuvem do IoT** | <ul><li>[Ligar ao Gateway de nuvem a utilização de tokens de menor privilégio](#cloud-least-privileged)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Utilize um permissões só de envio da chave SAS para gerar tokens de dispositivo](#sendonly-sas)</li><li>[Não utilize tokens de acesso que fornecem acesso direto para o Hub de eventos](#access-tokens-hub)</li><li>[Estabelecer ligação ao Hub de eventos utilizando chaves SAS que tem as permissões mínimas necessárias](#sas-minimum-permissions)</li></ul> |
| **Base de dados de documento do Azure** | <ul><li>[Utilize tokens de recursos para estabelecer ligação ao DocumentDB sempre que possível](#resource-docdb)</li></ul> |
| **Limite de fidedignidade do Azure** | <ul><li>[Ativar a gestão de acesso detalhado a subscrição do Azure através do RBAC](#grained-rbac)</li></ul> |
| **Limite de fidedignidade de recursos de infraestrutura de serviço** | <ul><li>[Restringir o acesso do cliente para operações de cluster através do RBAC](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Efetuar modelação de segurança e utilizar a segurança de nível do campo quando necessário](#modeling-field)</li></ul> |
| **Portal do Dynamics CRM** | <ul><li>[Efetuar modelação de segurança de contas portais manter em atenção que o modelo de segurança para o portal difere de acordo com o resto do CRM](#portal-security)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Conceder permissão de detalhado num intervalo de entidades no Table Storage do Azure](#permission-entities)</li><li>[Ativar baseada em funções controlo de acesso (RBAC) a conta de armazenamento do Azure utilizando o Azure Resource Manager](#rbac-azure-manager)</li></ul> |
| **Cliente para dispositivos móveis** | <ul><li>[Implementar implícita jailbreak ou rooting deteção](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Referência de classe fracos no WCF](#weak-class-wcf)</li><li>[Controlo de autorização de implementar WCF](#wcf-authz)</li></ul> |
| **API Web** | <ul><li>[Implementar o mecanismo de autorização adequado na API Web do ASP.NET](#authz-aspnet)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Executar verificações de autorização no dispositivo se suporta várias ações que necessitam de níveis de permissão diferentes](#device-permission)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Executar verificações de autorização no campo Gateway se suporta várias ações que necessitam de níveis de permissão diferentes](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que as ACLs adequadas estão configuradas para restringir o acesso não autorizado aos dados no dispositivo|

## <a id="sensitive-directory"></a>Certifique-se de que o conteúdo da aplicação de utilizadores específicos confidenciais é armazenado no diretório de perfil de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que o conteúdo da aplicação de utilizadores específicos confidenciais é armazenado no diretório de perfil de utilizador. Isto serve para impedir que vários utilizadores da máquina aceder aos dados entre si.|

## <a id="deployed-privileges"></a>Certifique-se de que as aplicações implementadas são executadas com menos privilégios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que a aplicação implementada for executada com menos privilégios. |

## <a id="sequential-logic"></a>Impor ordem sequencial passo durante o processamento de fluxos de lógica de negócio

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Para poder Certifique-se de que nesta fase foi percorrer por um utilizador genuíno que pretende impor apenas negócio lógica fluxos do processo por ordem sequencial passo, a aplicação com todos os passos que está a ser processados atempadamente humana realistas e não processar fora de ordem, ignorado passos , processados passos a partir de outro utilizador ou forma demasiado rápida submetido transações.|

## <a id="rate-enumeration"></a>Implementar a taxa de limitação de mecanismo para impedir a enumeração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que identificadores confidenciais aleatórios. Implementar o controlo de CAPTCHA nas páginas anónimos. Certifique-se de que exceções de erros e devem revelar a dados específicos|

## <a id="principle-least-privilege"></a>Certifique-se que autorização adequada no local e seguido do princípio de menos privilégios

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O princípio significa fornecer uma conta de utilizador apenas esses privilégios que são essenciais para que os utilizadores trabalham. Por exemplo, um utilizador de cópia de segurança não é necessário instalar o software: por conseguinte, o cópia de segurança utilizador tem direitos apenas para executar aplicações de cópia de segurança e relacionadas com a cópia de segurança. Outros privilégios, tais como a instalação de software novo, estão bloqueados. O princípio aplica-se também a um utilizador do computador pessoal que normalmente funciona numa conta de utilizador normal e abre-se uma conta de palavra-passe protegida (ou seja, um Superutilizador) com privilégios, apenas quando a situação absolutamente imperativas-lo. </p><p>Este princípio também pode ser aplicado às suas aplicações web. Em vez de apenas consoante os métodos de autenticação baseada em funções utilizando sessões, em vez disso queremos atribuir os privilégios aos utilizadores através de um sistema de autenticação baseada na base de dados. Vamos continuar a utilizar sessões para identificar se o utilizador foi registado no corretamente, apenas agora em vez de atribuir esse utilizador com uma função específica que é atribuir-lhe com privilégios para verificar as ações que ele é privilegiado para executar no sistema. Também uma grande pro deste método é, sempre que um utilizador tem de ser atribuídos menos privilégios que as alterações serão aplicadas no momento, uma vez que a atribuição não depende de sessão que caso contrário, tinha a expirar primeiro.</p>|

## <a id="logic-request-parameters"></a>Negócio lógica e os recursos acesso decisões de autorização não devem ser baseadas no parâmetros de entrada do pedido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Sempre que está a verificar se um utilizador está restrito a rever certos dados, as restrições de acesso devem ser processado-do lado do servidor. O ID de utilizador deve ser armazenado no interior de uma variável de sessão no início de sessão e deve ser utilizado para obter dados de utilizador da base de dados |

### <a name="example"></a>Exemplo
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Agora que um atacante possíveis não pode ser a adulteração e alterar a operação de aplicação, uma vez que é o identificador para obter os dados processados do lado do servidor.

## <a id="enumerable-browsing"></a>Certifique-se esse conteúdo e de recursos não estão acessíveis através de navegação forçada ou enumeráveis

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Ficheiros confidenciais estática e a configuração não devem ser mantidos na web-raiz. Para o conteúdo não é necessário para ser público, devem ser aplicados a qualquer um dos controlos de acesso corretos ou remoção de conteúdos propriamente ditos.</p><p>Além disso, o navegação forçada normalmente é combinado com técnicas de força bruta para recolher informações ao tentar aceder URLs tantas quanto possível enumerar os diretórios e ficheiros num servidor. Os atacantes podem procurar todas as variações do normalmente existente ficheiros. Por exemplo, uma pesquisa de ficheiros de palavra-passe seria abranger incluindo psswd.txt, password.htm, password.dat e outras variações de ficheiros.</p><p>Para atenuar isto, as capacidades de deteção de força bruta tentativas devem ser incluídas.</p>|

## <a id="privileged-server"></a>Certifique-se de que o menor privilégio contas são utilizadas para ligar ao servidor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hierarquia de permissões de base de dados SQL](https://msdn.microsoft.com/library/ms191465), [securables de base de dados do SQL Server](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Contas de menor privilégio devem ser utilizadas para ligar à base de dados. Início de sessão da aplicação deve ser restrita na base de dados e só deve executar procedimentos armazenados selecionados. Início de sessão da aplicação deve ter sem acesso de tabela direto. |

## <a id="rls-tenants"></a>Implementar RLS de segurança de nível de linha para impedir que os inquilinos acedam uns dos outros dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | MsSQL2016 de versão - V12, versão do SQL - SQL |
| **Referências**              | [Segurança do SQL Server ao nível da linha (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Passos** | <p>A Segurança ao Nível da Linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, associação a um grupo ou contexto de execução).</p><p>Segurança ao nível da linha (RLS) simplifica a programação de segurança na sua aplicação e a estrutura. O RLS permite-lhe implementar restrições ao acesso à linha de dados. É possível, por exemplo, garantir que os colaboradores só têm acesso às linhas de dados que são pertinentes para o departamento deles ou limitar o acesso a dados por parte de um cliente apenas àqueles que são relevantes para a empresa dele.</p><p>A lógica de restrição de acesso está localizado na camada de base de dados em vez de away dos dados na camada da aplicação em outro. O sistema de base de dados aplica-se as restrições de acesso sempre que esse acesso de dados é tentado a partir de qualquer camada. Isto faz com que o sistema de segurança mais fiável e robusta, reduzindo a área de superfície do sistema de segurança.</p><p>|

Tenha em atenção que RLS como uma funcionalidade de base de dados de out of box é aplicável apenas ao SQL Server a partir de 2016 e a SQL database do Azure. Se a funcionalidade RLS out of box não está implementada, este deve ser certificar-se de que o acesso a dados está restrito utilizar vistas e procedimentos

## <a id="sysadmin-users"></a>Função sysadmin deve ter apenas utilizadores necessários válidos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Hierarquia de permissões de base de dados SQL](https://msdn.microsoft.com/library/ms191465), [securables de base de dados do SQL Server](https://msdn.microsoft.com/library/ms190401) |
| **Passos** | Os membros da função de servidor fixa SysAdmin devem ser muito limitado e nunca conter contas utilizadas por aplicações.  Reveja a lista de utilizadores na função e remova quaisquer contas desnecessárias|

## <a id="cloud-least-privileged"></a>Ligar ao Gateway de nuvem a utilização de tokens de menor privilégio

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - IoT Hub do Azure |
| **Referências**              | [Controlo de acesso do IOT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Passos** | Fornece, pelo menos, permissões de privilégio para vários componentes que se ligam ao Gateway de nuvem (IoT Hub). Exemplo típico é – componente/aprovisionamento da gestão de dispositivos utiliza registryread/escrita, processadores de eventos (ASA) utiliza a ligação de serviço. Os dispositivos individuais estabelecer ligação com credenciais de dispositivo|

## <a id="sendonly-sas"></a>Utilize um permissões só de envio da chave SAS para gerar tokens de dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Event Hubs autenticação e segurança descrição geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Uma chave SAS é utilizada para gerar tokens de dispositivo individual. Utilizar uma chave SAS permissões só de envio ao gerar o token do dispositivo para um determinado publicador|

## <a id="access-tokens-hub"></a>Não utilize tokens de acesso que fornecem acesso direto para o Hub de eventos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Event Hubs autenticação e segurança descrição geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Um token que concede acesso direto para o hub de eventos não deve ser concedido ao dispositivo. Utilização de um token com menos privilégios para o dispositivo que permite o acesso apenas para um fabricante iria ajudar a identificar e a lista de proibições-lo se for um rogue ou fica comprometido dispositivo.|

## <a id="sas-minimum-permissions"></a>Estabelecer ligação ao Hub de eventos utilizando chaves SAS que tem as permissões mínimas necessárias

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Event Hubs autenticação e segurança descrição geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | Fornece, pelo menos, permissões de privilégio para várias aplicações de back-end que estabelecer ligação com o Hub de eventos. Gerar chaves SAS separadas para cada aplicação de back-end e fornecer apenas as permissões necessárias - enviar, Receive ou gerir aos mesmos.|

## <a id="resource-docdb"></a>Utilize tokens de recurso para ligar à base de dados do Cosmos sempre que possível

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de dados de documento do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Um token de recurso é associado a um recurso de permissão do DocumentDB e capture a relação entre o utilizador de uma base de dados e as permissões que esse utilizador tem de um recurso de aplicação DocumentDB específico (por exemplo, coleção, documentos). Utilize sempre um token de recurso para aceder do DocumentDB, se o cliente não pode ser fidedigno com processar chaves principais ou só de leitura - como uma aplicação do utilizador final, como um cliente de ambiente de trabalho ou móvel. Utilize a chave mestra ou chaves de só de leitura a partir de aplicações back-end que podem armazenar essas chaves de forma segura.|

## <a id="grained-rbac"></a>Ativar a gestão de acesso detalhado a subscrição do Azure através do RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Utilize atribuições de funções para gerir o acesso aos recursos de subscrição do Azure](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Passos** | O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode conceder apenas a quantidade de acesso precisa aos utilizadores para realizar os trabalhos.|

## <a id="cluster-rbac"></a>Restringir o acesso do cliente para operações de cluster através do RBAC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de recursos de infraestrutura de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Controlo de acesso baseado em funções para clientes de Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Passos** | <p>Recursos de infraestrutura de serviço do Azure suporta dois tipos de controlo de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e utilizador. Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais segura.</p><p>Os administradores têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, tem apenas acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.</p><p>Especifique as funções de cliente de dois (administrador e cliente) no momento da criação do cluster ao fornecer certificados separados para cada.</p>|

## <a id="modeling-field"></a>Efetuar modelação de segurança e utilizar a segurança de nível do campo quando necessário

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Efetuar modelação de segurança e utilizar a segurança de nível do campo quando necessário|

## <a id="portal-security"></a>Efetuar modelação de segurança de contas portais manter em atenção que o modelo de segurança para o portal difere de acordo com o resto do CRM

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Portal do Dynamics CRM | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Efetuar modelação de segurança de contas portais manter em atenção que o modelo de segurança para o portal difere de acordo com o resto do CRM|

## <a id="permission-entities"></a>Conceder permissão de detalhado num intervalo de entidades no Table Storage do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - tabela |
| **Referências**              | [Como delegar o acesso a objetos da sua conta do storage do Azure através da SAS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Passos** | Em certos cenários empresariais, o Table Storage do Azure poderá ser necessário para armazenar dados confidenciais que caters para entidades diferentes. Por exemplo, os dados confidenciais relativas a diferentes países. Nestes casos, podem ser construídas assinaturas SAS, especificando os intervalos de chaves partição e da fila, forma a que um utilizador pode aceder a dados específicos de um determinado país.| 

## <a id="rbac-azure-manager"></a>Ativar baseada em funções controlo de acesso (RBAC) a conta de armazenamento do Azure utilizando o Azure Resource Manager

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Passos** | <p>Quando cria uma nova conta de armazenamento, selecione um modelo de implementação de clássico ou do Azure Resource Manager. O modelo clássico de criação de recursos no Azure só permite all-or-nothing acesso à subscrição e, por sua vez, a conta de armazenamento.</p><p>Com o modelo Azure Resource Manager, coloque a conta de armazenamento no acesso de grupo e o controlo de recursos para o plane de gestão dessa conta de armazenamento específico utilizando o Azure Active Directory. Por exemplo, pode dar aos utilizadores específicos a capacidade de aceder as chaves de conta de armazenamento, enquanto outros utilizadores podem ver informações sobre a conta de armazenamento, mas não é possível aceder as chaves de conta de armazenamento.</p>|

## <a id="rooting-detection"></a>Implementar implícita jailbreak ou rooting deteção

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Cliente para dispositivos móveis | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Aplicação deve salvaguardar os seus próprios dados de configuração e o utilizador em caso se phone tem Root ou desbloqueado por jailbreak. Rooting/tiver jailbreak interrompendo implica acesso não autorizado, que os utilizadores normais não efetuar nos respetivos telefones. Por conseguinte aplicação deve ter a lógica de deteção implícita no arranque de aplicação, para detetar se o telemóvel tem Root.</p><p>A lógica de deteção pode ser simplesmente aceder a ficheiros que normalmente apenas raiz utilizador pode aceder ao, por exemplo:</p><ul><li>/System/App/SuperUser.apk</li><li>/sbin/Service su</li><li>/System/bin/su</li><li>/System/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/System/SD/xbin/su</li><li>/System/bin/FailSafe/su</li><li>/data/local/su</li></ul><p>Se a aplicação pode aceder a qualquer um destes ficheiros, indica que a aplicação está em execução como utilizador raiz.</p>|

## <a id="weak-class-wcf"></a>Referência de classe fracos no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | <p>O sistema utiliza uma referência de classe fracos, que poderá permitir que um atacante ao executar código não autorizado. O programa referencia uma classe definida pelo utilizador que não é identificada de forma exclusiva. Quando .NET carrega esta classe weakly identificado, o carregador do tipo CLR procura para a classe nas seguintes localizações ordem especificada:</p><ol><li>Se a assemblagem do tipo é conhecida, o carregador de procura de redirecionamento nas localizações do ficheiro de configuração, GAC, a assemblagem atual utilizando as informações de configuração e o diretório base da aplicação</li><li>Se a assemblagem for desconhecida, o carregador de pesquisa a assemblagem atual, mscorlib e a localização devolvido pelo processador de eventos TypeResolve</li><li>Esta ordem de pesquisa CLR, pode ser modificada com hooks, tais como o mecanismo de reencaminhamento de tipo e o evento AppDomain.TypeResolve</li></ol><p>Se um atacante exploits a ordem de pesquisa CLR através da criação de uma classe alternativa com o mesmo nome e a sua colocação numa localização alternativa que o CLR irá carregar o primeiro, o CLR irá inadvertidamente, execute o código fornecido pelo atacante</p>|

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração de WCF abaixo dá instruções ao WCF para adicionar uma classe de comportamento personalizados para uma determinada extensão WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Utilizar exclusivamente completamente qualificados de nomes (seguras) identifica um tipo e aumenta ainda mais a segurança do sistema. Utilize os nomes de assemblagem completamente qualificado ao registar tipos nos ficheiros de Machine. config e App. config.

### <a name="example"></a>Exemplo
O `<behaviorExtensions/>` elemento do ficheiro de configuração de WCF abaixo dá instruções ao WCF para adicionar classe de vivamente referenciado comportamento personalizados para uma determinada extensão WCF.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>Controlo de autorização de implementar WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | <p>Este serviço não utiliza um controlo de autorização. Quando um cliente chama um determinado serviço WCF, WCF fornece vários esquemas de autorização que certifique-se de que o chamador tem permissão para executar o método do serviço no servidor. Se os controlos de autorização não estão ativados para serviços WCF, um utilizador autenticado pode alcançar Escalamento de privilégios.</p>|

### <a name="example"></a>Exemplo
A seguinte configuração dá instruções ao WCF para não verificar o nível de autorização do cliente ao executar o serviço:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Utilize um esquema de autorização de serviço para verificar se o autor da chamada do método do serviço está autorizado a fazê-lo. WCF fornece dois modos e permite a definição de um esquema de autorização personalizadas. O modo de UseWindowsGroups utiliza funções do Windows e utilizadores e o modo de UseAspNetRoles utiliza um fornecedor de função ASP.NET, como o SQL Server, para se autenticar.

### <a name="example"></a>Exemplo
A seguinte configuração dá instruções ao WCF para se certificar de que o cliente faz parte do grupo Administradores antes de executar o serviço de adicionar:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
O serviço, em seguida, está declarado como o seguinte:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Implementar o mecanismo de autorização adequado na API Web do ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC5 |
| **Atributos**              | N/d, fornecedor de identidade do fornecedor - ADFS, de identidade - do Azure AD |
| **Referências**              | [Autenticação e autorização na API Web do ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Passos** | <p>Informações de função para os utilizadores da aplicação podem ser derivadas do Azure AD ou afirmações do ADFS se a aplicação depende-los como fornecedor de identidade ou poderá não é a própria aplicação fornecida-lo. Em qualquer um nestes casos, a implementação de autorização personalizada deve validar as informações de função de utilizador.</p><p>Informações de função para os utilizadores da aplicação podem ser derivadas do Azure AD ou afirmações do ADFS se a aplicação depende-los como fornecedor de identidade ou poderá não é a própria aplicação fornecida-lo. Em qualquer um nestes casos, a implementação de autorização personalizada deve validar as informações de função de utilizador.</p>

### <a name="example"></a>Exemplo
```C#
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Todos os controladores e métodos de ação que necessita de protegido que devem ser decorados com acima do atributo.
```C#
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Executar verificações de autorização no dispositivo se suporta várias ações que necessitam de níveis de permissão diferentes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>O dispositivo deve autorizar o autor da chamada para verificar se o autor da chamada tem as permissões necessárias para efetuar a ação pedida. Por exemplo, permite diga o dispositivo é uma porta de bloqueio do smart card que podem ser monitorizados a partir da nuvem, além fornece funcionalidades, como remotamente, bloquear a porta.</p><p>O bloqueio do smart card porta fornece uma funcionalidade de desbloqueio apenas quando alguém fisicamente vem quase a porta com um cartão. Neste caso, a implementação do comando remoto e controlo deve ser feita de forma a que não fornece qualquer funcionalidade para desbloquear a porta, como o gateway de nuvem não está autorizado a enviar um comando para desbloquear a porta.</p>|

## <a id="field-permission"></a>Executar verificações de autorização no campo Gateway se suporta várias ações que necessitam de níveis de permissão diferentes

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | O Gateway de campo deve autorizar o autor da chamada para verificar se o autor da chamada tem as permissões necessárias para efetuar a ação pedida. Para por exemplo, deve ser diferentes permissões para um utilizador interface/API de administrador utilizada para configurar um dispositivos de v/s de gateway de campo que se ligam ao mesmo.|
