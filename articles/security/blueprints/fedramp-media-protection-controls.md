---
title: "FedRAMP Azure Blueprint automatização - suporte de dados de proteção"
description: "Aplicações Web para FedRAMP - suporte de dados de proteção"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>Proteção de suporte de dados (MP)

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

## <a name="nist-800-53-control-mp-1"></a>Controlo NIST 800-53 MP-1

#### <a name="media-protection-policy-and-procedures"></a>Política de proteção do suporte de dados e de procedimentos

**Pacote de gestão 1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de proteção de suporte de dados que abrange o objetivo, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da política de proteção do suporte de dados e controlos de proteção de suporte de dados associada; analisa e atualiza a política de proteção de suporte de dados atual [atribuição: frequência definida de organização]; e procedimentos de proteção do suporte de dados [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de proteção do suporte de dados de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-mp-2"></a>Controlo NIST 800-53 MP-2

#### <a name="media-access"></a>Acesso de suporte de dados

**2 do pacote de gestão** organização restringe o acesso a [atribuição: definido de organização tipos de suportes de dados digitais e/ou não digital] para [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure tiver implementado o acesso de suporte de dados através da implementação da política de segurança do Microsoft. O acesso lógico para suporte de dados digitais é controlado através do grupo política objetos GPOs Active Directory (AD) e grupos de segurança. O acesso físico a todos os suportes de dados é restringido pelo processo de acesso de centro de dados. O acesso é restringido aos indivíduos que tenham um objetivo de legítima de negócios para aceder os dados. Consulte PE-3, controlo de acesso físico, para obter mais detalhes sobre os controlos de acesso do Centro de dados no local. O padrão de proteção ativo, define as salvaguardas necessárias para proteger a confidencialidade, integridade e disponibilidade dos recursos de informações nos centros de dados do Microsoft Azure. |


 ## <a name="nist-800-53-control-mp-3a"></a>Pacote de gestão-3.a do NIST 800-53 controlo

#### <a name="media-marking"></a>Marcação de suporte de dados

**Pacote de gestão 3.a** organização marca o suporte de dados do sistema de informações que indicam as limitações de distribuição, avisos e marcas de segurança aplicável (se aplicável) das informações de processamento.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure marca ativos dentro dos centros de dados do Microsoft com um HBI, MBI ou LBI designação (elevada, moderada ou pouco impacto sobre o negócio), que necessita de diferentes níveis de segurança e processamento precauções. Os proprietários de recursos são necessários para classificar os recursos que estão armazenados num centro de dados Microsoft. Consulte o padrão de classificação do recurso e Asset proteção padrão para obter mais informações. |


 ## <a name="nist-800-53-control-mp-3b"></a>Pacote de gestão-3.b do NIST 800-53 controlo

#### <a name="media-marking"></a>Marcação de suporte de dados

**MP 3.b** exempts a organização [atribuição: definido de organização tipos de suportes de dados de sistema de informações] marque, desde que o suporte de dados permanecem na [atribuição: definido de organização controlada áreas].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure requer os proprietários de recurso atribuir os respetivos recursos com uma classificação de recurso e não os ativos são excluídos este requisito. O ambiente de centro de dados da Microsoft, ativos referem-se a servidores, dispositivos de rede e bandas magnéticas. Outros suportes de dados digitais como USB flash/botão unidades, unidades de disco rígido externo amovível, ou CD/DVDs não são utilizados. Suporte de dados não digital não é utilizado no Centro de dados. |


 ## <a name="nist-800-53-control-mp-4a"></a>Pacote de gestão-4.a do NIST 800-53 controlo

#### <a name="media-storage"></a>Armazenamento de suporte de dados

**MP 4.a** organização controla fisicamente e armazena de forma segura [atribuição: definido de organização tipos de suportes de dados digitais e/ou não digital] dentro [atribuição: definido de organização controlada áreas].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure suporte de dados digitais estão fisicamente e segura armazenados os elementos no Centro de dados gabinetes de colocalização. Os datacenters da Microsoft tem várias camadas de controlos de acesso físico (destaque de acesso, biometria; Consulte PE 3 para obter mais detalhes em controlos de acesso físico) e vigilância de vídeo no local para fornecer armazenamento seguro. Inclui suporte de dados digital para servidores, dispositivos de rede e as bandas magnéticas utilizadas para cópia de segurança. Suporte de dados não digital não é utilizado no ambiente de centro de dados. |


 ## <a name="nist-800-53-control-mp-4b"></a>Pacote de gestão-4.b do NIST 800-53 controlo

#### <a name="media-storage"></a>Armazenamento de suporte de dados

**Pacote de gestão 4.b** a organização protege o suporte de dados de sistema de informações até que o suporte de dados serão destruídos ou utilizar sanitized aprovados equipamento, técnicas e procedimentos.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Recursos de suporte de dados digitais do Microsoft Azure são protegidos em colocations de centro de dados da Microsoft através de controlos de acesso físico (PE-3) e controlos de acesso lógico (IA-2) para a duração do elemento. Recursos do Microsoft Azure são limpa, removidos ou destruídos com métodos consistentes com SP de NIST 800-88 antes do disposal ativos. Para destruição de recurso, o Microsoft Azure utiliza serviços de destruição de recurso no local. |


 ## <a name="nist-800-53-control-mp-5a"></a>Pacote de gestão-5.a do NIST 800-53 controlo

#### <a name="media-transport"></a>Suporte de dados de transporte

**MP 5.a** organização protege e controla [atribuição: definido de organização tipos de suportes de dados de sistema de informações] durante o transporte fora áreas controladas utilizando [atribuição: as proteções de segurança definido para organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Suporte de dados digitais do Microsoft Azure em centros de dados do Microsoft consistir em servidores, dispositivos de rede e as bandas de cópia de segurança magnéticas e discos, apropriado. Os datacenters da Microsoft não utilize suportes de dados não digital. Microsoft utiliza três métodos para proteger os suportes de dados que estão a ser transportados fora do datacenter: 1) segura de transporte, 2) encriptação 3) limpar, remover ou destruir. |


 ## <a name="nist-800-53-control-mp-5b"></a>Pacote de gestão-5.b do NIST 800-53 controlo

#### <a name="media-transport"></a>Suporte de dados de transporte

**Pacote de gestão 5.b** organização mantém accountability para suporte de dados de sistema de informações durante o transporte fora áreas controladas.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure mantém accountability para recursos de abandonar o fileparser o Centro de dados através da utilização de documentação de orientação do SP do NIST 800-88: consistente limpeza/remoção, destruição de recurso, encriptação, inventário preciso, controlo e proteção de cadeia de custódia durante o transporte. |


 ## <a name="nist-800-53-control-mp-5c"></a>Pacote de gestão-5.c do NIST 800-53 controlo

#### <a name="media-transport"></a>Suporte de dados de transporte

**Pacote de gestão 5.c** organização documentos actividades associadas o transporte de suporte de dados de sistema de informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure mantém os registos de inventário antes de transporte, controlo e a proteção da cadeia de custódia durante o transporte asset limpeza/remoção, destruição de recurso, a receção ativos e validação de inventário depois de transporte. |


 ## <a name="nist-800-53-control-mp-5d"></a>Pacote de gestão-5.d do NIST 800-53 controlo

#### <a name="media-transport"></a>Suporte de dados de transporte

**Pacote de gestão 5.d** organização restringe as atividades associadas o transporte de suporte de dados de sistema de informações a empregados autorizados.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure restringe as atividades de transporte de elemento a empregados autorizados através da proteção da cadeia de custody. A utilização de bloqueios, seals prova de adulteração e a necessidade de validação de inventários de asset assegura que apenas a empregados autorizados estão envolvidos no transporte de elemento. |


 ### <a name="nist-800-53-control-mp-5-4"></a>Pacote de gestão do controlo NIST 800-53-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>Suporte de dados transporte | Proteção de criptografia

**Pacote de gestão-5 (4)** o sistema de informações implementa mecanismos criptográficos para proteger a confidencialidade e a integridade das informações armazenadas no suporte de dados digitais durante o transporte fora áreas controladas.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Serviço de proteção de dados (DPS) para gerir serviços de criptografia Microsoft Azure emprega chaves utilizando um FIPS 140-2 módulo de nível de encriptação validados 3 (cert #1694) e o HSM (cert #1178), para proteger os dados de AES 256 bits encriptados nas bandas magnéticas. |


 ## <a name="nist-800-53-control-mp-6a"></a>Pacote de gestão-6.a do NIST 800-53 controlo

#### <a name="media-sanitization"></a>Sanitização de suporte de dados

**MP 6.a** organização sanitizes [atribuição: suporte de dados de sistema de informações definidas pela organização] antes de disposal, versão fora do controlo organizacional ou para utilizar a reutilização de versão [atribuição: técnicas de sanitização definido de organização e procedimentos] em conformidade com as normas federais e organizacionais aplicáveis e as políticas.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure necessita de suporte de dados digitais no ambiente de centro de dados do Microsoft Azure para cleansed/removido utilizando ferramentas do Microsoft Azure aprovado e de forma consistente com SP do NIST 800-88, diretrizes para Sanitização de suporte de dados, antes de a ser reutilizado ou eliminado . Suporte de dados não digital não é utilizado pelo Microsoft Azure no ambiente de centro de dados. |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800-53 controlo MP-6. b

#### <a name="media-sanitization"></a>Sanitização de suporte de dados

**Pacote de gestão-6. b** a organização utiliza mecanismos de sanitização com a segurança e integridade proporcionais a categoria de segurança ou a classificação das informações.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure utiliza unidades de erasure de dados e os processos para limpar os/remoção de dados de forma consistente com SP do NIST 800-88 e que são proporcionais a classificação de recurso do Microsoft Azure do elemento. Para elementos necessidade de destruição, o Microsoft Azure utiliza serviços de destruição de recurso no local. |


 ### <a name="nist-800-53-control-mp-6-1"></a>Pacote de gestão do controlo NIST 800-53-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>Suporte de dados Sanitização | Reveja / aprovar / controlar / documentar / verificar

**Pacote de gestão-6 (1)** organização revê, aprova, monitoriza, documentos e verifica as ações de sanitização e disposal de suporte de dados.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure tem implementada procedimentos de sanitização de suporte de dados de acordo com as orientações SP do NIST 800-88 para padrão de classificação do recurso e padrão de proteção do recurso. Todos os elementos multimédia torção ou Eletrónicos é cleansed/removidos por seguintes especificações de 800 88 NIST SP em conformidade com a classificação de recurso do Azure. Azure utiliza unidades de erasure de dados de soluções de protocolo Alpine (EPS). EPS software suporta SP do NIST 800-88 requisitos para erasure de limpeza e limpeza/proteger. |


 ### <a name="nist-800-53-control-mp-6-2"></a>Pacote de gestão do controlo NIST 800-53-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>Suporte de dados Sanitização | Teste de equipamento

**Pacote de gestão-6 (2)** organização testa equipamento de sanitização e procedimentos [atribuição: frequência definida de organização] para verificar que o sanitização que se destinam a ser alcançada.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure utiliza unidades de erasure de dados e os processos para limpar os/remoção de dados de forma consistente com SP do NIST 800-88. A cada 180 dias, as operações de DCS testa as unidades de erasure de dados do Microsoft Azure e o processo para erasure. O teste, as operações de DCS verifica que o sanitização que se destinam a ser alcançada através de uma análise forense de unidades de disco rígido testadas para confirmar que os dados tem sido sanitized pelas unidades de erasure dados |


 ### <a name="nist-800-53-control-mp-6-3"></a>Pacote de gestão do controlo NIST 800-53-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>Suporte de dados Sanitização | Técnicas nondestructive

**Pacote de gestão-6 (3)** a organização aplica técnicas de sanitização nondestructive para dispositivos de armazenamento portátil antes de ligar esses dispositivos para o sistema de informações nas seguintes circunstâncias: [atribuição: definido de organização circunstâncias que requerem sanitização de dispositivos de armazenamento portátil].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure garante que os datacenters do Azure siga o procedimento de segurança do suporte de dados amovível no livro de executar serviços do Data Center e ferramentas para evitar a infeção do ambiente Government por software maligno nos dispositivos de armazenamento portátil. O procedimento Especifica que ser executadas com unidades USB antes de utilização no ambiente Government as seguintes ações: <br /> (1) formate as unidades USB, quando as unidades primeiro adquiridas do fornecedor, antes da utilização inicial ou quando a ser reutilizado para uma ferramenta diferente ou do fabricante. <br /> (2) qualquer unidade USB a ser utilizado numa área Government-designado para software maligno, antes de colocar a unidade para a área de análise. <br /> (3) depois de utilizar uma unidade dentro de uma área designado por Government, formate a unidade antes de deixarem a área. <br /> As ferramentas e o procedimento de segurança de suporte de dados amovível também requer que todos os botão perdido, rejeitados, roubado ou deslocados unidades nunca ser novamente introduzidas em centros de dados do Azure, mas em vez disso, ser cataloged e destruído. |


 ## <a name="nist-800-53-control-mp-7"></a>Controlo NIST 800-53 MP-7

#### <a name="media-use"></a>Utilização de suportes de dados

**MP de 7** a organização [seleção: restringe; proíbe] a utilização de [atribuição: definido de organização tipos de suportes de dados de sistema de informações] em [atribuição: sistemas informações definidas pela organização ou componentes do sistema] utilizando [atribuição: proteções de segurança definido para organização].

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure requer os proprietários de recurso atribuir os respetivos recursos com uma classificação de recurso e não os ativos são excluídos este requisito. Ambiente de centro de dados do Microsoft Azure, ativos referem-se aos servidores e dispositivos de rede. Outros suportes de dados digitais como unidades de botão/flash USB são geridas por políticas específicas e procedimentos que rege como esses dispositivos são geridos. CD/DVDs não são utilizados. Suporte de dados não digital não é utilizado no Centro de dados. A utilização de suportes de dados digitais em ambientes de centros de dados do Microsoft Azure é 24x7 monitorizados através de cobertura de CCTV. Para obter mais detalhes, consulte PE 06. |


 ### <a name="nist-800-53-control-mp-7-1"></a>Pacote de gestão do controlo NIST 800-53-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>Suporte de dados utilização | Proibir utilização sem proprietário

**Pacote de gestão-7 (1)** organização proíbe a utilização de dispositivos de armazenamento portátil nos sistemas de informações organizacionais, quando esses dispositivos não têm não existe nenhum proprietário de identificação.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Não há sem suporte de dados de cliente controlada no âmbito dos sistemas implementado no Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft restringe a utilização de suportes de dados amovível, gravável para suporte de dados que tenha sido aprovado explicitamente pela gestão de centros de dados através de ferramentas de DCS e o procedimento de suporte de dados amovível. Suporte de dados é pessoais ou não existe nenhum proprietário de identificação é proibida qualquer área de produção, conforme indicado no documento e regulamentos de regras de trabalho do Microsoft Datacenter. |
