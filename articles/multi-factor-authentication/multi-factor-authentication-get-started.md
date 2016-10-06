<properties 
    pageTitle="Multi-Factor Authentication do Azure - Introdução"
    description="Escolha a solução de segurança de autenticação multifator adequada para si ao perguntar o que está a tentar proteger e onde estão localizados os seus utilizadores.  Em seguida, selecione a nuvem, o servidor MFA ou o AD FS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>


#Escolha a sua solução de segurança multifator

Uma vez que existem vários tipos de Multi-Factor Authentication do Azure, é necessário determinar algumas ações para descobrir qual a versão mais adequada a utilizar.  Essas ações são:

-   [O que estou a tentar proteger](#what-am-i-trying-to-secure)
-   [Onde estão localizados os utilizadores](#where-are-the-users-located)

As secções seguintes irão fornecer orientação sobre como determinar cada uma destas ações.

## O que estou a tentar proteger?

Para determinar a solução de autenticação multifator correta, primeiro é necessário responder à pergunta sobre o que está a tentar proteger com um segundo método de autenticação.  É uma aplicação que está no Azure?  Ou é um sistema de acesso remoto, por exemplo.  Ao determinar o que estamos a tentar proteger, procuramos responder à pergunta sobre onde a autenticação multifator tem de ser ativada.  


O que está a tentar proteger| Multi-Factor Authentication na nuvem|Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Aplicações Microsoft originais|* |* |
Aplicações Saas na galeria de aplicações|* |* |
Aplicações IIS publicadas através do Proxy de Aplicação do Azure AD|* |* |
Aplicações IIS não publicadas através do Proxy de Aplicação do Azure AD | |* |
Acesso remoto, tais como VPN, RDG| |* |



## Onde estão localizados os utilizadores

Em seguida, consoante onde estão localizados os utilizadores, podemos determinar a solução correta a utilizar, se é autenticação multifator na nuvem ou no local através do Servidor MFA.



Localização do Utilizador| Solução
------------- | :------------- |
Azure Active Directory| Multi-Factor Authentication na nuvem|
Azure AD e AD no local utilizando federação com o AD FS| O MFA na nuvem e o Servidor MFA são opções disponíveis
Azure AD e AD no local utilizando DirSync, Azure AD Sync, Azure AD Connect - sem sincronização de palavras-passe|O MFA na nuvem e o Servidor MFA são opções disponíveis
Azure AD e AD no local utilizando DirSync, Azure AD Sync, Azure AD Connect - com sincronização de palavras-passe|Multi-Factor Authentication na nuvem
Active Directory no local|Servidor Multi-Factor Authentication

A tabela seguinte é uma comparação das funcionalidades do Multi-Factor Authentication na nuvem e do Servidor Multi-Factor Authentication.

 | Multi-Factor Authentication na nuvem | Servidor Multi-Factor Authentication
------------- | :-------------: | :-------------: |
Notificação da aplicação móvel como um segundo fator | ● | ● |
Código de verificação da aplicação móvel como um segundo fator | ● | ●
Chamada telefónica como segundo fator | ● | ●
SMS unidirecional como segundo fator | ● | ●
SMS bidirecional como segundo fator |  | ●
Tokens de Hardware como segundo fator |  | ●
Palavras-passe da aplicação para os clientes que não suportam MFA | ● |  
Controlo de administração sobre métodos de autenticação | ● | ●
Modo PIN |  | ●
Alerta de fraudes | ● | ●
Relatórios do MFA | ● | ●
Omissão de Uso Individual |  | ●
Saudações personalizadas para chamadas telefónicas | ● | ●
ID do autor da chamada personalizável para chamadas telefónicas | ● | ●
IPs Fidedignos | ● | ●
Memorizar MFA para dispositivos fidedignos  | ● |  
Acesso condicional | ● | ●
Cache |  | ●

Agora que determinámos se deve utilizar a autenticação multifator na nuvem ou o Servidor MFA no local, podemos começar a configurar e a utilizar o Multi-Factor Authentication do Azure.   **Selecione o ícone que representa o seu cenário!**

<center>




[![Nuvem](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Sep16_HO4-->


