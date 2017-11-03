---
title: "Do Azure Active Directory dos serviços de domínio: Cenários de implementação | Microsoft Docs"
description: "Cenários de implementação de serviços de domínio do Azure AD"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: b73bfc7703d79681f0de345f4ec994da540aa2a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>Cenários de implementação e casos de utilização
Nesta secção, vamos ver alguns cenários e casos de utilização que beneficiam dos serviços de domínio do Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Administração segura, fácil de máquinas virtuais do Azure
Pode utilizar os serviços de domínio do Active Directory do Azure para gerir as máquinas virtuais do Azure de forma simplificada. Máquinas virtuais do Azure pode ser associadas ao domínio gerido, permitindo-lhe utilizar credenciais AD da sua empresa para iniciar sessão. Esta abordagem ajuda a evitar problemas de gestão de credenciais, tais como a manutenção de contas de administrador local em cada uma das suas máquinas virtuais do Azure.

Também podem ser geridas e protegidas através da política de grupo de máquinas de virtuais de servidor que estão associadas ao domínio gerido. Pode aplicar linhas de base de segurança necessárias para as máquinas virtuais do Azure e bloqueie-los de acordo com as diretrizes de segurança da empresa. Por exemplo, pode utilizar as capacidades de gestão de política de grupo para restringir os tipos de aplicações que podem ser iniciadas nestas máquinas virtuais.

![Administração simplificada de máquinas virtuais do Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Como servidores e outra infraestrutura atingir o fim-de-vida, Contoso está a mover muitas aplicações atualmente alojadas no local para a nuvem. Os seus padrão IT atual exige deste que servidores que alojam as aplicações da empresa tem de ser associado a um domínio e geridos através da política de grupo. Do contoso administrador de TI prefers para as máquinas virtuais de adesão ao domínio implementadas no Azure, para facilitar a administração. Como resultado, os administradores e utilizadores podem iniciar sessão utilizando as respetivas credenciais empresariais. Ao mesmo tempo, podem ser configuradas máquinas para estar em conformidade com as linhas de base de segurança necessárias através da política de grupo. Contoso prefere não tem de implementar, monitorizar e gerir controladores de domínio do Azure para proteger máquinas virtuais do Azure. Por conseguinte, os serviços de domínio do Azure AD é uma excelente opção para este caso de utilização.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Domínios geridos fornecidos pelos serviços de domínio do Azure AD fornecem uma estrutura de UO (unidade organizacional) simples único por predefinição. Todas as máquinas associados a um domínio de residir numa única UO simples. No entanto pode optar por criar UOs personalizados.
* Serviços de domínio do Azure AD suporta a política de grupo simples sob a forma de um incorporada GPO cada para os utilizadores e computadores contentores. Pode criar GPOs personalizados e segmentá-los para UOs personalizados.
* Serviços de domínio do Azure AD suporta o esquema de objeto de computador do AD base. Não é possível expandir o esquema do objeto de computador.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Comparação de precisão-e-shift uma aplicação no local que utiliza a autenticação do enlace LDAP para serviços de infraestrutura do Azure
![Enlace de LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso tem uma aplicação no local que foi comprada um ISV e estiver muitos anos, há. A aplicação está em modo de manutenção pelo ISV e pedir alterações para a aplicação é prohibitively dispendioso para Contoso. Esta aplicação tem um baseada na web front-end que recolhe as credenciais de utilizador utilizando um formulário web e, em seguida, efetua a autenticação de utilizadores através de um enlace LDAP para o Active Directory empresarial. Contoso pretende migrar esta aplicação aos serviços de infraestrutura do Azure. É necessário que a aplicação funciona tal como está, sem necessidade de alterações. Além disso, os utilizadores devem ser capazes de autenticar com as respetivas credenciais empresariais existentes e sem ter de reparametrização dos utilizadores para efetuar ações de forma diferente. Por outras palavras, os utilizadores finais devem ser oblivious de onde a aplicação está em execução e a migração transparente aos mesmos.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que a aplicação não tem de modificar/escrita ao diretório. Acesso de escrita LDAP para domínios geridos fornecida pelos serviços de domínio do Azure AD não é suportado.
* Não é possível alterar as palavras-passe diretamente contra o domínio gerido. Os utilizadores finais podem alterar a palavra-passe: utilizando o mecanismo de alteração de palavra-passe self-service do Azure AD ou contra o diretório no local. Estas alterações são automaticamente sincronizados e está disponível no domínio gerido.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Comparação de precisão e shift de leitura de uma aplicação no local que utiliza o LDAP para aceder ao diretório nos serviços de infraestrutura do Azure
Contoso tem uma aplicação de linha de negócio (LOB) no local que foi desenvolvida quase um decade há. Esta aplicação com suporte de diretório e foi concebida para funcionar com o Windows Server AD. A aplicação utiliza o LDAP (Lightweight Directory acesso Protocol) para ler informações/atributos sobre os utilizadores do Active Directory. A aplicação não modificar os atributos ou caso contrário escrevam no diretório. Contoso pretende migrar esta aplicação aos serviços de infraestrutura do Azure e extinguir o hardware no local de envelhecimento aloja atualmente esta aplicação. A aplicação não pode ser rescrita para utilizar o diretório moderno APIs, tais como a Azure AD Graph API baseado em REST. Por conseguinte, uma opção de comparação de precisão e shift pretender whereby a aplicação pode ser migrada para executar na nuvem, sem modificar o código ou conversão a aplicação.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que a aplicação não tem de modificar/escrita ao diretório. Acesso de escrita LDAP para domínios geridos fornecida pelos serviços de domínio do Azure AD não é suportado.
* Certifique-se de que a aplicação não precisa de um esquema do Active Directory expandido personalizada. Extensões de esquema não são suportadas nos serviços de domínio do Azure AD.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrar uma aplicação de serviço ou daemon no local para serviços de infraestrutura do Azure
Algumas aplicações é constituída por várias camadas, onde um dos escalões tem de efetuar chamadas autenticadas para uma camada de back-end como uma camada de base de dados. Contas de serviço do Active Directory são frequentemente utilizadas para estes casos de utilização. Pode comparação de precisão e shift essas aplicações em serviços de infraestrutura do Azure e utilizar serviços de domínio do Azure AD para as necessidades de identidade destas aplicações. Pode optar por utilizar a mesma conta de serviço que é sincronizada a partir do diretório no local ao Azure AD. Em alternativa, pode criar primeiro uma UO personalizada e, em seguida, criar uma conta de serviço separada nessa UO, implementar essas aplicações.

![Conta de serviço através de WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso tem uma aplicação de cofre software personalizada que inclua um front-end da web, um SQL server e um servidor FTP de back-end. Autenticação integrada do Windows de contas de serviço é utilizada para autenticar web front-end para o servidor FTP. O front-end da web está configurado para ser executado como uma conta de serviço. O servidor de back-end estiver configurado para autorizar o acesso da conta de serviço para o front-end da web. Contoso prefers para não tem de implementar uma máquina de virtual do controlador de domínio na nuvem para passar esta aplicação aos serviços de infraestrutura do Azure. Do contoso administrador de TI pode implementar os servidores que alojam o front-end da web, o SQL server e o servidor de FTP para máquinas virtuais do Azure. Estas máquinas, em seguida, estão associadas a um domínio gerido dos serviços de domínio do Azure AD. Em seguida, pode utilizam a mesma conta de serviço do respetivo diretório no local para efeitos de autenticação da aplicação. Esta conta de serviço é sincronizada para um domínio gerido dos serviços de domínio do Azure AD e está disponível para utilização.

**Notas de implementação**

Considere os seguintes pontos importantes para este cenário de implementação:

* Certifique-se de que a aplicação utiliza o nome de utilizador/palavra-passe para autenticação. Autenticação de certificado/smart card com base não é suportada pelos serviços de domínio do Azure AD.
* Não é possível alterar as palavras-passe diretamente contra o domínio gerido. Os utilizadores finais podem alterar a palavra-passe: utilizando o mecanismo de alteração de palavra-passe self-service do Azure AD ou contra o diretório no local. Estas alterações são automaticamente sincronizados e está disponível no domínio gerido.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Serviços de ambiente de trabalho remoto do Windows Server implementações no Azure
Pode utilizar os serviços de domínio do Azure AD para fornecer serviços de domínio do AD geridos para os seus servidores de ambiente de trabalho remotos implementados no Azure.

Para obter mais informações sobre este cenário de implementação, consulte como [integrar os serviços de domínio do Azure AD com a implementação de RDS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clusters do HDInsight associados a um domínio (pré-visualização)
Pode configurar um cluster do HDInsight do Azure que está associado a um domínio gerido dos serviços de domínio do Azure AD com Ranger Apache ativada. Criar e aplicar políticas de ramo de registo através do Apache Ranger e permitir que os utilizadores (por exemplo, cientistas de dados) ligar ao ramo de registo utilizando ferramentas baseadas em ODBC, por exemplo, Excel, Tableau etc. Microsoft está a trabalhar na adição de outras cargas de trabalho, como o HBase, Spark e Storm, para o HDInsight associados a um domínio em breve.

Para obter mais informações sobre este cenário de implementação, consulte como [configurar clusters do HDInsight associados a um domínio](../hdinsight/hdinsight-domain-joined-configure.md)
