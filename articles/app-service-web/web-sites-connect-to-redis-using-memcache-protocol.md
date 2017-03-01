---
title: "Ligar uma aplicação Web do Serviço de Aplicações através do protocolo Memcache: Azure | Microsoft Docs"
description: "Ligar uma aplicação Web no App Service do Azure para a Cache de Redis através do protocolo Memcache"
services: app-service\web
documentationcenter: php
author: SyntaxC4
manager: erikre
editor: riande
ms.assetid: 0fcdf9fa-2995-4839-ba4d-cfa389c4ba06
ms.service: app-service-web
ms.devlang: php
ms.topic: get-started-article
ms.tgt_pltfrm: windows
ms.workload: na
ms.date: 02/29/2016
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 550b4154f0fe510cb37ec4f8bdef251b06f0b495
ms.openlocfilehash: 4d867f80cfbe5360962550fbda0091f88784b4af
ms.lasthandoff: 01/27/2017


---
# <a name="connect-a-web-app-in-azure-app-service-to-redis-cache-via-the-memcache-protocol"></a>Ligar uma aplicação Web no App Service do Azure para a Cache de Redis através do protocolo Memcache
Neste artigo, irá aprender como ligar uma aplicação Web WordPress no [Serviço de Aplicações do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) para [a Cache de Redis do Azure][12] através do protocolo [Memcache][13]. Se tiver uma aplicação Web existente que utiliza um servidor Memcached para colocar em cache dentro da memória, pode migrá-la para o App Service do Azure e utilizar a solução de colocação em cache originais no Microsoft Azure com pouca ou nenhuma alteração para o código da aplicação. Além disso, pode utilizar os seus conhecimentos de Memcache existentes para criar aplicações distribuídas e altamente dimensionáveis no App Service do Azure com a Cache de Redis do Azure para colocar em cache dentro da memória, ao utilizar as estruturas de aplicações populares, tais como .NET, PHP, Node.js, Java e Python.  

As Web Apps do App Service permitem este cenário de aplicação com o shim de Memcache das Web Apps, que é um servidor Memcached local que age como um proxy Memcache para colocar em cache as chamadas para a Cache de Redis do Azure. Isto permite que qualquer aplicação que comunica utilizando o protocolo de Memcache coloque os dados em cache com a Cache de Redis. Este shim de Memcache funciona ao nível do protocolo, pelo que pode ser utilizado por qualquer aplicação ou estrutura da aplicação, desde que comunique utilizando o protocolo de Memcache.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="prerequisites"></a>Pré-requisitos
A shim de Memcache das Web Apps pode ser utilizada com qualquer aplicação fornecida, desde que comunique utilizando o protocolo de Memcache. Para este exemplo em concreto, a aplicação de referência é um site WordPress dimensionável que pode ser aprovisionado do Azure Marketplace.

Siga os passos descritos nestes artigos:

* [Aprovisionar uma instância do Serviço de Cache de Redis do Azure][0]
* [Implementar um site WordPress dimensionável no Azure][1]

Assim que tiver o site WordPress dimensionável implementado e uma instância da Cache de Redis aprovisionada, estará pronto para continuar com a ativação do shim de Memcache nas Web Apps do App Service do Azure.

## <a name="enable-the-web-apps-memcache-shim"></a>Ativar o shim de Memcache das Web Apps
Para configurar o shim de Memcache, tem de criar três definições de aplicação. Pode fazê-lo utilizando uma variedade de métodos, incluindo o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715), o [Portal Clássico][3], os [Cmdlets do Azure PowerShell][5] ou o [Interface de linha de Comandos do Azure][5]. Para efeitos desta mensagem, vou utilizar o [Portal do Azure][4] para configurar as definições de aplicação. Os seguintes valores podem ser obtidos a partir do painel **Definições** da instância da Cache de Redis.

![Painel de Definições da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### <a name="add-redishost-app-setting"></a>Adicionar definição de aplicação REDIS_HOST
A primeira definição de aplicação que necessita de criar é a definição de aplicação **REDIS\_HOST**. Esta definição define o destino para os quais o shim reencaminha as informações da cache. O valor necessário para a definição de aplicação REDIS_HOST pode ser obtido no painel **Propriedades** da instância da Cache de Redis.

![Nome de Anfitrião da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Defina a chave da definição de aplicação para **REDIS\_HOST** e o valor da definição de aplicação para o **nome de anfitrião** da instância da Cache de Redis.

![AppSetting REDIS_HOST da Aplicação Web](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### <a name="add-rediskey-app-setting"></a>Adicionar definição de aplicação REDIS_KEY
A primeira definição de aplicação que necessita de criar é a definição de aplicação **REDIS\_KEY**. Esta definição fornece o token de autenticação necessário para aceder de forma segura à instância da Cache de Redis. Pode obter o valor necessário para a definição de aplicação REDIS_KEY no painel **Chaves de acesso** da instância da Cache de Redis.

![Chave Primária da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Defina a chave da definição de aplicação para **REDIS\_KEY** e o valor da definição de aplicação para a **Chave Primária** da instância da Cache de Redis.

![AppSetting REDIS_KEY do Web site do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### <a name="add-memcacheshimredisenable-app-setting"></a>Adicionar definição de aplicação MEMCACHESHIM_REDIS_ENABLE
A última definição de aplicação é utilizada para ativar o Shim de Memcache nas Web Apps, que utiliza a REDIS_HOST e REDIS_KEY para ligar à Cache de Redis do Azure e reencaminhar as chamadas da cache. Definir a chave da definição de aplicação para **MEMCACHESHIM\_REDIS\_ENABLE** e o valor para **true**.

![AppSetting MEMCACHESHIM_REDIS_ENABLE da Aplicação Web](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Depois de terminar de adicionar as&3; (três) definições de aplicação, clique em **Guardar**.

## <a name="enable-memcache-extension-for-php"></a>Ativar a extensão de Memcache para PHP
Para que a aplicação fale o protocolo de Memcache, é necessário instalar a extensão de Memcache para PHP - a estrutura de idioma para o seu site WordPress.

### <a name="download-the-phpmemcache-extension"></a>Transferir a Extensão php_memcache
Navegue até [PECL][6]. Na categoria de colocação em cache, clique em [memcache][7]. Na coluna de transferências, clique na ligação DLL.

![Web site de PECL PHP](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Transfira a ligação Não Segura para Threads (NTS) x86 para a versão do PHP ativada nas Web Apps. (A predefinição é PHP 5.4)

![Pacote do Memcache do Web site PHP PECL](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### <a name="enable-the-phpmemcache-extension"></a>Ativar a extensão php_memcache
Depois de transferir o ficheiro, deszipe e carregue o **php\_memcache.dll** para o diretório **d:\\home\\site\\wwwroot\\bin\\ext\\**. Depois de o php_memcache.dll ser carregado para a aplicação Web, tem de ativar a extensão para o Runtime PHP. Para ativar a extensão de Memcache no Portal do Azure, abra o painel **Definições da Aplicação** da aplicação Web, em seguida, adicione uma nova definição da aplicação com a chave de **PHP\_EXTENSIONS** e o valor **bin\\ext\\php_memcache.dll**.

> [!NOTE]
> Se a aplicação Web tiver de carregar várias extensões de PHP, o valor de PHP_EXTENSIONS deve ser uma lista delimitada por vírgulas de caminhos relativos aos ficheiros DLL.
> 
> 

![AppSetting PHP_EXTENSIONS da Aplicação Web](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Depois de terminar, clique em **Guardar**.

## <a name="install-memcache-wordpress-plugin"></a>Instalar o plug-in do Memcache WordPress
> [!NOTE]
> Também pode transferir o [Plug-in de Cache de Objeto Memcached](https://wordpress.org/plugins/memcached/) de WordPress.org.
> 
> 

Na página de plug-ins WordPress, clique em **Adicionar Novo**.

![Página de Plug-in do WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

Na caixa de pesquisa, escreva **memcached** e prima **Enter**.

![Adicionar Novo Plug-in WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Localize a ** Cache de Objetos Memcached** na lista e, em seguida, clique em **Instalar Agora**.

![Instalar o Plug-in do Memcache WordPress](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### <a name="enable-the-memcache-wordpress-plugin"></a>Ativar o Plug-in do Memcache WordPress
> [!NOTE]
> Siga as instruções neste blogue sobre [Como ativar uma Extensão de Site nas Web Apps][8] para instalar os Visual Studio Team Services.
> 
> 

No ficheiro `wp-config.php`, adicione o seguinte código acima do comentário parar a edição perto do fim do ficheiro.

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Assim que tiver sido colado este código, o editor Monaco guarda automaticamente o documento.

O passo seguinte consiste em ativar o plug-in da cache de objeto. Para o fazer, arraste e largue **objeto cache.php** da pasta **wp-content/plugins/memcached** para a pasta **wp-content** para ativar a funcionalidade de Cache de Objeto Memcache.

![Localizar o plug-in object-cache.php do memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Agora que o ficheiro **objeto cache.php** está na pasta **wp-content**, a Cache de Objeto Memcached está ativada.

![Ativar o plug-in object-cache.php do memcache](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## <a name="verify-the-memcache-object-cache-plugin-is-functioning"></a>Verificar se o plug-in de Cache de Objeto Memcache está a funcionar
Todos os passos para ativar o shim de Memcache das Web Apps estão agora concluídos. Só falta verificar se os dados estão a preencher a instância da Cache de Redis.

### <a name="enable-the-non-ssl-port-support-in-azure-redis-cache"></a>Ativar o suporte da porta não SSL na Cache de Redis do Azure
> [!NOTE]
> No momento da escrita deste artigo, a CLI de Redis não suporta a conectividade SSL, por conseguinte, os seguintes passos são necessários.
> 
> 

No Portal do Azure, navegue para a instância da Cache de Redis que criou para esta aplicação Web. Com p painel da cache aberto, clique no ícone **Definições**.

![Botão das Definições da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Selecione **Portas de Acesso** na lista.

![Porta de Acesso da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Clique em **Não** para **Permitir o acesso apenas via SSL**.

![Apenas SSL da Porta de Acesso da Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Verá que a porta não SSL está agora definida. Clique em **Guardar**.

![Portal de Acesso Redis Não SSL na Cache de Redis do Azure](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### <a name="connect-to-azure-redis-cache-from-redis-cli"></a>Ligar para a Cache de Redis do Azure a partir de redis-cli
> [!NOTE]
> Este passo parte do princípio que o redis está instalado localmente na sua máquina de desenvolvimento. [Instalar Redis localmente utilizando estas instruções][9].
> 
> 

Abra a consola da linha de comandos e escreva o seguinte comando:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Substitua **&lt;hostname-for-redis-cache&gt;** pelo nome de anfitrião xxxxx.redis.cache.windows.net real e o **&lt;primary-key-for-redis-cache&gt;** pela tecla de acesso da cache e, em seguida, prima a tecla **Enter**. Após a CLI ter estabelecido ligação à instância da Cache de Redis, emita qualquer comando redis. Na captura de ecrã abaixo, optei por listar as chaves.

![Ligar à Cache de Redis do Azure a partir da CLI de Redis no Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

A chamada para listar as chaves deverá devolver um valor. Caso contrário, tente navegar para a aplicação Web e tentar novamente.

## <a name="conclusion"></a>Conclusão
Parabéns! A aplicação WordPress centralizou a cache de memória interna para ajudar a aumentar o débito. Lembre-se de que o Shim de Memcache das Web Apps pode ser utilizado com qualquer cliente Memcache, independentemente da linguagem de programação ou estrutura da aplicação. Para fornecer comentários ou fazer perguntas sobre o shim de Memcache das Web Apps, publique nos [Fóruns da MSDN][10] ou [Stackoverflow][11].

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de se inscrever numa conta do Azure, aceda a [Experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde pode criar de imediato uma aplicação Web de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## <a name="whats-changed"></a>O que mudou
* Para obter um guia da alteração de Web sites para o App Service, consulte: [App Service do Azure e o respetivo impacto nos Serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: /powershell/azureps-cmdlets-docs
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org

