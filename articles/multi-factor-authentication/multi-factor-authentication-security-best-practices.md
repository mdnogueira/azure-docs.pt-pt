---
title: "Melhores práticas de segurança para a MFA | Microsoft Docs"
description: "Este documento fornece as melhores práticas em torno da utilização do MFA do Azure com as contas do Azure"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 2be36bce1b4cffdab2d25d150bd5a0e8451e422d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Melhores práticas de segurança para utilizar o Azure multi-factor Authentication com contas do Azure AD

Verificação de dois passos é a escolha preferida para a maioria das organizações que pretendem melhorar o seu processo de autenticação. Azure multi-factor Authentication (MFA) ajuda as empresas cumpram os requisitos de segurança e conformidade ao fornecer uma experiência de início de sessão simple para os respetivos utilizadores. Este artigo abrange algumas sugestões que deve considerar quando planear a adoção do MFA do Azure.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Implementar o Azure MFA na nuvem

Existem duas formas de ativar a MFA do Azure para todos os seus utilizadores.

* Comprar licenças para cada utilizador (o MFA do Azure, Azure AD Premium, ou Enterprise Mobility + Security)
* Criar um fornecedor do multi-factor Auth e pagamento por utilizador ou por autenticação

### <a name="licenses"></a>Licenças
![O EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Se tiver o Azure AD Premium ou Enterprise Mobility + licenças de segurança, já ter MFA do Azure. A organização não tem nada adicional para expandir a capacidade de verificação de dois passos para todos os utilizadores. Só tem de atribuir uma licença a um utilizador e, em seguida, pode ativar a MFA.

Quando configurar o multi-factor Authentication, considere as sugestões seguintes:

* Não crie um fornecedor por autenticação multi-factor Auth. Se o fizer, podem acabar pagar para pedidos de verificação dos utilizadores que já tem licenças.
* Se não tiver licenças suficientes para todos os seus utilizadores, pode criar um fornecedor de autenticação do multi-factor por utilizador para o resto da sua organização. 
* Azure AD Connect é apenas necessário se estiver a sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD. Se utilizar um diretório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não precisa do Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Fornecedor do multi-factor Auth
![Fornecedor do multi-factor Auth](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se não tiver licenças, que incluem o MFA do Azure, pode criar um fornecedor de autenticação do MFA. 

Ao criar o fornecedor de autenticação, tem de selecionar um diretório e considere os seguintes detalhes:

* Não é necessário um diretório do Azure AD para criar um fornecedor do multi-factor Auth, mas depara-se mais funcionalidade com um. As seguintes funcionalidades estão ativadas quando associa o fornecedor de autenticação com um diretório do Azure AD:  
  * Expandir a verificação de dois passos para todos os utilizadores  
  * Oferecem funcionalidades adicionais, tais como o portal de gestão, saudações personalizadas e relatórios dos administradores globais.
* Se sincronizar o seu ambiente do Active Directory no local com um diretório do Azure AD, terá de DirSync ou o AAD Sync. Se utilizar um diretório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não terá DirSync ou o AAD Sync.
* Escolha o modelo de consumo que melhor se adapta às suas empresas. Depois de selecionar o modelo de utilização, não é possível alterá-la. Dois modelos são:
  * Por autenticação: encargos, de cada verificação. Utilize este modelo, se pretender que a verificação de dois passos para qualquer pessoa que acede a uma determinada aplicação, não para utilizadores específicos.
  * Por utilizador ativado: cobra, para cada utilizador que ativar para o MFA do Azure. Utilize este modelo, se tiver alguns utilizadores com o Azure AD Premium ou licenças Enterprise Mobility Suite e algumas sem.

### <a name="supportability"></a>Suportabilidade
Uma vez que a maioria dos utilizadores são habituar-se à utilização de apenas palavras-passe para autenticar, é importante que a sua empresa dá durante a todos os utilizadores sobre este processo. Esta deteção pode reduzir a probabilidade dos utilizadores chamar o suporte técnico para problemas secundários relacionadas com a MFA. No entanto, existem alguns cenários onde é necessário desativar temporariamente o MFA. Utilize as diretrizes seguintes para compreender como lidar com esses cenários:

* Preparar a sua equipa de suporte técnico para processar cenários em que o utilizador não é possível iniciar sessão porque a aplicação móvel ou por telefone não está a receber uma notificação ou uma chamada telefónica. O suporte técnico pode [ativar uma omissão de uso individual](multi-factor-authentication-whats-next.md#one-time-bypass) para permitir que um utilizador autenticar uma única vez, "Ignorar" verificação de dois passos. A omissão é temporária e expira após um número de segundos especificado.
* Considere o [capacidade de IPs fidedignos](multi-factor-authentication-whats-next.md#trusted-ips) na MFA do Azure como uma forma para minimizar a verificação de dois passos. Com esta funcionalidade, os administradores de um inquilino federado ou não geridos podem ignorar a verificação de dois passos para utilizadores que está a iniciar sessão na intranet local da empresa. As funcionalidades estão disponíveis para os inquilinos do Azure AD que possuem licenças do Azure AD Premium, o Enterprise Mobility Suite ou o Azure multi-factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Melhores práticas para uma implementação no local
Se a empresa decidiu tirar partido para ativar a MFA a própria infraestrutura, terá de implementar um servidor de autenticação multi-factor do Azure no local. Os componentes de servidor MFA são apresentados no diagrama seguinte:

![Predefinição componentes de servidor MFA: consola, o motor de sincronização, o portal de gestão, o serviço em nuvem](./media/multi-factor-authentication-security-best-practices/server.png) \*não instalado por predefinição \** instalado, mas não ativado por predefinição

Servidor de autenticação multi-factor do Azure pode proteger as nuvem recursos de recursos e no local utilizando Federação. Tem de ter o AD FS e o tiver federadas com o seu inquilino do Azure AD.
Quando configurar o servidor multi-factor Authentication, considere os seguintes detalhes:

* Se estiver a proteger recursos do Azure AD utilizando os serviços de Federação do Active Directory (AD FS), em seguida, o primeiro passo de verificação é efetuado no local utilizando o AD FS. O segundo passo é executado no local honrando a afirmação.
* Não tem de instalar o servidor multi-factor Authentication do Azure o servidor de Federação do AD FS. No entanto, o adaptador do multi-factor Authentication para o AD FS tem de estar instalado no Windows Server 2012 R2 em execução do AD FS. Pode instalar o servidor num computador diferente, desde que é uma versão suportada e instalar o adaptador AD FS separadamente no seu servidor de Federação do AD FS. 
* O Assistente de instalação do adaptador do multi-factor Authentication. o AD FS cria um grupo de segurança chamado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS a este grupo. Verifique se o grupo PhoneFactor Admins foi criado no controlador de domínio e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente ao grupo PhoneFactor Admins no seu controlador de domínio.

### <a name="user-portal"></a>Portal de Utilizador
O portal de utilizador permite que as capacidades de self-service e fornece um conjunto completo de capacidades de administração do utilizador. É executada num web site do servidor de informação Internet (IIS). Utilize as diretrizes seguintes para configurar este componente:

* Utilizar o IIS 6 ou superior
* Instalar e registar o ASP.NET v2.0.507207
* Certifique-se de que este servidor pode ser implementado numa rede de perímetro

### <a name="app-passwords"></a>Palavras-passe de Aplicação
Se a sua organização estiver federada para SSO, com o Azure AD e, se pretender utilizar o MFA do Azure, tenha em atenção os seguintes detalhes:

* A palavra-passe de aplicação é verificada pelo Azure AD e, por conseguinte, ignora Federação. A Federação só é utilizada quando configurar palavras-passe de aplicação.
* Para os utilizadores federados (SSO), as palavras-passe são armazenadas no id organizacional. Se o utilizador sai da empresa, informações de que tem de fluir para o id organizacional utilizando DirSync. A desativação/eliminação da conta pode demorar até três horas a sincronização, o qual atrasa a desativação/eliminação de palavras-passe de aplicação no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Sem capacidade de registo de auditoria/da autenticação no local está disponível para palavras-passe de aplicação.
* Algumas estruturas da arquitetura avançadas podem exigir utilizando uma combinação de nome de utilizador organizacional e palavras-passe e palavras-passe de aplicação ao utilizar a verificação de dois passos com clientes, consoante onde se autenticar. Para clientes que se autenticarem numa infraestrutura no local, teria de utilizar um nome de utilizador organizacional e a palavra-passe. Para clientes de autenticação no Azure AD, teria de utilizar a palavra-passe de aplicação.
* Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação. Se precisar de permitir que os utilizadores criem palavras-passe aplicação, selecione o **permitir aos utilizadores criar palavras-passe de aplicação para iniciar sessão em aplicações não baseadas no browser** opção.

## <a name="additional-considerations"></a>Considerações adicionais
Utilize esta lista para obter considerações adicionais e orientações para cada componente que é implementado no local:

- Configure a Multi-Factor Authentication com o [Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md).
- Defina e configure o Servidor MFA do Azure com [Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md).
- Configurar o servidor MFA do Azure com [autenticação IIS](multi-factor-authentication-get-started-server-iis.md).
- Configurar o servidor MFA do Azure com [autenticação do Windows](multi-factor-authentication-get-started-server-windows.md).
- Configurar o servidor MFA do Azure com [autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md).
- Configurar o servidor MFA do Azure com [Gateway de ambiente de trabalho remoto e o servidor de autenticação do multi-factor do Azure com o RADIUS](multi-factor-authentication-get-started-server-rdg.md).
- Configurar a sincronização entre o servidor MFA do Azure e [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md).
- [Implemente o Serviço Web de Aplicações Móveis do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server-webservice.md).
- [Avançadas de configuração de VPN com o Azure multi-factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) para aparelhos de Cisco ASA, Citrix Netscaler e Juniper/Pulse Secure VPN com RADIUS ou LDAP.

## <a name="next-steps"></a>Passos seguintes
Embora este artigo realça algumas melhores práticas para o Azure MFA, existem outros recursos que também pode utilizar ao planear a implementação de MFA. A lista abaixo tem alguns artigos chaves que podem ajudá-lo durante este processo:

* [Relatórios do multi-factor Authentication do Azure](multi-factor-authentication-manage-reports.md)
* [A experiência de registo de verificação de dois passos](multi-factor-authentication-end-user-first-time.md)
* [FAQ sobre o multi-factor Authentication do Azure](multi-factor-authentication-faq.md)

