---
title: "Integrar um serviço em nuvem do Azure com o Azure CDN | Microsoft Docs"
description: "Saiba como implementar um serviço em nuvem que serve o conteúdo a partir de um ponto de final de CDN do Azure integrado"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f2849fe25fd0d5b3dc26598ffba7591cb7433161
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="intro"></a>Integrar um serviço em nuvem a CDN do Azure
Um serviço em nuvem pode ser integrado com a CDN do Azure, que serve qualquer conteúdo da localização do serviço em nuvem. Esta abordagem proporciona as seguintes vantagens:

* Fácil de implementar e atualizar imagens, scripts e tramas nos diretórios de projeto do seu serviço em nuvem
* Atualizar facilmente os pacotes de NuGet no seu serviço de nuvem, tais como jQuery ou versões de arranque de configuração
* Gerir a sua aplicação Web e o CDN-servidos conteúdo todos os da mesma interface de Visual Studio
* Fluxo de trabalho de implementação unificada para a sua aplicação Web e o conteúdo servido de CDN
* Integrar o agrupamento do ASP.NET e minification com CDN do Azure

## <a name="what-you-will-learn"></a>O que aprenderá
Neste tutorial, ficará a saber como:

* [Integrar um ponto final de CDN do Azure com o serviço de nuvem e a servir conteúdo estático nas suas páginas Web da CDN do Azure](#deploy)
* [Configurar definições da cache de conteúdo estático no seu serviço em nuvem](#caching)
* [Servir conteúdo das ações de controlador através da CDN do Azure](#controller)
* [Personalizada incluídas e minified conteúdo através da CDN do Azure, mantendo o script de depuração experiência no Visual Studio](#bundling)
* [Configurar a contingência os scripts e CSS quando a CDN do Azure está offline](#fallback)

## <a name="what-you-will-build"></a>O que irá criar
Irá implementar uma função de Web de serviço de nuvem utilizando predefinido modelo MVC do ASP.NET, adicionar código para servir conteúdo a partir de uma CDN do Azure integrada, como uma imagem, resultados de ação de controlador e ficheiros predefinidos JavaScript e CSS e também escrever código para configurar a contingência mecanismo de pacotes servidos no caso da CDN estiver offline.

## <a name="what-you-will-need"></a>O que precisa
Este tutorial tem os seguintes pré-requisitos:

* Um Active Directory [conta do Microsoft Azure](/account/)
* Visual Studio 2015 com [SDK do Azure](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure:
> 
> * Pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/) -receberá créditos pode utilizar para experimentar serviços pagos do Azure e, mesmo depois, pode manter a conta e utilizar os serviços do Azure, tais como Web sites gratuitos.
> * Pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) -sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar para serviços pagos do Azure.
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>Implementar um serviço em nuvem
Nesta secção, irá implementar a modelo de aplicação MVC do ASP.NET no Visual Studio 2015 de predefinição a uma função de Web do serviço de nuvem e, em seguida, integrá-lo com um novo ponto final da CDN. Siga as instruções abaixo:

1. No Visual Studio 2015, criar um novo serviço em nuvem do Azure da barra de menus, acedendo a **ficheiro > novo > projeto > nuvem > serviço de nuvem do Azure**. Atribua um nome e clique em **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. Selecione **função da Web ASP.NET** e clique em de  **>**  botão. Clique em OK.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. Selecione **MVC** e clique em **OK**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. Agora, publica esta função da Web num serviço em nuvem do Azure. O projeto de serviço em nuvem com o botão direito e selecione **publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. Se ainda não tiver iniciado no Microsoft Azure, clique em de **adicionar uma conta...**  pendente e clique em de **adicionar uma conta** item de menu.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. Na página de início de sessão, iniciar sessão com a conta Microsoft que utilizou para ativar a sua conta do Azure.
7. Assim que tem sessão iniciada, clique em **seguinte**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. Partindo do princípio que ainda não criou uma conta de armazenamento ou de serviço de nuvem, o Visual Studio irão ajudá-lo a criar ambos. No **criar serviço de nuvem e de conta** caixa de diálogo, introduza o nome do serviço pretendido e selecione a região pretendida. Em seguida, clique em **Criar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. Na página de definições de publicação, verifique a configuração e clique em **publicar**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > O processo de publicação para serviços em nuvem demora muito tempo. A ativar o Web Deploy para a opção de funções todos os pode tornar a depuração do seu serviço em nuvem muito mais rápido, fornecendo atualizações rápidas (mas temporário) para as funções da Web. Para obter mais informações sobre esta opção, consulte [publicação de um serviço em nuvem com as ferramentas do Azure](http://msdn.microsoft.com/library/ff683672.aspx).
   > 
   > 
   
    Quando o **registo de atividade do Microsoft Azure** mostra que a publicação de estado é **concluído**, irá criar um ponto final da CDN que está integrado com este serviço em nuvem.
   
   > [!WARNING]
   > Se, após a publicação, o serviço em nuvem implementado apresenta um ecrã de erro, é provável porque está a utilizar o serviço em nuvem que implementou um [convidado SO que não inclua a .NET 4.5.2](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Pode contornar este problema por [implementar .NET 4.5.2 como uma tarefa de arranque](../cloud-services/cloud-services-dotnet-install-dotnet.md).
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil da CDN
Um perfil da CDN é uma coleção de pontos finais da CDN.  Cada perfil contém um ou mais pontos finais da CDN.  Pode pretender utilizar vários perfis para organizar os seus pontos finais da CDN por domínio de Internet, aplicação Web ou alguns outros critérios.

> [!TIP]
> Se já tiver um perfil da CDN que pretende utilizar para este tutorial, avance para [criar um novo ponto final da CDN](#create-a-new-cdn-endpoint).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final da CDN
**Para criar um novo ponto final da CDN para a sua conta de armazenamento**

1. No [Azure Management Portal](https://portal.azure.com), navegue para o perfil de CDN.  É possível que o tenha afixado ao dashboard no passo anterior.  Se não o tiver feito, pode encontrá-lo ao clicar em **Procurar** e, em seguida, **Perfis da CDN** e clicar no perfil ao qual planeia adicionar o seu ponto final.
   
    É apresentado o painel do perfil da CDN.
   
    ![Perfil da CDN][cdn-profile-settings]
2. Clique no botão **Adicionar Ponto Final**.
   
    ![Botão Adicionar ponto final][cdn-new-endpoint-button]
   
    O painel **Adicionar um ponto final** aparece.
   
    ![Painel Adicionar ponto final][cdn-add-endpoint]
3. Introduza um **Nome** para este ponto final da CDN.  Este nome será utilizado para aceder aos seus recursos em cache no domínio `<EndpointName>.azureedge.net`.
4. No **tipo de origem** lista pendente, selecione *serviço em nuvem*.  
5. No **nome de anfitrião de origem** lista pendente, selecione o seu serviço de nuvem.
6. Deixe as predefinições para **caminho de origem**, **cabeçalho de anfitrião de origem**, e **porta de protocolo/origem**.  Tem de especificar, pelo menos, um protocolo (HTTP ou HTTPS).
7. Clique no botão **Adicionar** para criar o novo ponto final.
8. Quando o ponto final for criado, aparece numa lista de pontos finais para o perfil. A vista de lista mostra o URL a utilizar para aceder ao conteúdo em cache, bem como o domínio de origem.
   
    ![Ponto final da CDN][cdn-endpoint-success]
   
   > [!NOTE]
   > O ponto final não imediatamente estará disponível para utilização.  Pode demorar até 90 minutos para que o registo propagar através da rede da CDN. Os utilizadores que tentam utilizar o nome de domínio da CDN imediatamente poderão receber o código de estado 404 até que o conteúdo está disponível através do CDN.
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>Testar o ponto final da CDN
Quando o estado da publicação é **concluído**, abra uma janela do browser e navegue para  **http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Na minha configuração, este URL é:

    http://camservice.azureedge.net/Content/bootstrap.css

Que corresponde ao seguinte URL de origem no ponto final de CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Quando navegar para  **http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, consoante o seu browser, ser-lhe-á solicitado para transferir ou abra bootstrap.css fornecido da sua aplicação Web publicada.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Da mesma forma pode aceder a qualquer URL acessível publicamente em  **http://*&lt;serviceName >*.cloudapp.net/**, diretamente a partir do ponto final de CDN. Por exemplo:

* Um ficheiro. js do caminho de /script.
* Qualquer ficheiro de conteúdo a partir de /Content caminho
* Qualquer controlador/ação
* Se a cadeia de consulta está ativada no ponto final de CDN, qualquer URL com cadeias de consulta

Na verdade, a configuração acima, pode alojar o serviço integralmente na nuvem  **http://*&lt;cdnName >*.azureedge.net/**. Se posso navegue para **http://camservice.azureedge.net/**, receber o resultado da ação de Home/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Isto não significa que, no entanto, que é sempre boa ideia para servir um serviço integralmente na nuvem através da CDN do Azure. 

Uma CDN com otimização de entrega estático não necessariamente acelerar a entrega de elementos dinâmicos que não se destinam à colocadas em cache, ou são atualizado muito frequentemente, uma vez que a CDN tem de solicitar uma nova versão do elemento do servidor de origem muito frequentemente. Para este cenário, pode ativar [aceleração dinâmico do Site](cdn-dynamic-site-acceleration.md) otimização (DSA) no ponto final de CDN que utiliza várias técnicas para acelerar a entrega de elementos dinâmicos não colocáveis. 

Se tiver um site com uma combinação de conteúdo estático e dinâmico, pode escolher para servir conteúdo estático de CDN com um tipo de otimização estático (por exemplo, entrega web geral) e a servir conteúdo dinâmico diretamente a partir do servidor de origem ou através de um w de ponto final CDN Otimização de DSA ith ativado numa base de maiúsculas e minúsculas, maiúsculas e minúsculas. Para esse fim constatou já como aceder a ficheiros individuais de conteúdo do ponto final de CDN. Posso irá mostrar como fornecer uma ação de um controlador específico através de um ponto final de CDN específico em conteúdo servirem das ações de controlador através da CDN do Azure.

A alternativa consiste em determinar os conteúdos para servir a partir da CDN do Azure numa base caso a caso, o serviço em nuvem. Para esse fim constatou já como aceder a ficheiros individuais de conteúdo do ponto final de CDN. Posso irá mostrar como fornecer uma ação de um controlador específico através de ponto final de CDN no [servir conteúdo das ações de controlador através do Azure CDN](#controller).

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configurar opções de colocação em cache para ficheiros estáticos no seu serviço em nuvem
Com a integração da CDN do Azure no seu serviço em nuvem, pode especificar como pretende que o conteúdo estático para a colocação em cache no ponto final de CDN. Para tal, abra *Web. config* da função da Web do projeto (por exemplo, WebRole1) e adicione um `<staticContent>` elemento `<system.webServer>`. O XML abaixo configura a cache para expirar dentro de 3 dias.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Depois de fazê-lo, todos os ficheiros estáticos no seu serviço de nuvem serão observar a mesma regra na sua cache da CDN. Para um controlo mais granular das definições da cache, adicione um *Web. config* para uma pasta de ficheiros e adicionar as definições não existe. Por exemplo, adicionar um *Web. config* do ficheiro para o *\Content* pasta e substitua o conteúdo com o XML dos seguinte:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta definição faz com que todos os ficheiros estáticos a partir de *\Content* pasta à colocadas em cache para 15 dias.

Para obter mais informações sobre como configurar o `<clientCache>` elemento, consulte [Cache do cliente &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

No [servir conteúdo das ações de controlador através do Azure CDN](#controller), posso também irão mostrar como pode configurar definições de cache para resultados de ação de controlador na cache do CDN.

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Servir conteúdo das ações de controlador através da CDN do Azure
Quando integrar uma função de Web do serviço de nuvem com o Azure CDN, é relativamente fácil de servir conteúdo das ações de controlador pela CDN do Azure. Que não seja a servir a sua nuvem service diretamente através da CDN do Azure (demonstrados acima), [Maarten Balliauw](https://twitter.com/maartenballiauw) mostra-lhe como fazê-lo com um diversão MemeGenerator controlador no [reduzindo a latência na web com o Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN). Posso irá simplesmente reproduzi-la aqui.

Suponhamos na sua nuvem serviço que pretende gerar memes baseado numa imagem Chuck Norris mais novo / (fotografia por [João leve](http://www.flickr.com/photos/alan-light/218493788/)) como esta:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Tiver uma simples `Index` ação que permite que os clientes especificar os superlatives na imagem, em seguida, gera o meme depois de publicar a ação. Uma vez que for Chuck Norris, seria de esperar esta página para se tornarem wildly populares global. Este é um bom exemplo servir conteúdo por dinâmico com CDN do Azure.

Siga os passos acima para esta ação de controlador de configuração:

1. No *\Controllers* pasta, crie um novo ficheiro de CS chamado *MemeGeneratorController.cs* e substitua o conteúdo com o seguinte código. Não se esqueça de substituir a parte realçada com o nome do CDN.  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. Clique com o botão direito no predefinido `Index()` ação e selecione **Adicionar vista**.
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. Aceite as definições abaixo e clique em **adicionar**.
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. Abra o novo *Views\MemeGenerator\Index.cshtml* e substitua o conteúdo com o seguinte HTML simple para submeter os superlatives:
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. Publicar novamente o serviço em nuvem e navegue para  **http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** no seu browser.

Ao submeter os valores de forma a `/MemeGenerator/Index`, a `Index_Post` método da ação devolve uma hiperligação para o `Show` método da ação com o respetivo identificador de entrada. Ao clicar na ligação, alcançar o seguinte código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Se está ligado o depurador local, em seguida, irá receber a experiência de depuração regulares com um local de redirecionamento. Se estiver em execução no serviço em nuvem, em seguida, irá redirecionar para:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no ponto final de CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Em seguida, pode utilizar o `OutputCacheAttribute` atributo no `Generate` método para especificar como o resultado da ação deve ser colocados em cache, que irão honrar CDN do Azure. O código abaixo especificar uma expiração de cache de 1 hora (3.600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Da mesma forma, pode servir conteúdo a partir de qualquer ação do controlador de cópia de segurança no seu serviço em nuvem através da CDN do Azure, com a opção de colocação em cache pretendida.

Na secção seguinte, posso irá mostrar como servir os scripts de agrupadas e minified e CSS através da CDN do Azure.

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrar o agrupamento do ASP.NET e minification com CDN do Azure
Scripts e CSS tramas alterar com pouca frequência e prime candidatos para a cache da CDN do Azure. A servir de toda a função Web através da CDN do Azure é a forma mais fácil para integrar o agrupamento e minification com CDN do Azure. No entanto, como poderá não querer fazê-lo, posso irá mostrar-lhe como fazê-lo enquanto preservar a experiência de develper pretendido de agrupamento do ASP.NET e minification, tais como:

* Experiência de modo de depuração excelente
* Implementação simplificada
* Atualizações imediatas para os clientes para as atualizações de versão do script/CSS
* Mecanismo de contingência quando ocorre uma falha de ponto final de CDN
* Minimizar a modificação do código

No **WebRole1** projeto que criou no [integrar um ponto final de CDN do Azure com o seu Web site do Azure e a servir conteúdo estático nas suas páginas Web do Azure CDN](#deploy), abra *App_Start\BundleConfig.cs* e observe o `bundles.Add()` chamadas de método.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

O primeiro `bundles.Add()` instrução adiciona um pacote de scripts no diretório virtual `~/bundles/jquery`. Em seguida, abra *Views\Shared\_layout. cshtml* para ver como a etiqueta de pacote de script é composta. Poderá encontrar a seguinte linha de código Razor:

    @Scripts.Render("~/bundles/jquery")

Quando este código Razor é executado na função da Web do Azure, irá compor um `<script>` etiquetas para o pacote de script semelhante ao seguinte:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando for executada no Visual Studio, escrevendo `F5`, irá compor cada ficheiro de script no pacote de individualmente (no caso, apenas um ficheiro de script está no grupo):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Isto permite-lhe o código JavaScript no seu ambiente de desenvolvimento de depuração, ao reduzir as ligações de cliente simultâneas (agrupamento) e melhorando o ficheiro transferir desempenho (minification) na produção. É uma funcionalidade excelente para preservar a com a integração da CDN do Azure. Além disso, uma vez que o pacote composto já contém uma cadeia de versão gerado automaticamente, que pretende replicar essa funcionalidade o sempre que atualizar a versão de jQuery através do NuGet, pode ser atualizado no lado do cliente logo que possível.

Siga os passos abaixo para integração ASP.NET agrupamento e minification com o ponto final de CDN.

1. Novamente no *App_Start\BundleConfig.cs*, modifique o `bundles.Add()` métodos a utilizar outro [construtor do pacote](http://msdn.microsoft.com/library/jj646464.aspx), que especifica um endereço CDN. Para fazer isto, substitua o `RegisterBundles` definição de método com o seguinte código:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Não se esqueça de substituir `<yourCDNName>` com o nome do seu CDN do Azure.
   
    No palavras simples, que define `bundles.UseCdn = true` e adicionar um URL de CDN crafted cuidadosamente para cada pacote. Por exemplo, o construtor com o código do primeiro:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    é o mesmo:
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    Este construtor indica ao ASP.NET agrupamento e minification compor os ficheiros de script individuais quando debugged localmente, mas utilize o endereço da CDN especificado ao aceder o script em questão. No entanto, tenha em atenção dois características importantes com este URL CDN cuidadosamente crafted:
   
   * A origem para este URL CDN é `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que é realmente o diretório virtual do pacote de script no seu serviço em nuvem.
   * Uma vez que estiver a utilizar o construtor CDN, a tag de script da CDN para o pacote já não contém a cadeia de versão gerado automaticamente no URL composto. Tem de gerar manualmente uma cadeia de versão exclusivo sempre que o pacote de script é modificado para forçar uma falha de acerto na cache na sua CDN do Azure. Ao mesmo tempo, esta cadeia de versão exclusivo têm de permanecer constante através de vida da implementação para maximizar acertos na cache na sua CDN do Azure, depois do pacote é implementado.
   * A cadeia de consulta v = < W.X.Y.Z > obtém do *Properties\AssemblyInfo.cs* no seu projeto de função da Web. Pode ter um fluxo de trabalho de implementação que inclua incrementando a versão de assemblagem sempre publicar no Azure. Em alternativa, pode modificar apenas *Properties\AssemblyInfo.cs* no seu projeto para aumentar automaticamente a cadeia de versão sempre que criar, utilizar o caráter universal ' *'. Por exemplo:
     
        [assemblagem: AssemblyVersion("1.0.0.*")]
     
     Quaisquer outra estratégia para simplificar a gerar uma cadeia exclusiva para a vigência de uma implementação irá funcionar aqui.
2. Acesso a home page e voltar a publicar o serviço em nuvem.
3. Ver o código HTML da página. Deve conseguir ver o URL de CDN composto, com uma cadeia de versão exclusivo sempre voltar a publicar alterações ao seu serviço de nuvem. Por exemplo:  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. No Visual Studio, depurar o serviço em nuvem no Visual Studio, escrevendo `F5`.,
5. Ver o código HTML da página. Verão ainda cada ficheiro de script composto individualmente, para que pode ter uma depuração consistente experiência no Visual Studio.  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo de contingência para o URL de CDN
Quando o ponto final de CDN do Azure falha por algum motivo, quer a página Web para ser inteligente aceder ao seu servidor Web de origem como a opção de contingência para carregar o JavaScript ou o arranque de configuração. É suficientemente grave percam imagens no seu Web site devido a indisponibilidade CDN, mas muito mais graves percam a funcionalidade de página fundamental fornecida pelos scripts e tramas.

O [pacote](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) classe contém uma propriedade denominada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) que permite-lhe configurar o mecanismo de contingência para a falha da CDN. Para utilizar esta propriedade, siga os passos abaixo:

1. No seu projeto de função da Web, abra *App_Start\BundleConfig.cs*, onde adicionou um URL de CDN em cada [construtor do pacote](http://msdn.microsoft.com/library/jj646464.aspx)e efetue as seguintes alterações realçadas para adicionar o mecanismo de contingência para os pacotes de predefinição:  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    Quando `CdnFallbackExpression` é não nula, script é injetado HTML para testar se o pacote é carregado com êxito e, se não, o acesso o pacote diretamente a partir do servidor de Web de origem. Esta propriedade tem de ser definido como uma expressão de JavaScript testa se o respetivo pacote CDN é carregado corretamente. A expressão necessária para cada pacote de teste difere de acordo com o conteúdo. Para os pacotes de predefinição acima:
   
   * `window.jquery`está definido no jquery-{version}. js
   * `$.validator`está definido no jquery.validate.js
   * `window.Modernizr`está definido no modernizer-{version}. js
   * `$.fn.modal`está definido no bootstrap.js
     
     Poderá ter reparado posso não tenha definido CdnFallbackExpression para o `~/Cointent/css` pacote. Isto acontece porque atualmente não há um [erros nas System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) que injects um `<script>` etiqueta para o CSS contingência em vez do esperado `<link>` etiquetas.
     
     No entanto, há um bom [estilo pacote contingência](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferecidas pelo [Ember consultadoria grupo](https://github.com/EmberConsultingGroup).
2. Para utilizar a solução para CSS, crie um novo ficheiro de CS no seu projeto de função de Web *App_Start* pasta denominada *StyleBundleExtensions.cs*e substitua o respetivo conteúdo com o [código a partir do GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).
3. No *App_Start\StyleFundleExtensions.cs*, mudar o nome do espaço de nomes para o nome da função da Web (por exemplo, **WebRole1**).
4. Volte ao `App_Start\BundleConfig.cs` e modificar o último `bundles.Add` instrução com o seguinte código realçado:  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    Este novo método de extensão utiliza a mesma ideia ao inserir o script no HTML para verificar o DOM para a um nome de classe correspondente, o nome da regra e o valor de regra definido no pacote do CSS e volta para o servidor de Web de origem, se não conseguir encontrar a correspondência.
5. Publicar novamente o serviço em nuvem e aceder a home page.
6. Ver o código HTML da página. Encontrará scripts injetados semelhante ao seguinte:    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    Tenha em atenção que o script injetado para o pacote do CSS contém ainda o remnant errant do `CdnFallbackExpression` propriedade na linha:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mas desde a primeira parte da | | expressão será sempre devolver verdadeira (na linha diretamente acima que), a função de document.write() nunca será executado.

## <a name="more-information"></a>Mais Informações
* [Descrição geral da rede do Azure de entrega de conteúdos (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Utilizar a CDN do Azure](cdn-create-new-endpoint.md)
* [Agrupamento do ASP.NET e Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
