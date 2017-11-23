---
title: "FedRAMP Blueprint da automatização do Azure - sistema e proteção de comunicações"
description: "Aplicações Web para FedRAMP - sistema e proteção de comunicações"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 02e2d07eb29d0d5d436afed1cdab4fe710674a8c
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="system-and-communications-protection-sc"></a>Proteção de comunicações (SC) e de sistema

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 controlo SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Sistema e de política de proteção de comunicações e de procedimentos

**SC 1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de proteção do sistema e as comunicações que objetivo, âmbito, funções, responsabilidades, gestão de endereços compromisso, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação do sistema e política de proteção de comunicações e sistema associado e controlos de proteção de comunicações; analisa e atualiza a política de proteção atual do sistema e comunicações [atribuição: frequência definida de organização]; e procedimentos de proteção do sistema e comunicações [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de proteção de sistema e as comunicações de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 controlo SC-2

#### <a name="application-partitioning"></a>A criação de partições de aplicação

**SC 2** o sistema de informações separa a funcionalidade de utilizador (incluindo os serviços de interface de utilizador) da funcionalidade de gestão do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure separa a funcionalidade de utilizador da funcionalidade de gestão do sistema através da imposição de controlos de acesso lógico e arquitetura de sistema. Funcionalidade de utilizador está limitada a interfaces de aplicação web implementada de cliente. Interfaces para a funcionalidade de gestão do sistema são separadas das interfaces de utilizador. Conectividade da gestão de todos os é através de um bastion segura anfitrião (jumpbox) localizado numa sub-rede gestão com regras de grupo de segurança de rede para limitar o acesso a recursos de produção, conforme apropriado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 controlo SC-3

#### <a name="security-function-isolation"></a>Isolamento de função de segurança

**SC 3** o sistema de informações isola as funções de segurança de funções de nonsecurity.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows mantém domínios de execução separado para cada processo em execução, atribuindo um espaço de endereço virtual privado a cada processo. Além disso, a implementa solução controlos de acesso e arquitetura concebida para isolar a funcionalidade de segurança sempre que necessário. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 controlo SC-4

#### <a name="information-in-shared-resources"></a>Informações de recursos partilhados

**SC 4** o sistema de informações impede a transferência não autorizado e não intencional informações através de recursos de sistema partilhado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. O sistema operativo gere os recursos (por exemplo, memória, armazenamento) que informações apenas esteja acessíveis aos utilizadores e funções com as permissões adequadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 controlo SC-5

#### <a name="denial-of-service-protection"></a>Recusa de serviço de proteção

**SC 5** o sistema de informações protege contra ou limita os efeitos dos seguintes tipos de ataques denial of service: [atribuição: tipos definidos de organização de recusa de serviço ataques ou referências a origens para essas informações] por empregar [atribuição: as proteções de segurança definido para organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um Gateway de aplicação que incluem uma firewall de aplicação web e as capacidades de balanceamento de carga. Máquinas virtuais implementadas, que suportam a camada web, a camada de base de dados e o Active Directory são implementadas num conjunto de disponibilidade dimensionáveis. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 controlo SC-6

#### <a name="resource-availability"></a>Disponibilidade de recursos

**SC 6** o sistema de informações protege a disponibilidade dos recursos atribuindo [atribuição: recursos definidos pelo organização] por [seleção (um ou mais) prioridade; quota; [Atribuição: as proteções de segurança definido para organização]].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Cada processo do Windows fornece os recursos necessários para executar um programa. Prioridade de recurso é gerida pelo sistema operativo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-7a"></a>Controlo SC-7.a do NIST 800-53

#### <a name="boundary-protection"></a>Proteção de limite

**SC 7.a** o sistema de informações monitores e controla a comunicação do limite do sistema externo e em limites internos chaves no sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um Gateway de aplicação, o Balanceador de carga e configura as regras de grupo de segurança de rede para controlar commutations limites externos e entre sub-redes internas. Gateway de aplicação, o Balanceador de carga e eventos de grupo de segurança de rede e os registos de diagnóstico são recolhidos através da análise de registos do OMS para permitir a monitorização de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 controlo SC-7

#### <a name="boundary-protection"></a>Proteção de limite

**7 de SC** o sistema de informações implementa sub-redes dos componentes de sistema acessível publicamente que são [seleção: fisicamente; logicamente] separada das redes organizacionais internas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa recursos uma arquitetura com uma sub-rede separada web, uma sub-rede de base de dados, sub-rede do Active Directory e sub-rede de gestão. Sub-redes logicamente são separados por regras de grupos de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes de forma a que só é necessário para a funcionalidade de gestão e de sistema (por exemplo, o tráfego externo não consegue aceder à base de dados, gestão, ou sub-redes do Active Directory). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-7c"></a>Controlo SC-7.c do NIST 800-53

#### <a name="boundary-protection"></a>Proteção de limite

**SC 7.c** o sistema de informações estabelece ligação a redes externas ou sistemas de informação apenas através de interfaces geridas consiste em dispositivos de proteção de limite dispostos de acordo com uma arquitetura de segurança organizacional.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um Gateway de aplicação para gerir as ligações externas para uma aplicação web implementada de cliente. Ligações externas para acesso de gestão estão limitadas a um anfitrião de bastion / jumpbox implementado uma sub-rede de gestão com regras de segurança de rede aplicadas para restringir ligações externas ao autorizado endereços IP. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-3"></a>SC de controlo do NIST 800-53-7 (3)

#### <a name="boundary-protection--access-points"></a>Proteção de limite | Pontos de acesso

**SC-7 (3)** organização limita o número de ligações de rede externa para o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa dois endereços IP públicos: um associadas ao Gateway de aplicação; um associado ao anfitrião de bastion gestão / jumpbox. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-4a"></a>.A do NIST 800-53 controlo SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externo

**SC-7 (4) .a** organização implementa uma interface gerida para cada serviço telecommunication externo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa dois endereços IP públicos: um associadas ao Gateway de aplicação; um associado ao anfitrião de bastion gestão / jumpbox. A gestão destas interfaces é ativada através de redes definidas por software. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-4b"></a>.B do NIST 800-53 controlo SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externo

**SC-7 (4) .b** organização estabelece uma política de fluxo de tráfego para cada interface gerida.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa dois endereços IP públicos: um associadas ao Gateway de aplicação; um associado ao anfitrião de bastion gestão / jumpbox. A gestão destas interfaces é ativada através de redes definidas por software. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-4c"></a>.C do NIST 800-53 controlo SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externo

**SC-7 (4) .c** a organização protege a integridade das informações que está a ser transmitidas em cada interface e confidencialidade.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O gateway de aplicação web implementado por esta Blueprint Azure está configurado com um HTTPS serviço de escuta, confidencialidade ensuing e a integridade das sessões de comunicações. Ligações de ambiente de trabalho remotas para o jumpbox também são encriptadas fornecer confidencialidade e integridade. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-4d"></a>.D do NIST 800-53 controlo SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externo

**SC-7 (4) .d** organização documentos cada exceção para a política de fluxo de tráfego com um suporte necessidade de missão/empresarial e a duração de que tem.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os clientes não são responsáveis por operações do Centro de dados (incluir serviços telecomunicações). Todos os serviços de telecommunication são fornecidos e geridos pelo Microsoft Azure. Este controlo é herdado a partir do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-4e"></a>.E do NIST 800-53 controlo SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Proteção de limite | Serviços de telecomunicações externo

**.E SC-7 (4)** A organização revê exceções para a política de fluxo de tráfego [atribuição: frequência definida de organização] e remove as exceções que já não são suportadas por uma necessidade de missão/empresarial explícita.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Os clientes não são responsáveis por operações do Centro de dados (incluir serviços telecomunicações). Todos os serviços de telecommunication são fornecidos e geridos pelo Microsoft Azure. Este controlo é herdado a partir do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-5"></a>SC de controlo do NIST 800-53-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Proteção de limite | Negar por predefinição / permitir a exceção

**SC-7 (5)** o sistema de informações em interfaces geridas nega o tráfego de rede de comunicações por predefinição e permite que o tráfego de comunicações de rede por exceção (ou seja, negar tudo, permitir a exceção).

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | RuleSets aplicados aos grupos de segurança implementados por esta Blueprint Azure de rede estão configuradas utilizando um esquema de negar por predefinição. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-7"></a>SC de controlo do NIST 800-53-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Proteção de limite | Impedir a divisão de túnel para dispositivos remotos

**SC-7 (7)** o sistema de informação, juntamente com um dispositivo remoto, impede o dispositivo em simultâneo estabelecer ligações não remoto com o sistema e comunicar através de alguns outra ligação aos recursos na redes externas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de configuração de nível empresarial remota do dispositivo do cliente pode resolver a divisão do túnel. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-8"></a>SC de controlo do NIST 800-53-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Proteção de limite | Encaminhar o tráfego para servidores de Proxy autenticado

**SC-7 (8)** as rotas de sistema de informações [atribuição: tráfego definido de organização comunicações internas] para [atribuição: definido de organização redes externas] através de servidores de proxy em interfaces geridas autenticado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é da responsabilidade para encaminhamento informações definidos pelo cliente através de um proxy autenticado uma rede externa. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-10"></a>SC de controlo do NIST 800-53-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Proteção de limite | Impedir Exfiltration não autorizado

**SC-7 (10)** organização impede o exfiltration não autorizado de informações através de interfaces geridas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por impedir exfiltration não autorizado de informações através de interfaces geridas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-12"></a>SC de controlo do NIST 800-53-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Proteção de limite | Proteção baseada no anfitrião

**SC-7 (12)** implementa a organização [atribuição: mecanismos de proteção de organização-definidos limites baseada no anfitrião] em [atribuição: os componentes do sistema de informações definidas pela organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implementadas por esta Blueprint do Azure estão configuradas com uma firewall baseada no anfitrião ativada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-13"></a>SC de controlo do NIST 800-53-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Proteção de limite | Isolamento de ferramentas de segurança / mecanismos / suporta componentes

**SC-7 (13)** isola a organização [atribuição: ferramentas de segurança de informações definidas pela organização, mecanismos e componentes de suporte] de outros componentes do sistema de informações internas implementando fisicamente separadas sub-redes com interfaces geridas para outros componentes do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa recursos uma arquitetura com uma sub-rede de gestão separados para a implementação de cliente de ferramentas de segurança de informações e suporte componentes. Sub-redes logicamente estão separadas por regras do grupo de segurança de rede. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-18"></a>SC de controlo do NIST 800-53-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Proteção de limite | Falha seguro

**SC-7 (18)** o sistema de informações com segurança falha na eventualidade de ocorrer uma falha de operacional de um dispositivo de proteção de limite.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo de proteção de limite físico no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure implementa geograficamente separada redundante servidores de Gateway e de VPN SSL. Quando um sistema de Gateway falha, falhar de forma segura e o acesso é restrito no ambiente. Para estabelecer uma ligação ao ambiente do Microsoft Azure, um utilizador tem de estabelecer uma ligação separada para um servidor de Gateway ativo gerido pelo Microsoft Azure. <br /> Além disso, se falharem a dispositivos de rede do Microsoft Azure (incluindo routers de limite, routers de acesso, balanceadores de carga, comutadores de agregação e TORS), o circuito afetado fica desligado, assim falhar de forma segura. Não pode levar a uma falha de um dispositivo de rede do Microsoft Azure ou fazer com que as informações externas para o sistema para introduzir o dispositivo, nem pode permitir a uma falha de versão não autorizados de informações. Redundância incorporada permite que os recursos do Microsoft Azure para ativação sem afetar a disponibilidade. |


 ### <a name="nist-800-53-control-sc-7-20"></a>SC de controlo do NIST 800-53-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Proteção de limite | Isolamento dinâmico / segregação

**SC-7 (20)** o sistema de informações fornece a capacidade de forma dinâmica isolar/segregar [atribuição: os componentes do sistema de informações definidas pela organização] provenientes de outros componentes do sistema.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por assegurar que o sistema tem a capacidade de isolar dinamicamente recursos de implementação de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-7-21"></a>SC de controlo do NIST 800-53-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Proteção de limite | Isolamento de componentes do sistema de informações

**SC-7 (21)** a organização utiliza mecanismos de proteção de limites para separar [atribuição: os componentes do sistema de informações definidas pela organização] suporte [atribuição: missions definido de organização e/ou funções de negócio].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa recursos uma arquitetura com uma sub-rede separada web, uma sub-rede de base de dados, sub-rede do Active Directory e sub-rede de gestão. Sub-redes logicamente estão separadas por regras de grupos de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes de forma a que só é necessário para a funcionalidade de gestão e de sistema. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 controlo SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Confidencialidade de transmissão e integridade

**SC 8** protege o sistema de informações a [seleção (um ou mais): confidencialidade; integridade] informações transmitidos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Implementação (1) 8 TAMA satisfaça este requisito de controlo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-8-1"></a>SC de controlo do NIST 800-53-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Confidencialidade de transmissão e integridade | Proteção física criptográfica ou alternativa

**SC-8 (1)** mecanismos criptográficos para implementa o sistema de informações [seleção (um ou mais): impedir a divulgação não autorizada de informações; detetar alterações às informações] durante a transmissão, a menos que caso contrário, protegidos por [atribuição: definido pelo organização alternativas físicas salvaguardas].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure configura recursos para comunicar utilizando apenas seguros protocolos. O componente de WAF do Gateway de aplicação está configurado para aceitar communicators de utiliza externa através de HTTPS/TLS e comunicar com o conjunto de back-end apenas através de HTTPS/TLS. SQL Server está configurado para comunicar apenas através de HTTPS/TLS. Os serviços de ambiente de trabalho remoto estão configurados para utilizar ligações seguras. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-10"></a>10 do NIST 800-53 controlo SC

#### <a name="network-disconnect"></a>Rede se desligar

**SC 10** o sistema de informações termina a ligação de rede associada a uma sessão de comunicações no fim da sessão ou após [atribuição: organização definidos pelo período de tempo] de inatividade.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Autenticação de sessões de ambiente de trabalho remoto é gerida pelo Active Directory. Depois do acesso é desativado para um utilizador no Active Directory, sessões remotas imediatamente foi terminadas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 controlo SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Estabelecimento da chave criptográfico e gestão

**SC-12** organização estabelece e gere chaves criptográficas para criptografia necessária, empregue se encontram dentro do sistema em conformidade com as informações [atribuição: definido de organização requisitos para geração de chaves, distribuição, o armazenamento, acesso e destruição].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um cofre de chaves do Azure. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. O Cofre de chaves do Azure pode gerar chaves utilizando um FIPS 140-2 capacidade de geração de chaves de nível 2 hardware segurança (HSM) do módulo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-12-1"></a>SC de controlo do NIST 800-53-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Estabelecimento da chave criptográfico e gestão | Disponibilidade

**(1) de SC-12** organização mantém a disponibilidade das informações em caso de perda de chaves criptográficas pelos utilizadores.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O Cofre de chaves do Azure é utilizado para armazenar as chaves criptográficas e segredos utilizados neste Blueprint do Azure. Chave de cofre simplifica o processo de gestão de chaves para chaves que acede e encripta os dados. As seguintes autenticadores são armazenados no Cofre de chaves: palavra-passe do Azure para a conta de implementar, a palavra-passe de administrador de máquina virtual, a palavra-passe de conta de serviço do SQL Server. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-12-2"></a>SC de controlo do NIST 800-53-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Estabelecimento da chave criptográfico e gestão | Chaves simétricas

**SC-12 (2)** organização produz, controla e distribui as chaves criptográficas simétricas utilizando [seleção: NIST compatíveis com FIPS; Tecnologia de gestão de chaves e aprovada] e processos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O Cofre de chaves do Azure é utilizado para produzir, controlar e distribuir as chaves criptográficas. O Cofre de chaves do Azure pode gerar chaves utilizando um FIPS 140-2 capacidade de geração de chaves de nível 2 hardware segurança (HSM) do módulo. As chaves são armazenadas e geridas dentro de forma segura encriptadas contentores no Cofre de chaves do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-12-3"></a>SC de controlo do NIST 800-53-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Estabelecimento da chave criptográfico e gestão | Chaves assimétricas

**SC-12 (3)** organização produz, controla e distribui as chaves criptográficas assimétricas utilizando [seleção: tecnologia de gestão de chaves e aprovado e processos; aprovados certificados de PKI classe 3 ou material para chaves prepositioned; aprovou certificados PKI classe 3 ou 4 de classe e tokens de segurança de hardware que proteger a chave privada do utilizador].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por produzir, controlar e distribuir as chaves criptográficas assimétricas (se estes são utilizados nos recursos de implementação de cliente). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-13"></a>Controlo SC-13 do NIST 800-53

#### <a name="cryptographic-protection"></a>Proteção de criptografia

**SC 13** implementa o sistema de informações [atribuição: definido de organização utiliza serviços de criptografia e tipo de criptografia necessário para cada utilização] em conformidade com as leis federal aplicáveis, as ordens executivo, as diretivas, políticas, normas, e normas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Autenticação do Windows, o ambiente de trabalho remoto e o BitLocker são utilizada por esta Blueprint do Azure. Estes componentes podem ser configurados confiar nos módulos criptográficos FIPS 140 validada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-15a"></a>Controlo SC-15.a do NIST 800-53

#### <a name="collaborative-computing-devices"></a>Dispositivos de informática colaboração

**SC 15.a** o sistema de informações proíbe ativação remota de dispositivos de informáticas colaboração com as seguintes exceções: [atribuição: definido de organização exceções onde ativação remota é permitida].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo de informático colaboração implementado como parte deste Blueprint do Azure. Nota: Existem dispositivos físicos informáticos colaboração dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-15b"></a>Controlo SC-15.b do NIST 800-53

#### <a name="collaborative-computing-devices"></a>Dispositivos de informática colaboração

**SC 15.b** o sistema de informações fornece uma indicação explícita de utilização aos utilizadores fisicamente presentes em dispositivos.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhum dispositivo de informático colaboração implementado como parte deste Blueprint do Azure. Nota: Existem dispositivos físicos informáticos colaboração dentro do âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-17"></a>Controlo SC-17 do NIST 800-53

#### <a name="public-key-infrastructure-certificates"></a>Certificados de infraestrutura de chaves públicas

**17 de SC** organização emite certificados de chaves públicos num [atribuição: política de certificado definidos pelo organização] ou obtém certificados de chaves públicas de um fornecedor de serviços aprovados.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de uma nível empresarial de infraestrutura de chaves públicas para emissão de certificados. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-18a"></a>Controlo SC-18.a do NIST 800-53

#### <a name="mobile-code"></a>Código móvel

**SC 18.a** organização define aceitável e inaceitável código móvel e as tecnologias de código móvel.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Sistema de nível empresarial do cliente e procedimentos de proteção de comunicações, podem definir aceitável e inaceitável código móvel. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-18b"></a>Controlo SC-18.b do NIST 800-53

#### <a name="mobile-code"></a>Código móvel

**SC 18.b** organização estabelece as restrições de utilização e as diretrizes de implementação para as tecnologias de código móvel e de código móvel aceitável.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Sistema de nível empresarial do cliente e procedimentos de proteção de comunicações, podem estabelecer restrições sobre a utilização de código móvel. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-18c"></a>Controlo SC-18.c do NIST 800-53

#### <a name="mobile-code"></a>Código móvel

**SC 18.c** organização autoriza, monitoriza e controla a utilização de código móvel dentro do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem de um processo de nível empresarial para autorização, monitorização e controlo sobre a utilização de código móvel. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-19a"></a>Controlo SC-19.a do NIST 800-53

#### <a name="voice-over-internet-protocol"></a>Voz através do protocolo de Internet

**SC 19.a** organização estabelece as restrições de utilização e as diretrizes de implementação para voz através de tecnologias de Internet Protocol (VoIP) com base no potencial de causar danos no sistema de informações se utilizado de forma maliciosa.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Existem não existe qualquer voz através de tecnologias de protocolo de internet implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-19b"></a>Controlo SC-19.b do NIST 800-53

#### <a name="voice-over-internet-protocol"></a>Voz através do protocolo de Internet

**SC 19.b** organização autoriza, monitoriza e controla a utilização de VoIP dentro do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Existem não existe qualquer voz através de tecnologias de protocolo de internet implementadas como parte deste Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-20a"></a>Controlo SC-20.a do NIST 800-53

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Nome seguro / endereço do serviço de resolução (origem autoritária)

**SC 20.a** o sistema de informações fornece origem de dados adicionais artefactos de verificação de autenticação e integridade juntamente com os dados de resolução de nome autoritativo devolve o sistema em resposta a consultas de resolução de nome/endereço externo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por um serviço de resolução de nome e endereço seguro. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-20b"></a>Controlo SC-20.b do NIST 800-53

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Nome seguro / endereço do serviço de resolução (origem autoritária)

**SC 20.b** o sistema de informações fornece os meios para indicar o estado de segurança das zonas subordinadas e (se o subordinado suporta serviços de resolução segura) para ativar a verificação de uma cadeia de fidedignidade entre domínios principais e subordinados, quando funciona como parte de um espaço de nomes hierárquico, distribuído.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por um serviço de resolução de nome e endereço seguro. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-21"></a>Controlo SC-21 do NIST 800-53

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Nome seguro / endereço do serviço de resolução (recursivo ou de resolução de colocação em cache)

**SC-21** o sistema de informações de pedidos e efetua a autenticação de origem de dados e a verificação da integridade dos dados nas respostas de resolução de nome/endereço recebe o sistema de origens autoritativas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela configuração recursos implementados de cliente para pedir e efetuar a autenticação de origem de dados e a verificação da integridade dos dados em respostas de resolução de nome/endereço recebidas das origens de autoritativas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-22"></a>Controlo SC-22 do NIST 800-53

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Arquitetura e o aprovisionamento de nome / endereço do serviço de resolução

**22 de SC** os sistemas de informações que se encontram coletivamente fornecem o serviço de resolução de nome/endereço de uma organização estão com tolerância a falhas e implementar a separação de funções interna/externa.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por assegurar que os sistemas que fornece endereços serviços de resolução para os recursos implementados de cliente são com tolerância a falhas e implementam a separação de funções interna/externa. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-23"></a>Controlo SC-23 do NIST 800-53

#### <a name="session-authenticity"></a>Autenticidade de sessão

**SC 23** o sistema de informações protege a autenticidade das sessões de comunicações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso remoto aos recursos implementado por esta Blueprint do Azure, incluindo o portal do Azure, a ligação ao ambiente de trabalho remoto e o gateway de aplicação web, são protegidas através de TLS. TLS fornece autenticidade para comunicações de nível de sessão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-23-1"></a>SC de controlo do NIST 800-53-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Sessão autenticidade | Invalidar identificadores de sessão a fim de sessão

**(1) de SC-23** o sistema de informações invalida identificadores de sessão após a fim de sessão do utilizador ou outro término de sessão.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso remoto aos recursos implementado por esta Blueprint do Azure, incluindo o portal do Azure, a ligação ao ambiente de trabalho remoto e o gateway de aplicação web, são protegidas através de TLS. Portal do Azure e sessões de ambiente de trabalho remotas invalidar identificadores de sessão após a fim de sessão. Invalidação de sessão Web é imposta através do Gateway de aplicação do Azure - as regras de Firewall de aplicação Web (WAF). O WAF aplica-se a afinidade de cookie por sessão e efetua o tempo limite da sessão após 30 minutos (implementação post configurável de regras específicas de organização) de inatividade do cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 controlo SC-24

#### <a name="fail-in-known-state"></a>Falha no estado conhecido

**SC-24** o sistema de informações não consegue uma [atribuição: conhecido-estado definido pelo organização] para [atribuição: tipos definidos de organização de falhas de] preservam [atribuição: informações de estado do sistema definida de organização] falha.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por assegurar que os recursos implementados de cliente falhar num estado conhecido. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-28"></a>Controlo SC-28 do NIST 800-53

#### <a name="protection-of-information-at-rest"></a>Proteção de informações Inativos

**28 de SC** protege o sistema de informações a [seleção (um ou mais): confidencialidade; integridade] de [atribuição: definido de organização informações Inativos].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Implementação (1) SC 28 satisfaça este requisito de controlo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-sc-28-1"></a>SC de controlo do NIST 800-53-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Proteção de informações Inativos | Proteção de criptografia

**SC-28 (1)** o sistema de informações implementa mecanismos criptográficos para impedir a divulgação não autorizada e modificação de [atribuição: informações definidas pela organização] em [atribuição: sistema informações definidas pela organização componentes].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Máquinas virtuais implementadas por esta Blueprint Azure implementar a encriptação de disco para proteger a confidencialidade e a integridade das informações inativos. Encriptação de disco do Azure para Windows é implementada utilizando a funcionalidade de BitLocker do Windows. SQL Server está configurado para utilizar transparente dados encriptação (TDE), que efetua a encriptação em tempo real e a desencriptação de dados e ficheiros para proteger informações inativos de registo. TDE fornece assurance que armazenados os dados não foi sujeito acesso não autorizado. Cliente pode optar por implementar controlos de nível de aplicação adicionais para proteger a integridade das informações armazenadas. Confidencialidade e integridade dos todos os blobs de armazenamento implementado por esta Blueprint do Azure estão protegidos através da utilização de encriptação de serviço de armazenamento do Azure (SSE). SSE salvaguarda dados Inativos dentro de contas do storage do Azure através da encriptação AES de 256 bits. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-sc-39"></a>Controlo SC-39 do NIST 800-53

#### <a name="process-isolation"></a>Isolamento de processo

**SC-39** o sistema de informações mantém um domínio de execução separado para cada processo em execução.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As máquinas virtuais implementadas por esta Blueprint Azure executar sistemas operativos Windows. Windows mantém domínios de execução separado para cada processo em execução, atribuindo um espaço de endereço virtual privado a cada processo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |
