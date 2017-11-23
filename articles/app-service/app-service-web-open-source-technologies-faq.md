---
title: "Aplicações web de tecnologias de open source perguntas mais frequentes do Azure | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre as tecnologias de open source na funcionalidade Web Apps do App Service do Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4fb443691e216169dd1322b96d77139ffde752d4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Tecnologias de open source perguntas mais frequentes para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas com tecnologias de open source para o [funcionalidade Web Apps do App Service do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>A minha base de dados de ClearDB está inativo. Como resolver isto?

Para problemas relacionados com a base de dados, contacte [ClearDB suporte](https://www.cleardb.com/developers/help/support). 

Para obter respostas a questões recorrentes sobre ClearDB, consulte [perguntas mais frequentes de ClearDB](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>Por que motivo não foi migrado a minha base de dados de ClearDB durante a migração a minha subscrição?

Quando efetuar a migração de recursos nas subscrições, aplicam-se algumas limitações. Uma base de dados ClearDB MySQL é um serviço de terceiros e não migrado durante uma migração de subscrição do Azure.

Se não gere a migração da base de dados MySQL antes de migrar os recursos do Azure, a base de dados ClearDB MySQL poderá estar indisponível. Para evitar isto, em primeiro lugar, manualmente migre a base de dados de ClearDB, e, em seguida, migre a subscrição do Azure para a sua aplicação web.

Para obter mais informações, consulte [perguntas mais frequentes para bases de dados ClearDB MySQL App Service do Azure](https://docs.microsoft.com/azure/store-cleardb-faq/).

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como ativar no registo para resolver problemas do PHP do PHP

Para ativar o registo do PHP:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu superior, selecione **consola de depuração** > **CMD**.
3. Selecione o **Site** pasta.
4. Selecione o **wwwroot** pasta.
5. Selecione o  **+**  ícone e, em seguida, selecione **novo ficheiro**.
6. Defina o nome de ficheiro para **. user.ini**.
7. Selecione o ícone de lápis junto a **. user.ini**.
8. No ficheiro, adicione este código:`log_errors=on`
9. Selecione **Guardar**.
10. Selecione o ícone de lápis junto a **wp-config.php**.
11. Altere o texto para o seguinte código:
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. No portal do Azure, no menu de aplicação web, reinicie a aplicação web.

Para obter mais informações, consulte [registos de erros de ativar o WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como se registar erros de aplicação de Python nas aplicações que estão alojadas no App Service?

Para capturar os erros da aplicação Python:

1. No portal do Azure, na sua aplicação web, selecione **definições**.
2. No **definições** separador, selecione **definições da aplicação**.
3. Em **as definições de aplicação**, introduza o seguinte par chave/valor:
    * Chave: WSGI_LOG
    * Valor: D:\home\site\wwwroot\logs.txt (introduza a sua escolha de nome de ficheiro)

Deverá ver erros no ficheiro logs.txt na pasta wwwroot.

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como posso alterar a versão da aplicação Node.js que está alojada no App Service?

Para alterar a versão da aplicação Node.js, pode utilizar uma das seguintes opções:

*   No portal do Azure, utilize **as definições de aplicação**.
    1. No portal do Azure, aceda à sua aplicação web.
    2. No **definições** painel, selecione **definições da aplicação**.
    3. No **as definições de aplicação**, pode incluir WEBSITE_NODE_DEFAULT_VERSION como a chave e a versão do Node.js que quiser como o valor.
    4. Aceda ao seu [consola Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
    5. Para verificar a versão do Node.js, introduza o seguinte comando:  
   ```
   node -v
   ```
*   Modificar o ficheiro iisnode.yml. Alterar a versão do Node.js no ficheiro iisnode.yml apenas define o ambiente de tempo de execução que o iisnode utiliza. O Kudu cmd e outros utilizadores continuam a utilizar a versão do Node.js que está definida no **as definições de aplicação** no portal do Azure.

    Para definir o iisnode.yml manualmente, crie um ficheiro iisnode.yml na sua pasta de raiz da aplicação. No ficheiro, incluem a seguinte linha:
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   Defina o ficheiro iisnode.yml utilizando Package. JSON durante a implementação de controlo de origem.
    O processo de implementação do controlo de origem do Azure envolve os seguintes passos:
    1. Move o conteúdo para a aplicação web do Azure.
    2. Cria um script de implementação de predefinição, se não existir um (deploy.cmd, ficheiros de .deployment) na pasta de raiz de aplicação web.
    3. Executa um script de implementação em que cria um ficheiro iisnode.yml se mencionar a versão do Node.js no ficheiro Package. JSON > motor`"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. O ficheiro iisnode.yml tem a seguinte linha de código:
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Posso ver a mensagem "Error estabelecer uma ligação de base de dados" na minha aplicação WordPress, que está alojada no serviço de aplicações. Como resolver isto?

Se vir este erro na sua aplicação WordPress do Azure, para ativar php_errors.log e debug.log, concluir os passos detalhados [registos de erros de ativar o WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Quando os registos estão ativados, recriar o erro e, em seguida, verifique os registos para ver se estiver a ficar sem ligações:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se vir este erro nos seus ficheiros debug.log ou php_errors.log, a aplicação está a exceder o número de ligações. Se estiver a alojar o ClearDB, verifique se o número de ligações que estão disponíveis no seu [plano de serviço](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como posso depurar uma aplicação Node.js que está alojada no App Service?

1.  Aceda ao seu [consola Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Aceda à pasta de registos de aplicação (D:\home\LogFiles\Application).
3.  No ficheiro logging_errors.txt, verifique a existência de conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como instalar módulos nativos do Python uma aplicação web do app Service ou a aplicação API?

Alguns pacotes não poderão instalar utilizando o pip no Azure. O pacote pode não estar disponível no índice de pacote do Python, ou um compilador poderão ser necessário (um compilador não está disponível no computador que está a executar a aplicação web no App Service). Para obter informações sobre como instalar os módulos nativos no serviço de aplicações web apps e API apps, consulte [módulos de instalar o Python no App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como implementar uma aplicação Django no App Service utilizando o Git e a nova versão do Python?

Para obter informações sobre a instalação do Django, consulte [implementar uma aplicação Django no App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão localizados os ficheiros de registo do Tomcat?

Para o Azure Marketplace e implementações personalizadas:

* Localização da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Ficheiros de interesse:
    * catalina. *aaaa-mm-dd*. log
    * Gestor de anfitrião. *aaaa-mm-dd*. log
    * localhost. *aaaa-mm-dd*. log
    * Gestor. *aaaa-mm-dd*. log
    * site_access_log. *aaaa-mm-dd*. log


Para o portal **as definições de aplicação** implementações:

* Localização da pasta: D:\home\LogFiles
* Ficheiros de interesse:
    * catalina. *aaaa-mm-dd*. log
    * Gestor de anfitrião. *aaaa-mm-dd*. log
    * localhost. *aaaa-mm-dd*. log
    * Gestor. *aaaa-mm-dd*. log
    * site_access_log. *aaaa-mm-dd*. log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como posso resolver erros de ligação do controlador JDBC?

Poderá ver a mensagem seguinte nos seus registos de Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o ficheiro de sqljdbc*.jar da sua pasta de aplicação/lib.
2. Se estiver a utilizar o servidor web personalizado Tomcat ou do Azure Marketplace Tomcat, copie este ficheiro. JAR na pasta de lib Tomcat.
3. Se pretende ativar Java do portal do Azure (selecione **1.8 do Java** > **servidor Tomcat**), copie o ficheiro jar do sqljdbc.* na pasta que é paralela à sua aplicação. Em seguida, adicione a seguinte definição de classpath ao ficheiro Web. config:

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que motivo ver erros quando posso tentativa de copiar os ficheiros de registo em direto?

Está prestes a copiar ficheiros de registo em direto para uma aplicação de Java (por exemplo, Tomcat), poderá ver este erro FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar consoante o cliente de FTP.

Todas as aplicações de Java tem este problema de bloqueio. O Kudu só suporta transferir este ficheiro enquanto a aplicação está em execução.

Parar a aplicação permite o acesso FTP para estes ficheiros.

Outra solução é escrever um WebJob é executado numa agenda e copia estes ficheiros para um diretório diferente. Para um projeto de exemplo, consulte o [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projeto.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde posso encontrar os ficheiros de registo para Jetty?

Para implementações personalizadas e de Marketplace, o ficheiro de registo é na pasta D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Tenha em atenção que a localização da pasta depende da versão de Jetty estiver a utilizar. Por exemplo, o caminho fornecido é aqui para Jetty 9.1.2. Procure jetty_*YYYY_MM_DD*. stderrout.log.

Para implementações de definição de aplicação do portais, o ficheiro de registo está a ser D:\home\LogFiles. Procure jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Pode enviar correio eletrónico da minha aplicação web do Azure?

Serviço de aplicações não possui uma funcionalidade de e-mail incorporado. Para alguns boas alternativas para enviar correio eletrónico da sua aplicação, consulte este [Stack Overflow debate](http://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Por que motivo o meu site WordPress redireciona para outro URL?

Se tiver migrado recentemente para o Azure, WordPress poderão redirecionar para o URL antigo do domínio. Isto é causado por uma definição na base de dados MySQL.

WordPress Camarada + é uma extensão de Site do Azure que pode utilizar para atualizar o URL de redirecionamento diretamente na base de dados. Para obter mais informações sobre como utilizar o WordPress Camarada +, consulte [WordPress ferramentas e a migração de MySQL com WordPress Camarada +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Em alternativa, se preferir o redirecionamento de atualizar manualmente o URL através de consultas SQL ou PHPMyAdmin, consulte o artigo [WordPress: Redireccionamento de URL incorreto](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como alterar o meu WordPress início de sessão palavra-passe de

Se tenha esquecido o WordPress início de sessão palavra-passe de, pode utilizar WordPress Camarada + atualizá-lo. Para repor a palavra-passe, instalar o WordPress Camarada + Azure extensão do Site e, em seguida, conclua os passos descritos no [WordPress ferramentas e a migração de MySQL com WordPress Camarada +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não é possível iniciar sessão no WordPress. Como resolver isto?

Caso se bloqueado depois de instalar recentemente um plug-in WordPress, poderá ter um plug-in defeituoso. WordPress Camarada + é uma extensão de Site do Azure que pode ajudá-lo desativar plug-ins no WordPress. Para obter mais informações, consulte [WordPress ferramentas e a migração de MySQL com WordPress Camarada +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como posso migrar a minha base de dados do WordPress?

Tem várias opções para migrar a base de dados MySQL que está ligada ao seu site WordPress:

* Os programadores: Utilizar o [linha de comandos ou PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Não-os programadores: Utilizar [WordPress Camarada +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como ajudar a tornar o WordPress mais seguro?

Para saber mais sobre as melhores práticas de segurança para WordPress, consulte [melhores práticas de segurança do WordPress no Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou a tentar utilizar PHPMyAdmin e posso ver a mensagem "Acesso negado"." Como resolver isto?

Podem ocorrer este problema, se a funcionalidade de na aplicação MySQL não está em execução ainda nesta instância do serviço de aplicações. Para resolver o problema, tente aceder ao seu Web site. Esta ação inicia os processos necessários, incluindo o processo de na aplicação MySQL. Para verificar que MySQL na aplicação está em execução, no Explorador de processos, certifique-se de que mysqld.exe está listado em processos.

Depois de assegurar que MySQL via aplicação está em execução, tente utilizar PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Posso obter um erro de HTTP 403 quando tento importar ou exportar a minha base de dados MySQL na aplicação utilizando PHPMyadmin. Como resolver isto?

Se estiver a utilizar uma versão anterior do Chrome, poderá ocorrer um erro conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Experimente também utilizando um browser diferente, como o Internet Explorer ou Edge, onde o problema não ocorre.
