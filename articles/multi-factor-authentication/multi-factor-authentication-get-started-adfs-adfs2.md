---
title: Utilizar o Servidor MFA do Azure com o AD FS 2.0 | Microsoft Docs
description: "Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS 2.0."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 96168849-241a-4499-a224-d829913caa7e
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 639f11856fcb0828db2375e428d9f7efe640b5d7


---
# <a name="secure-cloud-and-onpremises-resources-using-azure-multifactor-authentication-server-with-ad-fs-20"></a>Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0
Este artigo destina-se a organizações federadas com o Azure Active Directory e que pretendem proteger recursos que estão no local ou na nuvem. Proteja os seus recursos através do Servidor Multi-Factor Authentication do Azure e configure-o para funcionar com o AD FS, para que a verificação em dois passos seja acionada para pontos finais de elevado valor.

Esta documentação abrange a utilização do Servidor Multi-Factor Authentication do Azure com o AD FS 2.0.  Obtenha mais informações em [Proteger recursos na nuvem e no local através do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md).

## <a name="secure-ad-fs-20-with-a-proxy"></a>Proteger o AD FS 2.0 com um proxy
Para proteger o AD FS 2.0 com um proxy, instale o Servidor Multi-Factor Authentication do Azure no servidor proxy do AD FS e configure o Servidor.

### <a name="configure-iis-authentication"></a>Configurar a autenticação do IIS
1. No Servidor Multi-Factor Authentication do Azure, clique no ícone **Autenticação do IIS** no menu da esquerda.
2. Clique no separador **Baseado em Formulários**.
3. Clique no botão **Adicionar…** Adicionar...
   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Para detetar automaticamente as variáveis de nome de utilizador, palavra-passe e domínio, introduza o URL de Início de Sessão (por exemplo, https://sso.contoso.com/adfs/ls) na caixa de diálogo Autoconfigurar Web site Baseado em Formulários e clique em OK.
5. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada. Para obter informações adicionais sobre esta funcionalidade, veja o ficheiro de ajuda.
6. Se as variáveis da página não puderem ser detetadas automaticamente, clique no botão **Especificar Manualmente...** na caixa de diálogo Autoconfigurar Web site Baseado em Formulários.
7. Na caixa de diálogo Adicionar Web site Baseado em Formulários, introduza o URL para a página de início de sessão do AD FS no campo URL de Submissão (por exemplo, https://sso.contoso.com/adfs/ls) e introduza um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel. Consulte o ficheiro de ajuda para obter mais informações sobre o URL de Submissão.
8. Defina o formato do Pedido como “POST ou GET”.
9. Introduza a variável de Nome de Utilizador (ctl00$ContentPlaceHolder1$UsernameTextBox) e a variável de Palavra-passe (ctl00$ContentPlaceHolder1$PasswordTextBox). Se a página de início de sessão baseada em formulários apresentar uma caixa de texto do domínio, introduza também a variável de Domínio. Poderá ter de navegar para a página de início de sessão num browser; clique com o botão direito do rato na página e selecione **Ver Origem** para localizar os nomes das caixas de entrada na página de início de sessão.
10. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada.
    <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Clique no botão **Avançadas...** para rever as definições avançadas. Pode configurar as definições, incluindo a capacidade de selecionar um ficheiro de página de recusa personalizado, colocar em cache as autenticações bem-sucedidas no site utilizando cookies e selecionar o modo de autenticação das credenciais principais.
12. Uma vez que é provável que o servidor proxy do AD FS não esteja associado ao domínio, pode utilizar o LDAP para ligar ao seu controlador de domínio para importação e pré-autenticação de utilizadores. Na caixa de diálogo Web site Baseado em Formulários Avançado, clique no separador **Autenticação Primária** e selecione **Enlace de LDAP** para o tipo de autenticação Pré-autenticação.
13. Quando terminar, clique no botão **OK** para regressar à caixa de diálogo Adicionar Web site Baseado em Formulários. Consulte o ficheiro de ajuda para obter mais informações sobre as definições avançadas.
14. Clique no botão **OK** para fechar a caixa de diálogo.
15. Assim que as variáveis de URL e página forem detetadas ou introduzidas, os dados do site são apresentados no painel Baseado em Formulários.
16. Clique no separador **Módulo Nativo** e selecione o servidor, o site no qual o proxy do AD FS está a ser executado (como “Site predefinido”) ou a aplicação do proxy do AD FS (como “ls” em “adfs”) para ativar o plug-in do IIS no nível pretendido.
17. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã.
18. A autenticação do IIS está agora ativada.

### <a name="configure-directory-integration"></a>Configurar a integração de diretórios
Ativou a autenticação do IIS, mas para executar a pré-autenticação para o Active Directory (AD) através de LDAP, tem de configurar a ligação LDAP para o controlador de domínio.

1. Clique no ícone **Integração de Diretórios**.
2. No separador Definições, selecione o botão de opção **Utilizar configuração de LDAP específica**.
   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Clique no botão **Editar…** Adicionar...
4. Na caixa de diálogo Editar Configuração de LDAP, preencha os campos com as informações necessárias para ligar ao controlador de domínio do AD. As descrições dos campos estão incluídas na tabela abaixo. Estas informações também estão incluídas no ficheiro de ajuda do Servidor Multi-Factor Authentication do Azure.
5. Teste a ligação LDAP, clicando no botão **Testar**.
   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Se o teste de ligação LDAP for concluído com êxito, clique no botão **OK**.

### <a name="configure-company-settings"></a>Configurar definições da empresa
1. Em seguida, clique no ícone **Definições da Empresa** e selecione o separador **Resolução de Nomes de Utilizador**.
2. Selecione o botão de opção **Utilizar o atributo de identificador exclusivo de LDAP para nomes de utilizador correspondentes**.
3. Se os utilizadores introduzirem o respetivo nome de utilizador no formato "domínio\nomedeutilizador", o Servidor tem de conseguir retirar o domínio do nome de utilizador quando criar a consulta de LDAP. Isso pode ser feito através de uma definição de registo.
4. Abra o editor de registo e aceda a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor num servidor de 64 bits. Se estiver num servidor de 32 bits, execute "Wow6432Node" fora do caminho. Crie uma chave de registo DWORD denominada “UsernameCxz_stripPrefixDomain” e defina o valor como 1. O Multi-Factor Authentication do Azure está agora a proteger o proxy do AD FS.

Certifique-se de que os utilizadores foram importados do Active Directory para o Servidor. Veja a [secção IPs Fidedignos](#trusted-ips) se pretender adicionar endereços IP internos à lista de permissões para que a verificação em dois passos não seja precisa ao iniciar sessão no site a partir dessas localizações.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct sem proxy
Pode proteger o AD FS quando o proxy do AD FS não é utilizado. Instale o Servidor Multi-Factor Authentication do Azure no servidor do AD FS e configure o Servidor de acordo com os seguintes passos:

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone **Autenticação do IIS** no menu da esquerda.
2. Clique no separador **HTTP**.
3. Clique no botão **Adicionar…** Adicionar...
4. Na caixa de diálogo Adicionar URL Base, introduza o URL para o site do AD FS onde é feita a autenticação HTTP (por exemplo, like https://sso.domain.com/adfs/ls/auth/integrated) no campo URL Base. Em seguida, introduza um Nome da aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
5. Se pretender, ajuste o Tempo limite de inatividade e os Tempos máximos de sessão.
6. Selecione a caixa de correspondência de utilizador **Exigir autenticação Azure Multi-Factor Authentication** se todos os utilizadores tiverem sido ou forem importados para o Servidor e forem sujeitos à verificação em dois passos. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor e/ou forem excluídos da verificação em dois passos, deixe a caixa desmarcada. Para obter informações adicionais sobre esta funcionalidade, veja o ficheiro de ajuda.
7. Selecione a caixa da cache de cookies, se pretender.
   <center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Clique no botão **OK**.
9. Clique no separador **Módulo Nativo** e selecione o servidor, o site (como “Site Predefinido”) ou a aplicação do AD FS (como “ls” em “adfs”) para ativar o plug-in do IIS no nível pretendido.
10. Clique na caixa **Ativar autenticação do IIS** na parte superior do ecrã. O Multi-Factor Authentication do Azure está agora a proteger o AD FS.

Certifique-se de que os utilizadores foram importados do Active Directory para o Servidor. Veja a secção IPs Fidedignos se pretender adicionar endereços IP internos à lista de permissões para que a verificação em dois passos não seja precisa ao iniciar sessão no site a partir dessas localizações.

## <a name="trusted-ips"></a>IPs Fidedignos
Os IPs Fidedignos permitem aos utilizadores ignorar o Multi-Factor Authentication do Azure para pedidos de sites com origem em sub-redes ou endereços IP específicos. Por exemplo, pode querer excluir utilizadores da verificação em dois passos quando iniciam sessão a partir do escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos.

### <a name="to-configure-trusted-ips"></a>Para configurar IPs fidedignos
1. Na secção Autenticação do IIS, clique no separador **IPs Fidedignos**.
2. Clique no botão **Adicionar…** Adicionar...
3. Quando for apresentada a caixa de diálogo Adicionar IPs Fidedignos, selecione o botão de opção **IP único**, **Intervalo de IPs** ou **Sub-rede**.
4. Introduza o endereço IP, o intervalo de endereços IP ou a sub-rede que deve colocar na lista de permissões. Se introduzir uma sub-rede, selecione a Máscara de rede adequada e clique no botão **OK**. O IP fidedigno foi agora adicionado.

<center>![Configuração](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Nov16_HO2-->


