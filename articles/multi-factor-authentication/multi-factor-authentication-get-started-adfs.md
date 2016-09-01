<properties 
    pageTitle="Introdução ao Multi-Factor Authentication do Azure e aos Serviços de Federação do Active Directory" 
    description="Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="get-started-article" 
    ms.date="08/04/2016" 
    ms.author="billmath"/>

# Introdução ao Multi-Factor Authentication do Azure e aos Serviços de Federação do Active Directory



<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se a sua organização tiver federado o seu Active Directory no local com o Azure Active Directory através do AD FS, estão disponíveis as duas seguintes opções para utilizar o Multi-Factor Authentication do Azure.

- Proteger recursos da nuvem com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory 
- Proteger recursos na nuvem e no local com o Servidor Multi-Factor Authentication do Azure 

A tabela seguinte resume a experiência de autenticação entre proteger recursos com o Multi-Factor Authentication do Azure e o AD FS

|Experiência de Autenticação - Aplicações baseadas no browser|Experiência de Autenticação - Aplicações não baseadas no browser
:------------- | :------------- | :------------- |
Proteger recursos do Azure AD com o Multi-Factor Authentication do Azure |<li>O primeiro fator de autenticação é efetuado no local com o AD FS.</li> <li>O segundo fator é um método telefónico efetuado através da autenticação na nuvem.</li>|Os utilizadores finais podem utilizar palavras-passe de aplicação para iniciar sessão.
Proteger recursos do Azure AD com os Serviços de Federação do Active Directory |<li>O primeiro fator de autenticação é efetuado no local com o AD FS.</li><li>O segundo fator é executado no local honrando a afirmação.</li>|Os utilizadores finais podem utilizar palavras-passe de aplicação para iniciar sessão.

Advertências com palavras-passe de aplicação para utilizadores federados: 

- A Palavra-passe de Aplicação é verificada utilizando a autenticação na nuvem e, por conseguinte, ignora as federações. A federação apenas é utilizada ativamente ao definir a Palavra-passe de Aplicação.
- As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
- Perde a capacidade de registos de autenticação no local para a Palavra-passe de Aplicação.
- A desativação/eliminação da conta pode demorar até 3 horas para o Dirsync, atrasando a desativação/eliminação da palavra-passe de aplicação na identidade da nuvem.

Para obter informações sobre como configurar o Multi-Factor Authentication do Azure ou o Servidor Multi-Factor Authentication do Azure com o AD FS, veja o seguinte:

- [Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 



<!--HONumber=ago16_HO4-->


