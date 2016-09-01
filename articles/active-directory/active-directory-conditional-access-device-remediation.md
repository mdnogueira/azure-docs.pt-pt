<properties
    pageTitle="Resolução de problemas: Problemas difíceis de resolver | Microsoft Azure"
    description="Este tópico ajuda a identificar se existem passos de remediação que pode seguir para obter acesso à aplicação que pretende."
    services="active-directory"
    keywords="acesso condicional baseado no dispositivo, registo de dispositivos, ativar o registo de dispositivos, registo de dispositivos e MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# Resolução de problemas: Problemas difíceis de resolver

Obteve um página com acesso negado ao aceder a uma aplicação como o Office 365 SharePoint Online.  
Agora, o que deve fazer?

Este guia ajuda-o a identificar se existem passos de remediação que pode seguir para obter acesso à aplicação que pretende.



Em que plataforma de dispositivo está a ser executado o dispositivo?
A resposta a esta pergunta determina a secção adequada deste tópico para si:
 

-   Dispositivo Windows

-   Dispositivo iOS (iPhone ou iPad)

-   Dispositivo Android

## Acesso a partir de um dispositivo Windows

Se o dispositivo for do Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, escolha a causa adequada ao identificar a página obtida ao tentar aceder à aplicação.

### O dispositivo não está registado

Se o dispositivo não estiver registado no Azure Active Directory e a aplicação estiver protegida por uma política baseada no dispositivo, poderá ver uma página com o seguinte conteúdo:

![Cenário](./media/active-directory-conditional-access-device-remediation/01.png "Scenario")

 

Se o seu dispositivo estiver associado a um domínio do Active Directory da sua organização, pode tentar o seguinte:

1.  Certifique-se de que tem sessão iniciada no Windows com a sua conta profissional (conta do Active Directory).

2.  Ligue à sua rede empresarial através de VPN ou Acesso Direto.

3.  Assim que estiver ligado, bloqueie a sua sessão do Windows com a tecla Windows + tecla “L”.

4.  Desbloqueie a sua sessão do Windows, introduzindo as credenciais da sua conta profissional.

5.  Aguarde um minuto e tente aceder novamente à aplicação.

6.  Se obtiver a mesma página, contacte o administrador e forneça os detalhes depois de clicar na ligação “Mais detalhes”.

Se o dispositivo não estiver associado a um domínio e executar o Windows 10, tem duas opções: 

1. Executar a Associação do Azure AD
2. Adicionar a sua conta profissional ou escolar ao Windows. 

Para obter informações sobre as diferenças entre as duas, veja [Utilizar dispositivos Windows 10 na sua área de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar a Associação do Azure AD, faça o seguinte (não disponível no Windows Phone):

**Atualização de Aniversário do Windows 10**   

1.  Inicie a aplicação Definições.

2.  Aceda a “Contas” e, em seguida, a “Aceder a escola ou trabalho”.

3.  Clique em “Ligar”.

4.  Escolha “Associar este dispositivo ao Azure AD”, na parte inferior da página.

5.  Autentique com a sua organização, forneça a prova MFA se for necessário e siga os passos até à conclusão.

6.  Termine a sessão e inicie sessão com a sua conta profissional.

7.  Tente aceder novamente à aplicação.




**Atualização do Windows 10 de Novembro de 2015**


1.  Inicie a aplicação Definições.

2.  Aceda a “Sistema” e, em seguida, “Sobre”.
    
3.  Clique em “Associar ao Azure AD”.

4.  Autentique com a sua organização, forneça a prova MFA se for necessário e siga os passos até à conclusão.

5.  Termine a sessão e inicie sessão com a sua conta profissional (conta Azure AD).

6.  Tente aceder novamente à aplicação.


Para adicionar a sua conta profissional ou escolar ao Windows, faça o seguinte:

**Atualização de Aniversário do Windows 10**   

1.  Inicie a aplicação Definições.

2.  Aceda a “Contas” e, em seguida, a “Aceder a escola ou trabalho”.

3.  Clique em “Ligar”.

4.  Autentique com a sua organização, forneça a prova MFA se for necessário e siga os passos até à conclusão.

5.  Tente aceder novamente à aplicação.    


**Atualização do Windows 10 de Novembro de 2015**
    
1.  Inicie a aplicação Definições.
2.  Aceda a “Contas” e, em seguida, “As suas contas”.
3.  Clique em “Adicionar conta profissional ou escolar”.
4.  Autentique com a sua organização, forneça a prova MFA se for necessário e siga os passos até à conclusão.
5.  Tente aceder novamente à aplicação.

Se o dispositivo não estiver associado a um domínio e executar o Windows 8.1, pode fazer a Associação à Área de Trabalho e inscrevê-lo no Microsoft Intune, fazendo o seguinte:

1.  Inicie as Definições do PC.

2.  Aceda a “Rede” e, em seguida, a “Área de Trabalho”.

3.  Clique em “Associar”.

4.  Autentique com a sua organização, forneça a prova MFA se for necessário e siga os passos até à conclusão.

5.  Clique em “Ativar”.

6.  Aguarde até à conclusão.

7.  Tente aceder novamente à aplicação.


## Browser não suportado

Se estiver a aceder à aplicação a partir dos seguintes browsers, verá uma página semelhante à página mostrada abaixo:

1.  Chrome, Firefox ou qualquer outro browser que não seja Microsoft Edge ou o Microsoft Internet Explorer no Windows 10 ou Windows Server 2016.

2.  Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.
 

![Cenário](./media/active-directory-conditional-access-device-remediation/02.png "Scenario")


A única remediação é utilizar um browser suportado para a plataforma do dispositivo.

## Acesso a partir de um dispositivo iOS

Volte em breve para obter instruções para iPhones ou iPads.

## Acesso a partir de um dispositivo Android

Volte em breve para obter instruções para telemóveis ou tablets Android.


## Passos seguintes

[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=ago16_HO4-->


