---
title: "Encriptação e de segurança dos dados melhores práticas | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança dos dados e utilizar encriptação incorporada no capacidades do Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 0cebc7ae5279b720e8fd0d6c986e1706d944476f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Melhores práticas de segurança de dados do Azure e encriptação
Uma das chaves para proteção de dados na nuvem é contabilidade para os Estados possíveis em que os dados poderão ocorrer e que controlos estão disponíveis para esse Estado. Para efeitos de dados do Azure segurança encriptação melhores práticas e recomendações serão à volta de Estados dos dados seguintes:

* Em rest: Isto inclui informações de todos os objetos de armazenamento, contentores e tipos de que existem estaticamente no suporte de dados físico, ser-torção ou optical disco.
* Em trânsito: Quando a dados está a ser transferidos entre componentes, localizações ou programas, tal como através da rede, entre um barramento de serviço (a partir de no local para a nuvem e vice-versa, incluindo as ligações híbridas, tais como o ExpressRoute), ou durante um processo de entrada/saída, é considerar como estando em movimento.

Neste artigo, vamos abordar uma coleção de dados do Azure e encriptação de segurança melhores práticas. Estas melhores práticas são derivadas da nossa experiência com a segurança dos dados do Azure e encriptação e as experiências dos clientes, como por si.

Para cada melhor prática, vamos explicar:

* O que é a melhor prática
* Por que motivo que pretende ativar essa recomendado
* Se falhar ativar a melhor prática, que poderá ser o resultado
* Possíveis alternativas à melhor prática
* Como pode saber mais ativar a melhor prática

Este artigo de melhores práticas de encriptação e de segurança dos dados do Azure baseia-se no opinião um consenso e capacidades da plataforma do Azure e conjuntos de funcionalidades, tal como existem no momento que este artigo foi escrito. Opinions e tecnologias alteram ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Dados do Azure encriptação melhores práticas de segurança e abordadas neste artigo incluem:

* Impor autenticação multifator
* Controlo de acesso (RBAC) baseado em funções de utilização
* Encriptar virtual machines do Azure
* Utilizar modelos de segurança de hardware
* Gerir com estações de trabalho seguras
* Ativar a encriptação de dados do SQL Server
* Proteger dados em trânsito
* Impor a encriptação de dados ao nível do ficheiro

## <a name="enforce-multi-factor-authentication"></a>Impor autenticação multifator
O primeiro passo no acesso a dados e controlo no Microsoft Azure estão a autenticar o utilizador. [Azure multi-factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) é um método de verificar a identidade do utilizador utilizando outro método que apenas um nome de utilizador e palavra-passe. Esta autenticação método ajuda a salvaguardar o acesso a dados e aplicações, cumprindo o pedido do utilizador para um processo de início de sessão simple.

Ao ativar a MFA do Azure para os seus utilizadores, que está a adicionar uma segunda camada de segurança para inícios de sessão de utilizador e de transações. Neste caso, uma transação poderá aceder a um documento localizado num servidor de ficheiros ou o SharePoint Online. Também ajuda-o MFA do Azure que as TI reduzem a probabilidade de que uma credencial comprometida terão acesso aos dados da organização.

Por exemplo: se impor o MFA do Azure para os seus utilizadores e configurá-lo para utilizar uma chamada telefónica ou mensagem de texto como verificação, se a credencial do utilizador for comprometida, o atacante não conseguirá aceder a qualquer recurso uma vez que ele não terá acesso para o telefone do utilizador. As organizações que não adicione esta camada adicional de proteção de identidade sejam mais suscetíveis de ataque de roubo de credenciais, que pode levar ao comprometimento de dados.

Uma alternativa para as organizações que pretende manter a autenticação controlo no local é utilizar [do servidor multi-factor Authentication Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), também denominado MFA no local. Ao utilizar este método irá ainda ser capaz de impôr a autenticação multifator, mantendo o MFA server no local.

Para obter mais informações sobre o MFA do Azure, leia o artigo [introdução ao Azure multi-factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Controlo de acesso (RBAC) baseado em funções de utilização
Restringir o acesso com base no [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança. Este é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados. Azure baseada em funções controlo de acesso (RBAC) pode ser utilizado para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito. O âmbito de uma atribuição de função pode ser uma subscrição, um grupo de recursos ou um único recurso.

Pode tirar partido [funções incorporadas do RBAC](../active-directory/role-based-access-built-in-roles.md) no Azure para atribuir os privilégios aos utilizadores. Considere a utilização de *contribuinte de conta de armazenamento* para os operadores da nuvem que necessitam de gerir as contas do storage e *contribuinte de conta de armazenamento clássico* função para gerir contas de armazenamento clássicas. Para os operadores da nuvem que necessita para gerir VMs e conta de armazenamento, considere adicionar-lhes *contribuinte de Máquina Virtual* função.

As organizações que não a impor controlo de acesso de dados ao tirar partido das capacidades, tal como o RBAC podem ser dá ao mais privilégios que necessários para os respetivos utilizadores. Isto pode levar ao comprometimento de dados por ter alguns utilizadores ter acesso aos dados que não deve ter em primeiro lugar.

Pode saber mais sobre o RBAC do Azure ao ler o artigo [controlo de acesso em funções do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Encriptar Virtual Machines do Azure
Para muitas organizações, [encriptação de dados Inativos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório para soberania dos dados, conformidade e privacidade de dados. Encriptação de disco do Azure permite aos administradores de TI encriptar discos do Windows e Linux IaaS Máquina Virtual (VM). Tira partido do Azure Disk Encryption a funcionalidade do BitLocker de padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer a encriptação de volume para o SO e os discos de dados.

Pode tirar partido do Azure Disk Encryption para ajudar a proteger e salvaguardar os seus dados para satisfazer os seus requisitos de conformidade e segurança organizacional. As organizações também devem considerar utilizar a encriptação para ajudar a mitigar os riscos acesso a dados relacionados para não autorizado. Também é recomendável que encriptar unidades antes de escrever os dados confidenciais.

Certifique-se encriptar os volumes de dados da VM e o volume de arranque para poder proteger dados Inativos na sua conta do storage do Azure. Salvaguardar as chaves de encriptação e segredos, tirando partido [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md).

Para os servidores do Windows no local, considere a encriptação seguinte melhores práticas:

* Utilize [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para encriptação de dados
* Armazenar as informações de recuperação no AD DS.
* Se não houver qualquer preocupação de que as chaves do BitLocker tem sido comprometidas, recomendamos que optar por formatar a unidade para remover todas as instâncias dos metadados do BitLocker na unidade ou que desencriptar e encriptar a unidade completa novamente.

As organizações que não impor a encriptação de dados estejam mais predispostas a ser exposto a problemas de integridade de dados, como os utilizadores mal intencionados ou não autorizados roubo de dados e comprometido contas obtenham acesso não autorizado aos dados num formato encriptado. Para além destes riscos, as empresas que têm de estar em conformidade com regulamentos da indústria, tem de provar que são diligent e estiver a utilizar os controlos de segurança correto para melhorar a segurança dos dados.

Pode saber mais sobre a Azure Disk Encryption ao ler o artigo [encriptação de disco do Azure para Windows e as VMs de IaaS Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utilize módulos de segurança de Hardware
Soluções de encriptação da indústria utilizam chaves secretas para encriptar dados. Por conseguinte, é fundamental que estas chaves são armazenadas em segurança. Gestão de chaves torna-se uma parte integral da proteção de dados, uma vez que irá ser utilizada para armazenar as chaves secretas que são utilizadas para encriptar dados.

A encriptação de disco do Azure utiliza [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) para ajudar a controlar e gerir chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, enquanto garantir que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure. Deve utilizar o Cofre de chaves do Azure para as chaves e a utilização da política de auditoria.

Existem muitos riscos inerentes relacionadas com a não ter controlos de segurança adequados para proteger as chaves secretas que foram utilizadas para encriptar os dados. Se os atacantes têm acesso às chaves secretas, poderão desencriptar os dados e poderão ter acesso a informações confidenciais.

Pode saber mais sobre as recomendações gerais para a gestão de certificados no Azure ao ler o artigo [gestão de certificados no Azure: Do's e que não deve fazer](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para mais informações sobre o Cofre de chaves do Azure, leia o artigo [introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gerir com estações de trabalho seguras
Uma vez que a vasta maioria das ataques de visar o utilizador final, o ponto final torna-se um dos pontos de principais de ataque. Se um atacante compromises o ponto final, ele pode tirar partido as credenciais do utilizador para aceder aos dados da organização. A maioria dos ataques de ponto final são capazes de tirar partido do facto de que os utilizadores finais são administradores nos respetivos estações de trabalho locais.

Pode reduzir estes riscos através da utilização de uma estação de trabalho de gestão segura. Recomendamos que utilize um [estações de trabalho com privilégios de acesso (PAW)](https://technet.microsoft.com/library/mt634654.aspx) para reduzir a superfície de ataque em estações de trabalho. Estas estações de trabalho de gestão protegidos podem ajudar a mitigar alguns deles ataques ajudam a garantir que os seus dados são mais seguro. Certifique-se de que utiliza PAW para proteger e bloquear a estação de trabalho. Este é um passo importante para fornecer elevada segurança oferece garantias ao rendimento para contas confidenciais, tarefas e proteção de dados.

Falta do endpoint protection pode colocar dados em risco, certifique-se para impor políticas de segurança em todos os dispositivos que são utilizados para consumir dados, independentemente da localização de dados (na nuvem ou no local).

Pode saber mais sobre privileged aceder a estação de trabalho ao ler o artigo [proteger o acesso privilegiado](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Ativar a encriptação de dados do SQL Server
[Encriptação de dados transparente de base de dados SQL do Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) ajuda a proteger contra a ameaça de atividade maliciosa efetuando a encriptação em tempo real e a desencriptação da base de dados, cópias de segurança associadas e ficheiros de registo de transações Inativos sem necessidade de alterações à aplicação.  TDE encripta o armazenamento de uma base de dados completa através de uma chave simétrica chamada a chave de encriptação da base de dados.

Mesmo quando o armazenamento completo é encriptado, é muito importante também encripte a sua própria base de dados. Esta é uma implementação de defesa numa abordagem de profundidade para proteção de dados. Se estiver a utilizar [SQL Database do Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e pretende proteger os dados confidenciais, tais como o cartão de crédito ou números de segurança social, pode encriptar as bases de dados com FIPS 140-2 validado bits 256 AES encriptação que cumpra os requisitos de muitos normas da indústria (por exemplo, HIPAA, PCI).

É importante compreender que os ficheiros relacionados com [a memória intermédia de extensão do conjunto](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) não são encriptadas quando uma base de dados é encriptado utilizando TDE. Tem de utilizar ferramentas de encriptação de nível do sistema de ficheiros, como o BitLocker ou o [sistema de encriptação de ficheiros](https://technet.microsoft.com/library/cc700811.aspx) (EFS) para BPE relacionados com os ficheiros.

Dado que um utilizador autorizado, tais como um administrador de segurança ou um administrador da base de dados pode aceder aos dados, mesmo se a base de dados é encriptado com TDE, também deve seguir as recomendações abaixo:

* Autenticação do SQL Server ao nível da base de dados
* Azure AD autenticação utilizando funções do RBAC
* Utilizadores e aplicações devem utilizar contas separadas para autenticação. Desta forma, pode limitar as permissões concedidas a utilizadores e aplicações e reduzir os riscos de atividade maliciosa
* Segurança de nível de base de dados de implementar através de funções de base de dados fixa (como db_datareader ou db_datawriter), ou pode criar funções personalizadas para a sua aplicação conceder permissões explícitas para objetos de base de dados selecionada

As organizações que não estão a utilizar encriptação de nível de base de dados podem ser mais suscetíveis a ataques que podem comprometer os dados localizados nas bases de dados SQL.

Pode saber mais sobre a encriptação de SQL TDE ao ler o artigo [encriptação transparente de dados com a SQL Database do Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteger dados em trânsito
Proteger dados em trânsito deve fazer parte essencial da sua estratégia de proteção de dados. Uma vez que os dados irão ser movidos anterior e descritos de diversas localizações, a recomendação geral é que utilize sempre protocolos SSL/TLS para trocar dados entre localizações diferentes. Em algumas circunstâncias, poderá pretender isolar o canal de comunicação completa entre o local e nuvem infraestrutura através da utilização de uma rede privada virtual (VPN).

Para dados mover entre a infraestrutura no local e o Azure, deve considerar as proteções de adequada, tais como HTTPS ou VPN.

Para organizações que precisam de proteger o acesso a partir de várias estações de trabalho localizados no local para o Azure, utilize [VPN de site para site do Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Para organizações que precisam de proteger o acesso a partir de uma estação de trabalho localizado no local para o Azure, utilize [ponto a Site VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Conjuntos de dados maiores podem ser movidos através de uma ligação WAN de alta velocidade dedicada, tais como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se optar por utilizar o ExpressRoute, também pode encriptar os dados ao nível da aplicação utilizando [SSL/TLS](https://support.microsoft.com/kb/257591) ou outros protocolos para maior proteção.

Se interage com o Storage do Azure através do Portal do Azure, todas as transações ocorrem através de HTTPS. [API REST do Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) HTTPS também pode ser utilizado para interagir com a ativação pós-falha [Storage do Azure](https://azure.microsoft.com/services/storage/) e [SQL Database do Azure](https://azure.microsoft.com/services/sql-database/).

As organizações que falham para proteger dados em trânsito sejam mais suscetíveis de [ataques man-in-the-middle](https://technet.microsoft.com/library/gg195821.aspx), [escutas](https://technet.microsoft.com/library/gg195641.aspx) e hijacking de sessão. Estes ataques podem ser o primeiro passo na obtendo acesso aos dados confidenciais.

Pode saber mais sobre a opção de VPN do Azure ao ler o artigo [planeamento e design para o Gateway de VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Impor a encriptação de dados ao nível do ficheiro
Outra camada de proteção que pode aumentar o nível de segurança para os seus dados está a encriptar o próprio ficheiro, independentemente da localização do ficheiro.

[O Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utiliza políticas de encriptação, identidade e autorização para ajudar a proteger os seus ficheiros e e-mail. O Azure RMS funciona em vários dispositivos — telemóveis, tablets e PCs ao proteger dentro da sua organização e fora da sua organização. Esta capacidade é possível porque o Azure RMS adiciona um nível de proteção permanece com os dados, mesmo quando sai dos limites da organização.

Quando utilizar o Azure RMS para proteger os seus ficheiros, estiver a utilizar a criptografia de norma da indústria com suporte completo de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Ao tirar partido do Azure RMS para proteção de dados, tem a garantia que a proteção permanece com o ficheiro, mesmo que seja copiado para armazenamento que não se encontra sob o controlo de TI, tal como um serviço de armazenamento em nuvem. O mesmo ocorre para os ficheiros partilhados através de e-mail, o ficheiro está protegido como um anexo a uma mensagem de e-mail, com instruções sobre como abrir o anexo protegido.

Quando planear a adoção do Azure RMS, recomendamos o seguinte:

* Instalar o [aplicação de partilha RMS](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicação integra-se com o Office aplicações através da instalação de um escritório suplemento para que os utilizadores possam facilmente proteger ficheiros diretamente.
* Configurar aplicações e serviços suportam o Azure RMS
* Criar [modelos personalizados](https://technet.microsoft.com/library/dn642472.aspx) que reflete os requisitos de negócio. Por exemplo: um modelo de dados secretos superiores que devem ser aplicados em todos os principal segredo relacionados com mensagens de correio eletrónico.

As organizações que são fracas no [classificação de dados](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e proteção de ficheiros pode ser mais suscetível a fuga de dados. Sem proteção de ficheiros adequado, as organizações não conseguirão obter informações empresariais, monitorizar abusos e impedir o acesso malicioso a ficheiros.

Pode saber mais sobre o Azure RMS ao ler o artigo [introdução ao Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
