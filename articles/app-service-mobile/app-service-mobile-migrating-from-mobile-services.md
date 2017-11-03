---
title: "Migrar a partir dos Mobile Services a uma aplicação móvel do serviço de aplicações"
description: "Saiba como migrar facilmente a sua aplicação de Mobile Services a uma aplicação de Mobile do serviço de aplicações"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 07507ea2-690f-4f79-8776-3375e2adeb9e
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: glenga
ms.openlocfilehash: ee64913629124f886e91478c21304956fbec9f90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="article-top"></a>Migrar o serviço de móvel do Azure existente para o App Service do Azure
Com o [disponibilidade geral do App Service do Azure], sites de Mobile Services do Azure podem ser migrados facilmente no local para tirar partido de todas as funcionalidades do serviço de aplicações do Azure.  Este documento explica o que esperar quando migrar o seu site de Mobile Services do Azure App Service do Azure.

## <a name="what-does-migration-do"></a>O que fazer migração para o site
Migração do seu serviço móvel do Azure ativa o seu serviço móvel para um [App Service do Azure] aplicações sem afetar o código.  Os Notification Hubs, SQL Server a ligação de dados, definições de autenticação, tarefas agendadas e nome de domínio permanecem inalterados.  Utilizar o seu serviço móvel do Azure de clientes móveis continuam a funcionar normalmente.  Migração reinicia o serviço uma vez que é transferido para o App Service do Azure.

[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Por que motivo deve migrar do site
Microsoft é Recomendamos a migração do seu serviço de móvel do Azure para tirar partido das funcionalidades do App Service do Azure, incluindo:

* Novas funcionalidades de anfitrião, incluindo [WebJobs] e [nomes de domínio personalizados].
* Monitorização e resolução de problemas com New Relic ou [Application Insights].
* Ferramentas de DevOps incorporadas, incluindo [ranhuras de teste], reversão e produção de teste.
* [Escala automática], balanceamento, de carga e [monitorização do desempenho].

Para obter mais informações sobre as vantagens do App Service do Azure, consulte o [vs de Mobile Services. Serviço de aplicações] tópico.

## <a name="before-you-begin"></a>Antes de começar
Antes de iniciar qualquer trabalho principais no seu site, deve copiar em segurança os scripts de serviço móvel e a base de dados SQL.

## <a name="migrating-site"></a>Migrar os sites
O processo de migração migra todos os sites numa única região do Azure.

Para migrar o seu site:

1. Inicie sessão no [Portal clássico do Azure].
2. Selecione um serviço móvel na região que pretende migrar.
3. Clique em de **migrar para o App Service** botão.

   ![O botão de migração][0]
4. Ler o migrar para a caixa de diálogo App Service.
5. Introduza o nome do seu serviço móvel na caixa fornecida.  Por exemplo, se o nome de domínio for contoso.azure mobile.net, em seguida, introduza *contoso* na caixa fornecida.
6. Clique no botão de marcas de escala.

Monitorize o estado da migração no monitor de atividade. O site está listado como *migrar* no Portal clássico do Azure.

  ![Monitor de atividade de migração][1]

Cada migração pode tomar entre 3 e 15 minutos por serviço móvel que está a ser migrados.  O site permanece disponível durante a migração.
O site é reiniciado no fim do processo de migração.  O site não está disponível durante o processo de reinício, o que poderá última alguns segundos.

## <a name="finalizing-migration"></a>A finalizar a migração
Plano para testar o seu site de um cliente móvel na conclusão do processo de migração.  Certifique-se de que pode efetuar todas as ações de cliente comuns sem alterações para o cliente móvel.  

### <a name="update-app-service-tier"></a>Selecione um escalão de preço do serviço de aplicações adequado
Tem mais flexibilidade na preços depois de migrar para o App Service do Azure.

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **plano do App Service** no menu definições.
5. Clique em de **escalão de preço** mosaico.
6. Clique no mosaico adequado aos seus requisitos, em seguida, clique em **selecione**.  Poderá ter de clique **ver todos os** para ver os escalões de preços disponíveis.

Como um ponto de partida, recomendamos as seguintes escalões:

| Escalão de preço do serviço móvel | Escalão de preço do serviço de aplicações |
|:--- |:--- |
| Gratuito |F1 Gratuito |
| Básica |B1 básico |
| Standard |S1 Standard |

Não há flexibilidade considerável na escolher o direito de escalão de preço para a aplicação.  Consulte [preços do App Service] para obter detalhes completos sobre os preços do seu novo serviço de aplicações.

> [!TIP]
> A camada de serviço de aplicações padrão contém acesso a várias funcionalidades que pretende utilizar, incluindo [ranhuras de teste], cópias de segurança automáticas e o dimensionamento automático.  Verifique as capacidades do novo enquanto estiver existe!
>
>

### <a name="review-migration-scheduler-jobs"></a>Reveja as tarefas do programador migrados
Programador de tarefas não serão visíveis até aproximadamente 30 minutos após a migração.  Tarefas agendadas continuam a ser executado em segundo plano.
Para ver as tarefas agendadas depois de estarem visíveis novamente:

1. Inicie sessão no [portal do Azure].
2. Selecione **Procurar >**, introduza **agenda** no *filtro* caixa, em seguida, selecione **coleções do agendador**.

Existem um número limitado de gratuitas do agendador tarefas disponíveis pós-migração.  Reveja a sua utilização e o [planos de agendador do Azure].

### <a name="configure-cors"></a>Configurar a CORS, se for necessário
Recursos de várias origens de partilha é uma técnica para permitir que um Web site aceder a uma API Web num domínio diferente.  Se estiver a utilizar Mobile Services do Azure com um Web site associado, terá de configurar a CORS como parte da migração.  Se estiver a aceder Mobile Services do Azure exclusivamente a partir de dispositivos móveis, em seguida, CORS não precisa de ser configurada, exceto em casos raros.

As definições CORS migradas estão disponíveis como o **MS_CrossDomainWhitelist** definição de aplicação.  Para migrar o seu site para a instalações de CORS do App Service:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **CORS** no menu de API.
5. Introduza as origens permitidas na caixa fornecida, premindo Enter após cada um deles.
6. Assim que a sua lista de origens permitidas estiver correta, clique no botão de guardar.

> [!TIP]
> Uma das vantagens da utilização de um serviço de aplicações do Azure é que pode executar o seu web site e o serviço móvel no mesmo site.  Para obter mais informações, consulte o [passos](#next-steps) secção.
>
>

### <a name="download-publish-profile"></a>Transfira um novo perfil de publicação
O perfil de publicação do seu site é alterado quando migrar para o App Service do Azure.  Se pretender publicar o site a partir de Visual Studio, precisa de um novo perfil de publicação.  Para transferir o novo perfil de publicação:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Clique em **perfil de publicação de Get**.

O ficheiro PublishSettings é transferido para o seu computador.  É normalmente denominado *sitename*. PublishSettings.  Importe as definições de publicação para o seu projeto existente:

1. Abra o Visual Studio e o seu projeto de serviço do Azure Mobile.
2. Clique com o botão direito no projeto a **Explorador de soluções** e selecione **publicar...**
3. Clique em **importação**
4. Clique em **procurar** e selecione o transferido publicar o ficheiro de definições.  Clique em **OK**
5. Clique em **validar ligação** para garantir que o trabalho de definições de publicação.
6. Clique em **publicar** para publicar o seu site.

## <a name="working-with-your-site"></a>Trabalhar com a pós-migração de site
Começar a trabalhar com o novo serviço de aplicações no [portal do Azure] pós-migração.  Seguem-se algumas notas sobre operações específicas que utilizou para executar o [Portal clássico do Azure], em conjunto com os respetivos equivalentes de serviço de aplicações.

### <a name="publishing-your-site"></a>Transferir e publicar o seu site migrado
O site está disponível através do git ou ftp e pode ser republicado com vários mecanismos diferentes, incluindo WebDeploy, TFS, Mercurial, GitHub e FTP.  As credenciais de implementação são migradas com o resto do seu site.  Se não foi possível definir as suas credenciais de implementação ou não se lembra-los, pode repô-los:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **as credenciais de implementação** na publicação de menu.
5. Introduza as credenciais de implementação nova nas caixas fornecidas e, em seguida, clique no botão guardar.

Pode utilizar estas credenciais para clonar o site com o git ou configurar implementações automatizadas do GitHub, TFS ou Mercurial.  Para obter mais informações, consulte a [documentação de implementação do App Service do Azure].

### <a name="appsettings"></a>Definições da aplicação
A maioria das definições para um serviço móvel migrado estão disponíveis através de definições da aplicação.  Pode obter uma lista das definições de aplicação a [portal do Azure].
Para ver ou alterar as definições de aplicação:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **definições da aplicação** no menu geral.
5. Desloque-se para a secção de definições da aplicação e localize a definição de aplicação.
6. Clique no valor da definição de aplicação para editar o valor.  Clique em **guardar** para guardar o valor.

Pode atualizar várias definições de aplicação ao mesmo tempo.

> [!TIP]
> Existem duas definições de aplicação com o mesmo valor.  Por exemplo, poderá ver *ApplicationKey* e *MS\_ApplicationKey*.  Atualize as definições da aplicação ao mesmo tempo.
>
>

### <a name="authentication"></a>Autenticação
Todas as definições de autenticação estão disponíveis como definições de aplicação no seu site migrado.  Para atualizar as definições de autenticação, tem de alterar as definições da aplicação adequado.  A tabela seguinte mostra as definições de aplicação adequado para o fornecedor de autenticação:

| Fornecedor | ID de cliente | Segredo do cliente | Outras definições |
|:--- |:--- |:--- |:--- |
| Conta Microsoft |**MS\_MicrosoftClientID** |**MS\_MicrosoftClientSecret** |**MS\_MicrosoftPackageSID** |
| Facebook |**MS\_FacebookAppID** |**MS\_FacebookAppSecret** | |
| Twitter |**MS\_TwitterConsumerKey** |**MS\_TwitterConsumerSecret** | |
| Google |**MS\_GoogleClientID** |**MS\_GoogleClientSecret** | |
| Azure AD |**MS\_AadClientID** | |**MS\_AadTenants** |

Nota: **MS\_AadTenants** é armazenado como uma lista separada por vírgulas de domínios do inquilino (os campos "Inquilinos permitida" no portal de Mobile Services).

> [!WARNING]
> **Não utilize os mecanismos de autenticação no menu Definições**
>
> App Service do Azure fornece um "sem código" autenticação e autorização sistema separado sob o *autenticação / autorização* menu definições e (preterido) *Mobile autenticação* opção no menu definições.  Estas opções são incompatíveis com um serviço de Mobile migrado do Azure.  Pode [atualizar o seu site](app-service-mobile-net-upgrading-from-mobile-services.md) para tirar partido da autenticação do App Service do Azure.
>
>

### <a name="easytables"></a>Dados
O *dados* separador nos Mobile Services foi substituído por *tabelas fácil* no portal do Azure.  Para aceder a tabelas fácil:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **tabelas fácil** no menu móvel.

Pode adicionar uma tabela clicando a **adicionar** botão ou aceder as tabelas existentes ao clicar em nome de uma tabela.  Existem várias operações, pode fazê-lo a partir deste painel, incluindo:

* Alterar as permissões de tabela
* Editar os scripts operacionais
* Gerir o esquema da tabela
* Eliminar a tabela
* Limpar o conteúdo da tabela
* Eliminar linhas específicas da tabela

### <a name="easyapis"></a>API
O *API* separador nos Mobile Services foi substituído por *APIs fácil* no portal do Azure.  Para aceder a APIs fácil:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Clique em **APIs fácil** no menu móvel.

As suas APIs migrados já estão listados no painel.  Também pode adicionar uma API a partir deste painel.  Para gerir uma API específica, clique em da API.
No novo painel, pode ajustar as permissões e editar os scripts para a API.

### <a name="on-demand-jobs"></a>Tarefas do agendador
Todos os trabalhos do programador do estão disponíveis através da secção de coleções de tarefas do programador.  Para aceder às suas tarefas do agendador:

1. Inicie sessão no [portal do Azure].
2. Selecione **Procurar >**, introduza **agenda** no *filtro* caixa, em seguida, selecione **coleções do agendador**.
3. Selecione a coleção de tarefas para o seu site.  É denominado *sitename*-tarefas.
4. Clique em **definições**.
5. Clique em **trabalhos do programador do** em GERIR.

Tarefas agendadas são listadas com a frequência especificada antes da migração.  Tarefas a pedido estão desativadas.  Para executar uma tarefa a pedido:

1. Selecione a tarefa que pretende executar.
2. Se necessário, clique em **ativar** para ativar a tarefa.
3. Clique em **definições**, em seguida, **agenda**.
4. Selecione uma periodicidade de **uma vez**, em seguida, clique em **guardar**

As tarefas a pedido estão localizadas em `App_Data/config/scripts/scheduler post-migration`.  Recomendamos que converta todas as tarefas a pedido para [WebJobs] ou [funções].  Escrever novas tarefas de programador como [WebJobs] ou [funções].

### <a name="notification-hubs"></a>Hubs de notificação
Mobile Services utiliza os Notification Hubs para notificações push.  As seguintes definições de aplicação são utilizadas para ligar o Hub de notificação para o seu serviço móvel após a migração:

| Definição de aplicação | Descrição |
|:--- |:--- |
| **MS\_PushEntityNamespace** |O espaço de nomes do Hub de notificação |
| **MS\_NotificationHubName** |O nome do Notification Hub |
| **MS\_NotificationHubConnectionString** |A cadeia de ligação do Hub de notificação |
| **MS\_NamespaceName** |Um alias para MS_PushEntityNamespace |

O Notification Hub é gerido através de [portal do Azure].  Tome nota do nome de Hub de notificação (pode encontrar isto utilizando as definições de aplicação):

1. Inicie sessão no [portal do Azure].
2. Selecione **procurar**>, em seguida, selecione **Hubs de notificação**
3. Clique no nome do Notification Hub associado ao serviço móvel.

> [!NOTE]
> Se o seu HUb de notificação é um tipo de "Mixed", não é visível.  "Misto" tipo de notification hubs utilizam os Notification Hubs e funcionalidades do Service Bus legadas.  [Converter os espaços de nomes mistos] antes de continuar.  Depois de concluída a conversão, é apresentado o hub de notificação no [portal do Azure].
>
>

Para obter mais informações, consulte o [Notification Hubs] documentação.

> [!TIP]
> Funcionalidades de gestão de Hubs de notificação no [portal do Azure] estão ainda na pré-visualização.  O [Portal clássico do Azure] permanece disponível para gerir todos os seus Hubs de notificação.
>
>

### <a name="legacy-push"></a>Definições de legado Push
Se tiver configurado o Push no seu serviço móvel antes da introdução nos Notification Hubs, estiver a utilizar *push legado*.  Se estiver a utilizar o Push e não vir um Hub de notificação listadas na sua configuração, em seguida, é provável que está a utilizar *push legado*.  Esta funcionalidade é migrada com todas as outras funcionalidades.  No entanto, recomendamos que Atualize para os Notification Hubs logo após a migração estar concluída.

No provisório, todas as definições de legado push (com a exceção relevantes do certificado do APNS) estão disponíveis nas definições de aplicação.  Atualize o certificado do APNS ao substituir o ficheiro adequado no sistema de ficheiros.

### <a name="app-settings"></a>Outras definições de aplicação
As seguintes definições de aplicação adicionais são migrados do seu serviço móvel e disponíveis em *definições* > *as definições de aplicação*:

| Definição de aplicação | Descrição |
|:--- |:--- |
| **MS\_MobileServiceName** |O nome da sua aplicação |
| **MS\_MobileServiceDomainSuffix** |O prefixo de domínio. revertidos mobile.net do Azure |
| **MS\_ApplicationKey** |A chave de aplicação |
| **MS\_MasterKey** |A chave mestra de aplicação |

A chave da aplicação e a chave mestra são idênticas às chaves de aplicação do seu serviço móvel original.  Em particular, a chave de aplicação é enviada por clientes móveis para validar a sua utilização da API móvel.

### <a name="cliequivalents"></a>Linha de comandos equivalentes
Já pode utilizar o *azure móvel* comandos para gerir o site de Mobile Services do Azure.  Em vez disso, foram substituídas muitas funções com o *do azure site* comando.  Utilize a seguinte tabela para localizar equivalentes de comandos comuns:

| *Azure Mobile* comando | Equivalente *Azure Site* comando |
|:--- |:--- |
| localizações móveis |lista de localização do site |
| lista móvel |lista de sites |
| Mostrar móvel *nome* |site mostrar *nome* |
| reinício móvel *nome* |reinício de sites *nome* |
| Volte a implementar móvel *nome* |a implementar de implementação do site *commitId* *nome* |
| conjunto de chaves móvel *nome* *tipo* *valor* |Elimine a appsetting de site *chave* *nome* <br/> Adicionar site appsetting *chave*=*valor* *nome* |
| lista de configuração móvel *nome* |a lista de sites appsetting *nome* |
| obter configuração móvel *nome* *chave* |site appsetting mostrar *chave* *nome* |
| conjunto de configuração móvel *nome* *chave* |Elimine a appsetting de site *chave* *nome* <br/> Adicionar site appsetting *chave*=*valor* *nome* |
| lista de domínio móveis *nome* |lista de sites de domínio *nome* |
| Adicionar domínio móvel *nome* *domínio* |Adicionar domínio site *domínio* *nome* |
| eliminação de domínio móveis *nome* |eliminação de domínio do site *domínio* *nome* |
| Mostrar escala móvel *nome* |site mostrar *nome* |
| alteração de escala móvel *nome* |modo de dimensionamento de site *modo* *nome* <br /> instâncias de escala do site *instâncias* *nome* |
| appsetting móveis lista *nome* |a lista de sites appsetting *nome* |
| appsetting móvel adicionar *nome* *chave* *valor* |Adicionar site appsetting *chave*=*valor* *nome* |
| Elimine a appsetting móvel *nome* *chave* |Elimine a appsetting de site *chave* *nome* |
| appsetting móveis mostrar *nome* *chave* |Elimine a appsetting de site *chave* *nome* |

Atualize autenticação ou as definições de notificação push ao atualizar a definição de aplicação adequada.
Editar os ficheiros e publicar o site através de ftp ou git.

### <a name="diagnostics"></a>Diagnóstico e de registo
Diagnóstico está normalmente desativado num serviço de aplicações do Azure.  Para ativar o registo de diagnóstico:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Abre o painel de definições por predefinição.
4. Selecione **os registos de diagnóstico** sob o menu de funcionalidades.
5. Clique em **ON** para os seguintes registos: **registo na aplicação (sistema de ficheiros)**, **mensagens de erro detalhadas**, e **rastreio de pedidos falhados**
6. Clique em **sistema de ficheiros** para registo de servidor Web
7. Clique em **guardar**

Para ver os registos:

1. Inicie sessão no [portal do Azure].
2. Selecione **todos os recursos** ou **serviços aplicacionais** , em seguida, clique no nome do seu serviço móvel migrados.
3. Clique em de **ferramentas** botão
4. Selecione **registo fluxo** sob o menu OBSERVE.

Os registos são apresentados na janela à medida que estes são gerados.  Também pode transferir os registos para o Analysis Services posteriores com as suas credenciais de implementação. Para obter mais informações, consulte o [registo] documentação.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Eliminar um Clone de aplicação móvel migrados faz com que uma falha do site
Se a clonar o seu serviço móvel migrado com o Azure PowerShell, em seguida, elimine o clone, é removida a entrada DNS para o seu serviço de produção.  O site é deixará de estar acessível a partir da Internet.  

Resolução: Se pretender clonar o seu site, tal através do portal.

### <a name="changing-webconfig-does-not-work"></a>A alteração de Web. config não funciona
Se tiver um site do ASP.NET, é alterado para o `Web.config` ficheiro não ser aplicadas.  O App Service do Azure baseia-se um adequado `Web.config` ficheiro durante o arranque para suportar o tempo de execução de Mobile Services.  Pode substituir determinadas definições (tais como cabeçalhos personalizados), utilizando um ficheiro de transformação XML.  Criar um ficheiro chamado no `applicationHost.xdt` -este ficheiro tem acabar no `D:\home\site` diretório no serviço do Azure.  Carregar o `applicationHost.xdt` ficheiro através de um script de implementação personalizada ou diretamente com o Kudu.  O seguinte mostra um documento de exemplo:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para obter mais informações, consulte o [amostras de transformação XDT] documentação no GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Serviços móveis migrados não pode ser adicionados para o Gestor de tráfego
Quando cria um perfil do Traffic Manager, diretamente não é possível escolher um serviço móvel migrado para o perfil.  Utilize "pontos finais externos."  Só é possível adicionar o ponto final externo através do PowerShell.  Para obter mais informações, consulte o [tutorial do Gestor de tráfego](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Passos Seguintes
Agora que a aplicação é migrada para o App Service, existem ainda mais funcionalidades que pode utilizar:

* Implementação [ranhuras de teste] permitem-lhe testar alterações ao seu site e executar um teste a / B.
* [WebJobs] é um substituto para tarefas agendada no pedido.
* Pode [continuamente implementar] do site ao ligar o seu site para o GitHub, TFS ou Mercurial.
* Pode utilizar [Application Insights] para monitorizar o seu site.
* Servem um Web site e uma API Mobile do código do mesmo.

### <a name="upgrading-your-site"></a>Atualizar o site de Mobile Services para o SDK de aplicações móveis do Azure
* Para projetos de servidor com base no Node.js, a nova [Mobile Apps Node.js SDK] fornece várias funcionalidades novas. Por exemplo, pode agora efetuar desenvolvimento local e de depuração, utilize qualquer versão do Node.js acima 0.10 e personalizar com qualquer middleware de Express.js.
* Para. Projetos do servidor com base em rede, a nova [pacotes NuGet de SDK do Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tem mais flexibilidade na NuGet dependências.  Estes pacotes suportam a autenticação do serviço de aplicações nova e compõem com qualquer projeto ASP.NET. Para saber mais sobre a atualização, consulte [atualizar o serviço de móvel atual do .NET no App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Preços do serviço de aplicações]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[Escala automática]: ../app-service/web-sites-scale.md
[App Service do Azure]: ../app-service/app-service-web-overview.md
[Portal clássico do Azure]: https://manage.windowsazure.com
[portal do Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[planos de agendador do Azure]: ../scheduler/scheduler-plans-billing.md
[continuamente implementar]: ../app-service/app-service-continuous-deployment.md
[Converter os espaços de nomes mistos]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomes de domínio personalizados]: ../app-service/app-service-web-tutorial-custom-domain.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidade geral do App Service do Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Hybrid Connections]: ../app-service/app-service-hybrid-connections.md
[registo]: ../app-service/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[vs de Mobile Services. Serviço de aplicações]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitorização do desempenho]: ../app-service/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[ranhuras de teste]: ../app-service/web-sites-staged-publishing.md
[VNet]: ../app-service/web-sites-integrate-with-vnet.md
[amostras de transformação XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[funções]: ../azure-functions/functions-overview.md
