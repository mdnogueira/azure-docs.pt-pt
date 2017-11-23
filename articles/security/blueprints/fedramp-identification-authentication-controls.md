---
title: "FedRAMP Blueprint da automatização do Azure - identificação e autenticação"
description: "Aplicações Web para FedRAMP - identificação e autenticação"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5ceac04f3f5c58ac5810dda6c99b40ebc8ad736f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="identification-and-authentication-ia"></a>Identificação e autenticação (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 controlo IA-1

> [!NOTE]
> Estes controlos são definidos pelo NIST e para os E.U.A. Departamento de comércio como parte da revisão a publicação especial do NIST 800-53 4. Consulte Rev 4 do NIST 800-53. para obter informações sobre testes de procedimentos e as diretrizes para cada controlo.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Identificação e a política de autenticação e procedimentos

**IA-1** organização desenvolvidas pela organização, documentos e disseminates para [atribuição: técnico definido pela organização ou funções] uma política de autenticação e identificação objetivo, âmbito, funções, responsabilidades, gestão de endereços compromisso, coordenação entre entidades organizacionais e de conformidade; e procedimentos para facilitar a implementação da identificação e política de autenticação e identificação e controlos de autenticação analisa e atualiza a política de autenticação e identificação atual [atribuição: frequência definida de organização]; e procedimentos de identificação e autenticação [atribuição: frequência definida de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Política de identificação e autenticação de nível empresarial e procedimentos do cliente podem ser suficientes para este controlo de endereços. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 controlo IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Identificação e autenticação (organizacionais utilizadores)

**IA-2** o sistema de informações identifica exclusivamente e autentica utilizadores organizacionais (ou processos agir em nome de utilizadores organizacionais).

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | As contas criadas por esta Blueprint Azure ter identificadores exclusivos. Contas incorporadas com identificadores exclusivos são desativadas ou removidas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 controlo IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso de rede para contas com privilégios

**IA-2 (1)** o sistema de informações implementa a autenticação multifator para acesso à rede para contas com privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para acesso à rede para contas com privilégios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 controlo IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso de rede para contas sem privilégios

**IA-2 (2)** o sistema de informações implementa a autenticação multifator para acesso à rede para contas sem privilégios.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para acesso à rede para contas sem privilégios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 controlo IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso local para contas com privilégios

**IA-2 (3)** o sistema de informações implementa a autenticação multifator para acesso local para contas com privilégios.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Cliente não ter acesso local ao quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure não permitir o acesso local, a menos que o acesso físico é necessário. Acesso de administrador local deve ser utilizado apenas para resolver problemas em instâncias onde o servidor membro está com problemas de rede e autenticação de domínio não está a funcionar. <br /> Azure implementa a autenticação multifator para acesso local através de mecanismos de controlo de acesso necessários para o acesso físico para o ambiente. Gabinetes dentro de centros de dados do Azure que contém todos os sistemas de infraestrutura do Azure dentro do limite de sistema estão limitadas por vários mecanismos de segurança física, incluindo o requisito de acesso badging empresarial de smart card e dispositivos biométricos. Ambos os formulários de autenticação são necessários para o acesso físico no ponto de entrada para colocations datacenter do Azure. |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 controlo IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso local ao contas sem privilégios

**IA-2 (4)** o sistema de informações implementa a autenticação multifator para acesso local ao contas sem privilégios.

**Responsabilidades:**`Azure Only`

|||
|---|---|
| **Cliente** | Cliente não ter acesso local ao quaisquer recursos de sistema nos centros de dados do Azure. |
| **Fornecedor (Microsoft Azure)** | Microsoft Azure considera todas as contas do Microsoft Azure Government utilizadas pela equipa do Microsoft Azure Government como privilegiado. A autenticação multifator está implementada para todas as contas de pessoal do Microsoft Azure Government utilizar smart cards e pins, que inclui acesso local. |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 controlo IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Identificação e autenticação (utilizadores organizacionais) | Autenticação de grupo

**IA-2 (5)** organização requer indivíduos sejam autenticados com um autenticador individuais quando um autenticador de grupo é utilizado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem contas de acesso partilhado/grupo estão ativadas nos recursos implementados por este Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 controlo IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso de rede para contas com privilégios - resistentes de repetição

**IA-2 (8)** mecanismos de autenticação de reproduzir a adulterações para acesso à rede para contas com privilégios de implementa o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso a recursos implementados por este Blueprint Azure se encontra protegido contra ataques de repetição, as funcionalidades de Kerberos incorporada do Azure Active Directory, do Active Directory e o sistema operativo Windows. A autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, tais como uma lista IP encriptada, carimbos de cliente e duração de permissão. Se um pacote é reproduzido, o timestamp é verificado. Se o timestamp é anterior ao, ou o mesmo que um autenticador anterior, o pacote foi rejeitado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 controlo IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso de rede para contas sem privilégios - resistentes de repetição

**IA-2 (9)** mecanismos de autenticação de reproduzir a adulterações para acesso à rede para contas sem privilégios de implementa o sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso a recursos implementados por este Blueprint Azure se encontra protegido contra ataques de repetição, as funcionalidades de Kerberos incorporada do Azure Active Directory, do Active Directory e o sistema operativo Windows. A autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, tais como uma lista IP encriptada, carimbos de cliente e duração de permissão. Se um pacote é reproduzido, o timestamp é verificado. Se o timestamp é anterior ao, ou o mesmo que um autenticador anterior, o pacote foi rejeitado. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 controlo IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Identificação e autenticação (utilizadores organizacionais) | Acesso remoto - dispositivos separadas

**IA-2 (11)** o sistema de informações implementa a autenticação multifator para acesso remoto para com privilégios e sem privilégios contas de forma a que um dos fatores é fornecido por um dispositivo separado do sistema obtenham acesso e o dispositivo cumpra [ Atribuição: força definido de organização dos requisitos de mecanismo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação multifator para aceder a recursos de implementar o cliente remotamente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 controlo IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Identificação e autenticação (utilizadores organizacionais) | Aceitação de Piv credenciais

**IA-2 (12)** o sistema de informações aceita e eletronicamente verifica as credenciais de verificação de identidade pessoal (PIV).

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar e verificar as credenciais de verificação de identidade pessoal (PIV). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 controlo IA-3

#### <a name="device-identification-and-authentication"></a>Autenticação e identificação de dispositivos

**IA-3** o sistema de informações identifica exclusivamente e autentica [atribuição: específicas do organização e/ou os tipos de dispositivos] antes de estabelecer uma [seleção (um ou mais): local; remoto; rede] ligação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por implementar a autenticação e identificação do dispositivo. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-4a"></a>Controlo IA-4.a do NIST 800-53

#### <a name="identifier-management"></a>Gestão de identificador

**IA-4.a** organização Gere identificadores de sistema de informações através da receção de autorização de [atribuição: técnico definido pela organização ou funções] para atribuir um indivíduo, identificador de dispositivo, grupo ou função.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão identificadores (ou seja, indivíduos, grupos, funções e dispositivos) para os recursos de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-4b"></a>Controlo IA-4.b do NIST 800-53

#### <a name="identifier-management"></a>Gestão de identificador

**IA-4.b** organização Gere identificadores de sistema de informações, selecionando um identificador que identifica um indivíduo, dispositivo, grupo ou função.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure pede ao utilizador durante a implementação para os identificadores de cliente especificadas de contas individuais.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-4c"></a>Controlo IA-4.c do NIST 800-53

#### <a name="identifier-management"></a>Gestão de identificador

**IA-4.c** organização Gere identificadores de sistema de informações ao atribuir o identificador para o indivíduo pretendido, o grupo, a função ou o dispositivo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão identificadores (ou seja, indivíduos, grupos, funções e dispositivos) para os recursos de cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-4d"></a>Controlo IA-4.d do NIST 800-53

#### <a name="identifier-management"></a>Gestão de identificador

**IA-4.d** organização Gere identificadores de sistema de informações por impedir a reutilização de identificadores para [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Active Directory e contas de sistema operativo Windows locais são atribuídas um identificador exclusivo de segurança (SID). As contas do Active Directory do Azure são atribuídas um ID de objeto globalmente exclusivo. Estes IDs exclusivos não são sujeitos a reutilização. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-4e"></a>Controlo IA-4.e do NIST 800-53

#### <a name="identifier-management"></a>Gestão de identificador

**IA-4.e** A organização Gere identificadores de sistema de informações através da desativação de identificador após [atribuição: organização definidos pelo período de tempo de inatividade].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa uma tarefa agendada para o Active Directory desativar as contas automaticamente depois de 35 dias de inatividade. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 controlo IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Gestão de identificador | Identificar o estado do utilizador

**IA-4 (4)** organização Gere identificadores individuais ao identificar exclusivamente cada indivíduo como [atribuição: característica definido pela organização que identifica o estado individual].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory e suporte do Active Directory que indica contratantes, fornecedores e outros tipos de utilizador utilizando as convenções de nomenclatura aplicadas aos respetivos identificadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5a"></a>Controlo IA-5.a do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.a** organização gere autenticadores de sistema de informações através da verificação dos, como parte da distribuição de autenticação inicial, a identidade do indivíduo, grupo, função ou dispositivo receber a autenticação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5b"></a>Controlo IA-5.b do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.b** organização gere autenticadores de sistema de informações através do estabelecimento de conteúdo de autenticação inicial para autenticadores definidos pela organização.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Todo o conteúdo de autenticação inicial para as contas criadas por esta Blueprint Azure satisfaz os requisitos indicados em IA-5 (1) verificada quando especificados pelo cliente durante a implementação.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5c"></a>Controlo IA-5.c do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.c** organização gere autenticadores de sistema de informações, garantindo que autenticadores tem suficiente força de mecanismo para utilização pretendida.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Autenticadores utilizados por esta requisitos do Azure Blueprint para força conforme exigido pelo FedRAMP. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5d"></a>Controlo IA-5.d do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.d** organização gere autenticadores de sistema de informações pelo estabelecimento e implementar administrativos procedimentos para distribuição de autenticação inicial de autenticadores comprometido/perdido ou danificados e revogue autenticadores.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5e"></a>Controlo IA-5.e do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.e** A organização gere autenticadores de sistema de informações ao alterar o conteúdo predefinido dos autenticadores antes da instalação do sistema de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Autenticadores de todos os componentes deste Blueprint Azure tiverem sido alterados as predefinições. Autenticadores são especificadas de cliente durante a implementação desta solução. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5f"></a>Controlo IA-5.f do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.f** organização gere autenticadores de sistema de informações através do estabelecimento de restrições de duração mínima e máxima e reutilizar as condições para autenticadores.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela gestão autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5g"></a>Controlo IA-5.g do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.g** organização gere autenticadores de sistema de informações atualizando a alteração/autenticadores [atribuição: organização definidos pelo período de tempo por tipo de autenticação].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo é estabelecida e configurada para implementar restrições de duração de palavra-passe (60 dias). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5h"></a>Controlo IA-5.h do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.h** organização gere autenticadores de sistema de informações por proteger conteúdo de autenticação de divulgação não autorizada e modificação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o Cofre de chaves para proteger o conteúdo do autenticador de divulgação não autorizada e modificação. As seguintes autenticadores são armazenados no Cofre de chaves: palavra-passe do Azure para a conta de implementar, a palavra-passe de administrador de máquina virtual, a palavra-passe de conta de serviço do SQL Server. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5i"></a>Controlo IA-5.i do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.i** organização gere autenticadores de sistema de informações ao exigir que os indivíduos a tomar e ter dispositivos implementar, a salvaguarda de segurança específicas para proteger autenticadores.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa o Cofre de chaves para proteger o conteúdo do autenticador de divulgação não autorizada e modificação. As seguintes autenticadores são armazenados no Cofre de chaves: palavra-passe do Azure para a conta de implementar, a palavra-passe de administrador de máquina virtual, a palavra-passe de conta de serviço do SQL Server. O Cofre de chaves encripta as chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados e as palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSMs). |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-5j"></a>Controlo IA-5.j do NIST 800-53

#### <a name="authenticator-management"></a>Gestão de autenticador

**IA-5.j** organização gere autenticadores de sistema de informações alterando autenticadores para contas de grupo/função quando associação às contas de alterações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem contas de acesso partilhado/grupo estão ativadas nos recursos implementados por este Blueprint do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1a"></a>.A do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**IA-5 (1) .a** o sistema de informações, para autenticação baseada em palavra-passe impõe a complexidade de palavra-passe mínima de [atribuição: requisitos definidos por organização para sensibilidade maiúsculas e minúsculas, número de carateres, mistura de letras maiúsculas, minúsculas letras, números e carateres especiais, incluindo os requisitos mínimos para cada tipo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo é estabelecida e configurada para impor a requisitos de complexidade de palavra-passe para contas locais de máquina virtual e contas de anúncios.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1b"></a>.B do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**IA-5 (1) .b** o sistema de informações, para autenticação baseada em palavra-passe impõe a, pelo menos, o seguinte número de carateres alteradas quando forem criadas novas palavras-passe: [atribuição: número definido de organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de autenticação baseada em palavra-passe dentro de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1c"></a>.C do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**IA-5 (1) .c** o sistema de informações, para autenticação baseada em palavra-passe armazena e transmite criptograficamente protegido apenas palavras-passe.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Diretório do Azure é utilizado para garantir que todas as palavras-passe criptograficamente estão protegidas enquanto armazenados e transmitidos. As palavras-passe armazenadas pelo Active Directory e localmente em máquinas de virtuais implementadas do Windows são codificadas automaticamente como parte da funcionalidade de segurança incorporadas. Sessões de ambiente de trabalho de autenticação remota são protegidas através de TLS para garantir a proteção autenticadores quando transmitido. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1d"></a>.D do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**IA-5 (1) .d** o sistema de informações, para autenticação baseada em palavra-passe impõe mínimo da palavra-passe e restrições de duração máxima do [atribuição: números definido pela organização para a duração mínima, duração máxima].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo é estabelecida e configurada para impor restrições de palavras-passe que impõem mínimo (1 dia) e a duração máxima (60 dias) restrições para contas locais e contas de anúncios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1e"></a>.E do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**.E IA-5 (1)** O sistema de informações, para autenticação baseada em palavra-passe proíbe a reutilização de palavras-passe para [atribuição: número definido de organização] gerações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Este Blueprint Azure implementa um controlador de domínio ao qual estão associadas a todas as máquinas virtuais implementadas. Uma política de grupo é estabelecida e configurada para impor restrições em condições de reutilização (24 palavras-passe) para contas locais e contas de anúncios. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-1f"></a>.F do NIST 800-53 controlo IA-5 (1)

#### <a name="authenticator-management--password-based-authentication"></a>Gestão de autenticador | Autenticação baseada em palavra-passe

**IA-5 (1) .f** o sistema de informações, para autenticação baseada em palavra-passe permite a utilização de uma palavra-passe temporária para inícios de sessão do sistema com uma alteração imediata uma palavra-passe permanente.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Azure Active Directory é utilizado para gerir o controlo de acesso ao sistema de informações. Sempre que uma conta for criada inicialmente, ou uma palavra-passe temporária é gerada, o Azure Active Directory é utilizado para exigir que o utilizador altere a palavra-passe no próximo início de sessão. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-2a"></a>.A do NIST 800-53 controlo IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Gestão de autenticador | Autenticação baseada em PKI

**IA-5 (2) .a** o sistema de informações, para autenticação baseada em PKI valida certificações através da criação e verificar um caminho de certificação para uma âncora de confiança aceite, incluindo a verificar as informações de estado do certificado.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de autenticação baseada em PKI dentro de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-2b"></a>.B do NIST 800-53 controlo IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Gestão de autenticador | Autenticação baseada em PKI

**IA-5 (2) .b** o sistema de informações, para autenticação baseada em PKI impõe acesso autorizado à chave privada correspondente.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de autenticação baseada em PKI dentro de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-2c"></a>.C do NIST 800-53 controlo IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Gestão de autenticador | Autenticação baseada em PKI

**IA-5 (2) .c** o sistema de informações, para autenticação baseada em PKI mapeia a identidade autenticada para a conta do indivíduo ou grupo.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de autenticação baseada em PKI dentro de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-2d"></a>.D do NIST 800-53 controlo IA-5 (2)

#### <a name="authenticator-management--pki-based-authentication"></a>Gestão de autenticador | Autenticação baseada em PKI

**IA-5 (2) .d** o sistema de informações, para autenticação baseada em PKI implementa uma cache de dados de revogação para suportar a deteção de caminho e a validação em caso de impossibilidade de aceder às informações de revogação através da rede local.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável pela utilização de autenticação baseada em PKI dentro de recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 controlo IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Gestão de autenticador | Registo de terceiros em pessoa ou fidedigno

**IA-5 (3)** organização exige que o registo de processo para receber [atribuição: tipos definidos de organização de e/ou autenticadores específicos] realização de um [seleção: pessoalmente; por terceiros fidedigna] antes de [atribuição: autoridade de registo definida pelo organização] com a autorização por [atribuição: técnico definido pela organização ou funções].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por registar autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 controlo IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Gestão de autenticador | Suporte automatizado para a determinação da força de palavra-passe

**IA-5 (4)** organização emprega ferramentas automatizadas para determinar se autenticadores de palavra-passe são suficientemente seguras para satisfazer [atribuição: requisitos definidos por organização].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Incluem de contas de utilizador implementadas com este Blueprint do Azure AD e contas de utilizador local. Ambos estes fornecem mecanismos que forçar a compatibilidade com requisitos de palavra-passe estabelecida para criar uma palavra-passe inicial e durante a alterações de palavra-passe. Azure Active Directory é a ferramenta automatizada utilizada para determinar se autenticadores de palavra-passe são suficientemente seguras para satisfazer o comprimento de palavra-passe, complexidade, rotação e restrições de duração estabelecidas no IA-5 (1). Azure Active Directory garante que força de autenticação de palavra-passe durante a criação do cumpre os requisitos dessas normas. Cliente-especificado palavras-passe utilizadas para implementar esta solução são verificadas para satisfazer os requisitos de segurança de palavra-passe. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 controlo IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Gestão de autenticador | Proteção de autenticadores

**IA-5 (6)** a organização protege autenticadores proporcionais a categoria de segurança das informações para o qual a utilização do autenticador permite o acesso.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por proteger autenticadores. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 controlo IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Gestão de autenticador | Não existem autenticadores de estáticos não encriptadas incorporados

**IA-5 (7)** organização garante que autenticadores estáticos não encriptadas não são incorporados em aplicações ou scripts de acesso ou armazenados em teclas de função.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não há nenhuma utilização de autenticadores estáticos não encriptadas incorporada em aplicações, aceder aos scripts ou função chaves implementadas por esta Blueprint do Azure. Qualquer aplicação que utiliza um autenticador ou script efetua uma chamada para um contentor do Cofre de chaves do Azure antes de cada utilização. Acesso a contentores do Cofre de chaves do Azure é auditado, que permite a deteção das violações este prohibition se uma conta de serviço é utilizada para aceder a um sistema sem uma chamada correspondente para o contentor do Cofre de chaves do Azure. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 controlo IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Gestão de autenticador | Várias contas de sistema de informações

**IA-5 (8)** implementa a organização [atribuição: as proteções de segurança definido para organização] para gerir o risco de compromisso devido a indivíduos com contas em vários sistemas de informações.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente poderá dependem as proteções de segurança de nível empresarial para gerir os riscos associados a utilizadores com contas em vários sistemas. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 controlo IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Gestão de autenticador | Autenticação baseada em tokens de hardware

**IA-5 (11)** o sistema de informações, para autenticação baseada em tokens de hardware, emprega mecanismos que satisfaçam [atribuição: requisitos definidos por organização token qualidade].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar mecanismos satisfazer os requisitos de qualidade de autenticação baseada em tokens de hardware. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 controlo IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Gestão de autenticador | Expiração de autenticadores em cache

**IA-5 (13)** o sistema de informações proíbe a utilização de autenticadores em cache após [atribuição: organização definidos pelo período de tempo].

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Não existem recursos implementados por este Blueprint do Azure estão configurados para permitir a utilização de autenticadores em cache. A autenticação para as máquinas virtuais implementadas requer que um autenticador é introduzido no momento da autenticação. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 controlo IA-6

#### <a name="authenticator-feedback"></a>Comentários de autenticador

**IA-6** o sistema de informações obscures comentários de informações de autenticação durante o processo de autenticação para proteger as informações de possíveis exploração por utilizar por pessoas não autorizadas.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Acesso a recursos implementados por este Blueprint do Azure é efetuada através do ambiente de trabalho remoto e baseia-se na autenticação do Windows. O comportamento predefinido de sessões de autenticação do Windows dissimula palavras-passe quando durante uma sessão de autenticação.  |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 controlo IA-7

#### <a name="cryptographic-module-authentication"></a>Autenticação do módulo criptográfico

**IA-7** mecanismos de autenticação para um módulo criptográfico que cumprem os requisitos do federais e leis aplicáveis, as ordens executivo, diretivas, políticas, normas, normas, orientações para tal implementa o sistema de informações autenticação.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | Autenticação do Windows, o ambiente de trabalho remoto e o BitLocker são utilizada por esta Blueprint do Azure. Estes componentes podem ser configurados confiar nos módulos criptográficos FIPS 140 validada. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 controlo IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Identificação e autenticação (não organizacional utilizadores)

**IA-8** o sistema de informações identifica exclusivamente e autentica os utilizadores não organizacional (ou processos agir em nome dos utilizadores não organizacionais).

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por identificar e autenticar utilizadores não organizacional aceder a recursos implementados no cliente. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 controlo IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Identificação e autenticação (utilizadores não organizacional) | Aceitação de Piv credenciais de outros governamentais

**IA-8 (1)** o sistema de informações aceita e eletronicamente verifica as credenciais de verificação de identidade pessoal (PIV) de outras órgãos federais.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar e verificar as credenciais de verificação de identidade pessoal (PIV) emitidas por outros órgãos federais. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 controlo IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Identificação e autenticação (utilizadores não organizacional) | Aceitação de credenciais de terceiros

**IA-8 (2)** o sistema de informações aceita apenas aprovados FICAM credenciais de terceiros.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por aceitar apenas credenciais de terceiros que foram aprovadas pela iniciativa de identidade Federal, credenciais e gestão de acesso (FICAM) de confiança Framework soluções. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 controlo IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Identificação e autenticação (utilizadores não organizacional) | Utilização de produtos Ficam aprovado

**IA-8 (3)** a organização utiliza apenas componentes do sistema aprovado FICAM informações no [atribuição: sistemas informações definidas pela organização] para aceitar as credenciais de terceiros.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por empregar apenas Federal identidade, credenciais e a iniciativa de gestão de acesso (FICAM) de confiança Framework soluções aprovada recursos para aceitar as credenciais de terceiros. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 controlo IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Identificação e autenticação (utilizadores não organizacional) | Utilização de perfis emitidos por Ficam

**IA-8 (4)** está em conformidade com o sistema de informações aos perfis emitidos por FICAM.

**Responsabilidades:**`Customer Only`

|||
|---|---|
| **Cliente** | O cliente é responsável por cumprindo os perfis emitidos pela iniciativa de identidade Federal, credenciais e gestão de acesso (FICAM) de confiança Framework soluções. |
| **Fornecedor (Microsoft Azure)** | Não Aplicável |
