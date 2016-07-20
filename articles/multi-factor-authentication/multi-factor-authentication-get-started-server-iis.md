<properties 
    pageTitle="Autenticação do IIS e Servidor Multi-Factor Authentication do Azure" 
    description="Esta é a página do Multi-Factor Authentication do Azure que irá ajudar a implementar a Autenticação do IIS e o Servidor Multi-Factor Authentication do Azure." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Autenticação do IIS

A secção Autenticação do IIS do Servidor Multi-Factor Authentication do Azure permite ativar e configurar a autenticação do IIS para integração com aplicações Web do Microsoft IIS. O Servidor Multi-Factor Authentication do Azure instala um plug-in que pode filtrar os pedidos efetuados para o servidor Web do IIS para poder adicionar o Multi-Factor Authentication do Azure. O plug-in do IIS fornece suporte para Autenticação Baseada em Formulários e Autenticação HTTP Integrada do Windows. Também podem ser configurados IPs Fidedignos para excluir os endereços IP internos da autenticação de dois fatores. 


![Autenticação do IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Utilizar a Autenticação do IIS Baseada em Formulários com o Servidor Multi-Factor Authentication do Azure

Para proteger uma aplicação Web IIS que utilize autenticação baseada em formulários, instale o Servidor Multi-Factor Authentication do Azure no servidor Web do IIS e configure o Servidor consoante o procedimento seguinte.

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do IIS no menu esquerdo.
2. Clique no separador Baseado em Formulários.
3. Clique no botão Adicionar...
4. Para detetar automaticamente as variáveis do nome de utilizador, palavra-passe e domínio, introduza o URL de Início de Sessão (por exemplo, https://localhost/contoso/auth/login.aspx) na caixa de diálogo Autoconfigurar Web site Baseado em Formulários e clique em OK.
5. Selecione a caixa de correspondência de utilizador Exigir autenticação Multi-Factor Authentication se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada.
6. Se as variáveis da página não puderem ser detetadas automaticamente, clique no botão Especificar Manualmente... na caixa de diálogo Autoconfigurar Web site Baseado em Formulários.
7. Na caixa de diálogo Adicionar Web Site Baseado em Formulários, introduza o URL para a página de início de sessão no campo URL de Submissão e introduza um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel. Consulte o ficheiro de ajuda para obter mais informações sobre o URL de Submissão. 
8. Selecione o formato correto do Pedido. Está definido como "POST ou GET" para a maioria das aplicações Web.
9. Introduza a variável de Nome de Utilizador, Palavra-passe e Domínio (se aparecer na página de início de sessão). Poderá ter de navegar para a página de início de sessão num browser; clique com o botão direito do rato na página e selecione "Ver Origem" para localizar os nomes das caixas de introdução na página.
10. Selecione a caixa de correspondência de utilizador Exigir autenticação Azure Multi-Factor Authentication se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada. Consulte o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade.
11.  Clique no botão Avançadas... para rever as definições avançadas, incluindo a capacidade de selecionar um ficheiro de página de recusa personalizado, colocar em cache as autenticações bem-sucedidas para o site durante um período de tempo utilizando cookies e selecionar o modo de autenticação das credenciais principais relativamente ao Domínio do Windows, um diretório LDAP ou um servidor RADIUS. Quando terminar, clique no botão OK para regressar à caixa de diálogo Adicionar Web Site Baseado em Formulários. Consulte o ficheiro de ajuda para obter mais informações sobre as definições avançadas.
12. Clique no botão OK.
13. Assim que as variáveis de URL e página forem detetadas ou introduzidas, os dados do site serão apresentados no painel Baseado em Formulários.
14. Veja a secção Ativar Plug-ins do IIS para o Servidor Multi-Factor Authentication do Azure diretamente abaixo para concluir a configuração da autenticação do IIS. 

## Utilizar a Autenticação Integrada do Windows com o Servidor Multi-Factor Authentication do Azure

Para proteger uma aplicação Web IIS que utilize autenticação HTTP integrada do Windows, instale o Servidor Multi-Factor Authentication do Azure no servidor Web do IIS e configure o Servidor consoante o procedimento seguinte. 

1. No Servidor Multi-Factor Authentication do Azure, clique no ícone Autenticação do IIS no menu esquerdo.
2. Clique no separador HTTP. Clique no separador Baseado em Formulários.
3. Clique no botão Adicionar...
4. Na caixa de diálogo Adicionar URL Base, introduza o URL para o site onde é efetuada a autenticação HTTP (por exemplo, http://localhost/owa) no campo URL Base e introduza um nome de Aplicação (opcional). O nome da Aplicação aparece nos relatórios do Multi-Factor Authentication do Azure e poderá ser apresentado nas mensagens de autenticação SMS ou da Aplicação Móvel.
5. Ajuste o tempo limite de inatividade e os tempos máximos de sessão, se a predefinição não for suficiente.
6. Selecione a caixa de correspondência de utilizador Exigir autenticação Multi-Factor Authentication se todos os utilizadores tiverem sido ou forem importados para o Servidor e estiverem sujeitos à autenticação multifator. Se um número significativo de utilizadores ainda não tiverem sido importados para o Servidor de e/ou estiverem excluídos da autenticação multifator, deixe a caixa desmarcada. Consulte o ficheiro de ajuda para obter informações adicionais sobre esta funcionalidade. 
7. Selecione a caixa da cache de cookies, se pretender.
8. Clique no botão OK.
9. Veja a secção [Ativar Plug-ins do IIS para o Servidor Multi-Factor Authentication do Azure](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) diretamente abaixo para concluir a configuração da autenticação do IIS. 


## Ativar Plug-ins do IIS para o Servidor Multi-Factor Authentication do Azure

Assim que tiver configurado os URLs e as definições de autenticação HTTP ou Baseada em Formulários, tem de selecionar as localizações onde os plug-ins do IIS do Multi-Factor Authentication do Azure devem ser carregados e ativados no IIS. Utilize o seguinte procedimento:

1. Se tiver o IIS 6, clique no separador ISAPI e selecione o site no qual a aplicação Web está a ser executada (por exemplo, Site Predefinido) para ativar o plug-in do filtro ISAPI do Multi-Factor Authentication do Azure para esse site.
2. Se tiver o IIS 7 ou superior, clique no separador Módulo Nativo e selecione o servidor, os sites ou as aplicações para ativar o plug-in do IIS nos níveis pretendidos.
3. Clique na caixa de autenticação Ativar o IIS na parte superior do ecrã. O Multi-Factor Authentication do Azure está agora a proteger a aplicação IIS selecionada. Certifique-se de que os utilizadores foram importados para o Servidor. Veja a secção IPs Fidedignos abaixo se pretender adicionar endereços IP internos à lista de permissões para que a autenticação de dois fatores não é necessária ao iniciar sessão no site a partir dessas localizações. 


## IPs Fidedignos

Os IPs Fidedignos permitem aos utilizadores ignorar o Multi-Factor Authentication do Azure para pedidos de sites com origem em sub-redes ou endereços IP específicos. Por exemplo, pode pretender excluir utilizadores do Multi-Factor Authentication do Azure ao iniciar sessão a partir do escritório. Para tal, especifique a sub-rede do escritório como uma entrada de IPs Fidedignos. Para configurar IPs Fidedignos, utilize o seguinte procedimento:

1. Na secção Autenticação do IIS, clique no separador IPs Fidedignos. 
2. Clique no botão Adicionar...
3. Quando for apresentada a caixa de diálogo Adicionar IPs Fidedignos, selecione o botão de opção IP único, intervalo de IPs ou sub-rede.
4. Introduza o endereço IP, o intervalo de endereços IP ou a sub-rede a colocar na lista de permissões. Se introduzir uma sub-rede, selecione a Máscara de rede adequada e clique no botão OK. A lista de permissões foi agora adicionada.



<!--HONumber=Jun16_HO2-->


