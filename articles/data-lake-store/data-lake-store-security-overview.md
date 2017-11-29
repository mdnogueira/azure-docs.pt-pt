---
title: "Descrição geral de segurança no Data Lake Store | Microsoft Docs"
description: "Compreender a forma como o Azure Data Lake Store é um arquivo de macrodados mais seguro"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 52e176711f512e8a3788309a58011c8484821a1e
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="security-in-azure-data-lake-store"></a>Segurança no Azure Data Lake Store
Muitas empresas são tirar partido da análise de macrodados informações empresariais ajudar a tomar decisões inteligentes. Uma organização pode ter um ambiente complexo e regulado, com um número crescente de diversos utilizadores. É vital para uma empresa para se certificar de que os dados empresariais críticos são armazenados de forma mais segura, com o nível correto de acesso concedido a utilizadores individuais. O Azure Data Lake Store foi concebido para ajudar que cumpram estes requisitos de segurança. Neste artigo, saiba mais sobre as capacidades de segurança do Data Lake Store, incluindo:

* Autenticação
* Autorização
* Isolamento de rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Gestão de identidade e autenticação
A autenticação é o processo pelo qual um utilizador é confirmar a identidade quando o utilizador interage com o Data Lake Store ou com o serviço que liga ao Data Lake Store. Gestão de identidade e autenticação, de Data Lake Store utiliza [do Azure Active Directory](../active-directory/active-directory-whatis.md), uma abrangente identidades e acessos na nuvem solução de gestão que simplifica a gestão de utilizadores e grupos.

Cada subscrição do Azure pode ser associada uma instância do Azure Active Directory. Apenas os utilizadores e identidades de serviço que estão definidas no seu serviço do Azure Active Directory podem aceder à sua conta do Data Lake Store, utilizando o portal do Azure, as ferramentas de linha de comandos, ou através de aplicações de cliente organização baseia-se utilizando o SDK do Azure Data Lake Store. Principais vantagens de utilizar o Azure Active Directory como um mecanismo de controlo de acesso centralizado são:

* Gestão de ciclo de vida de identidade simplificada. A identidade de um utilizador ou um serviço (uma identidade principal do serviço) pode ser criada rapidamente e rapidamente revogada pela simplesmente eliminar ou desativar a conta no diretório.
* Autenticação multifator. [Autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para inícios de sessão de utilizador e de transações.
* Autenticação a partir de qualquer cliente através de um protocolo de abrir padrão, tais como OAuth ou OpenID.
* Federação com os serviços de diretório de enterprise e fornecedores de identidade de nuvem.

## <a name="authorization-and-access-control"></a>Autorização e controlo de acesso
Depois do Azure Active Directory efetua a autenticação de um utilizador para que o utilizador pode aceder ao Azure Data Lake Store, controlos de autorização permissões de acesso para Data Lake Store. Arquivo data Lake separa autorização para atividades relacionadas com a conta e relacionadas com a dados da seguinte forma:

* [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) (RBAC) fornecida pelo Azure para gestão de contas
* ACL POSIX para aceder aos dados no arquivo

### <a name="rbac-for-account-management"></a>RBAC para a gestão de conta
Por predefinição, quatro funções básicas são definidas para o Data Lake Store. As funções permitem operações diferentes numa conta do Data Lake Store através do portal do Azure, os cmdlets do PowerShell e REST APIs. As funções de proprietário e contribuinte podem efetuar uma variedade de funções de administração na conta. Pode atribuir a função de leitor aos utilizadores que apenas interagem com os dados.

![Funções do RBAC](./media/data-lake-store-security-overview/rbac-roles.png "funções do RBAC")

Tenha em atenção que, apesar das funções são atribuídas para gestão de contas, algumas funções afetam acesso a dados. Tem de utilizar as ACLs para controlar o acesso para operações que um utilizador pode efetuar no sistema de ficheiros. A tabela seguinte mostra um resumo dos direitos e gestão de direitos de acesso de dados para as funções predefinidas.

| Funções | Direitos de gestão | Direitos de acesso de dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhuma |Regida pelo ACL |O utilizador não é possível utilizar o portal do Azure ou os cmdlets do Azure PowerShell para procurar o Data Lake Store. O utilizador pode utilizar apenas as ferramentas da linha de comandos. |
| Proprietário |Todos |Todos |A função de proprietário é um Superutilizador. Esta função pode gerir tudo e tem acesso total aos dados. |
| Leitor |Só de Leitura |Regida pelo ACL |A função de leitor pode ver tudo sobre gestão de contas, por exemplo, o que o utilizador está atribuído ao qual a função. A função de leitor não é possível efetuar quaisquer alterações. |
| Contribuinte |Todos os exceto adicionar e remover funções |Regida pelo ACL |A função de contribuinte pode gerir alguns aspetos de uma conta, tais como implementações e criar e gerir alertas. A função de contribuinte não é possível adicionar ou remover funções. |
| Administrador de Acesso de Utilizador |Adicionar e remover funções |Regida pelo ACL |A função de administrador de acesso de utilizador pode gerir o acesso às contas de utilizador. |

Para obter instruções, consulte [atribuir utilizadores ou grupos de segurança para contas de Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Utilizando ACLs para operações em sistemas de ficheiros
Data Lake Store é um sistema de ficheiros hierárquico como distribuídas ficheiro sistema Hadoop (HDFS) e suporta [POSIX ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controla (r) de leitura, escrita (m) e executar (permissões para recursos para a função de proprietário, para o grupo de proprietários e para outros utilizadores e grupos x). Na Pré-visualização Pública do Data Lake Store (a versão atual), as ACLs podem ser ativadas na pasta raiz, em subpastas e em ficheiros individuais. Para obter mais informações sobre como as ACLs funcionam no contexto do Data Lake Store, veja [Controlo de acesso no Data Lake Store](data-lake-store-access-control.md).

Recomendamos que definir ACLs para vários utilizadores utilizando [grupos de segurança](../active-directory/active-directory-groups-create-azure-portal.md). Adicionar utilizadores a um grupo de segurança e, em seguida, atribua as ACLs para um ficheiro ou pasta para o grupo de segurança. Isto é útil quando pretende fornecer acesso personalizados, porque está limitado a adição de um máximo de nove entradas de acesso personalizados. Para obter mais informações sobre como proteger melhor os dados armazenados no Data Lake Store utilizando grupos de segurança do Azure Active Directory, consulte [atribuir utilizadores ou grupo de segurança como ACLs para o sistema de ficheiros do Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Lista de acesso padrão e personalizado](./media/data-lake-store-security-overview/adl.acl.2.png "lista acesso padrão e personalizado")

## <a name="network-isolation"></a>Isolamento de rede
Utilizar o Data Lake Store para ajudar a controlar o acesso ao seu arquivo de dados ao nível da rede. Pode estabelecer as firewalls e definir um intervalo de endereços IP para os seus clientes fidedignos. Com um intervalo de endereços IP, apenas os clientes que têm um endereço IP no intervalo definido podem ligar ao Data Lake Store.

![As definições da firewall e IP acesso](./media/data-lake-store-security-overview/firewall-ip-access.png "endereço IP e definições da Firewall")

## <a name="data-protection"></a>Proteção de dados
O Azure Data Lake Store protege os dados ao longo do respetivo ciclo de vida. Para os dados em trânsito, o Data Lake Store utiliza o protocolo de segurança de camada de transporte (TLS) de norma da indústria para proteger dados através da rede.

![Encriptação no Data Lake Store](./media/data-lake-store-security-overview/adls-encryption.png "encriptação no Data Lake Store")

O Data Lake Store também fornece encriptação para dados armazenados na conta. Pode optar por encriptar os seus dados ou por não utilizar encriptação. Se optar por para encriptação, dados armazenados no Data Lake Store são encriptados antes de armazenar no suporte de dados persistente. Nesse caso, o Data Lake Store automaticamente encripta os dados antes de a persistência e desencripta os dados antes da obtenção de, pelo que é completamente transparente para o cliente de acesso aos dados. Não há nenhuma alteração de código necessária do lado do cliente para encriptar/desencriptar dados.

Gestão de chaves, o Data Lake Store fornece dois modos para gerir as chaves de encriptação mestra (MEKs), que são necessárias para desencriptar os dados são armazenados no Data Lake Store. Pode optar por deixar o Data Lake Store gerir os MEKs por si ou optar por manter a propriedade de MEKs utilizando a sua conta do Cofre de chaves do Azure. Especifique o modo de gestão de chaves enquanto durante a criação de uma conta de Data Lake Store. Para obter mais informações sobre como fornecer configuração relacionada com encriptação, veja [Introdução ao Azure Data Lake Store utilizando o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="auditing-and-diagnostic-logs"></a>Registos de auditorias e diagnóstico
Pode utilizar os registos de auditorias ou de diagnóstico, dependendo se estiver à procura de registos para atividades relacionadas com a gestão ou atividades relacionadas com a dados.

* Atividades relacionadas com a gestão de utilizam as APIs do Azure Resource Manager e estão anexadas no portal do Azure através de registos de auditoria.
* Atividades relacionadas com a dados utilizam WebHDFS REST APIs e estão anexadas no portal do Azure através de registos de diagnóstico.

### <a name="auditing-logs"></a>Registos de auditorias
Para estar em conformidade com regulamentos, uma organização exijam os registos de auditoria adequado se tiver de aprofundar em incidentes específicos. Arquivo data Lake tem monitorização incorporada e auditoria e regista todas as atividades de gestão de conta.

Para registos de auditoria da gestão de conta, ver e escolha as colunas que pretende iniciar sessão. Também pode exportar os registos de auditoria para o Storage do Azure.

![Registos de auditoria](./media/data-lake-store-security-overview/audit-logs.png "Registos de auditoria")

### <a name="diagnostic-logs"></a>Registos de diagnósticos
Pode definir os registos de auditoria de acesso a dados no portal do Azure (nas definições de diagnóstico) e criar uma conta de armazenamento de Blobs do Azure onde os registos são armazenados.

![Os registos de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "registos de diagnóstico")

Depois de configurar as definições de diagnóstico, pode ver os registos no **os registos de diagnóstico** separador.

Para obter mais informações sobre como trabalhar com registos de diagnóstico com o Azure Data Lake Store, consulte [aceder a registos de diagnóstico para o Data Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo
Os clientes empresariais exigem uma plataforma de nuvem de análise de dados que é segura e fácil de utilizar. O Azure Data Lake Store foi concebido para ajudar o endereço que estes requisitos através da gestão de identidade e autenticação através de integração do Active Directory do Azure, com base na ACL autorização, o isolamento de rede, a encriptação de dados em trânsito e rest (futuras no futuro) e auditoria.

Se pretender ver as novas funcionalidades no Data Lake Store, envie-nos seus comentários [fórum do Data Lake Store UserVoice](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consultar também
* [Descrição geral do Azure Data Lake Store](data-lake-store-overview.md)
* [Introdução ao Data Lake Store](data-lake-store-get-started-portal.md)
* [Secure data in Data Lake Store (Proteger dados no Data Lake Store)](data-lake-store-secure-data.md)

