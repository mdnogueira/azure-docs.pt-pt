As aplicações .NET podem utilizar o cliente de cache **StackExchange.Redis**, que poderá ser configurado no Visual Studio com um pacote NuGet que simplifica a configuração das aplicações cliente de cache. 

> [!NOTE]
> Para obter mais informações, veja a página do GitHub [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) e a [Documentação do cliente de cache StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
> 
> 

Para configurar uma aplicação cliente no Visual Studio com o pacote NuGet StackExchange.Redis, clique com o botão direito do rato no projeto em **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**. 

![Gerir pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Introduza **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão pretendida apresentada nos resultados e clique em **Instalar**.

> [!NOTE]
> Se preferir utilizar uma versão da biblioteca de clientes **StackExchange.Redis** com nome seguro, escolha **StackExchange.Redis.StrongName**; caso contrário, escolha **StackExchange.Redis**.
> 
> 

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias para a sua aplicação cliente aceder à Cache de Redis do Azure com o cliente de cache StackExchange.Redis.

> [!NOTE]
> Se configurou anteriormente o seu projeto para utilizar StackExchange.Redis, pode procurar atualizações para o pacote a partir do **Gestor de Pacotes NuGet**. Para procurar e instalar versões atualizadas do pacote NuGet do StackExchange.Redis, clique em **Atualizações** na janela **Gestor de Pacotes NuGet**. Se estiver disponível uma atualização do pacote NuGet do StackExchange.Redis, pode atualizar o seu projeto para utilizar a versão atualizada.
> 
> 

Também é possível instalar o pacote de StackExchange.Redis NuGet clicando em **Gestor de Pacotes NuGet**, **Gestor de Pacotes** a partir do menu **Ferramentas** e executar o seguinte comando a partir da janela **Consola do Gestor de Pacotes**.
    
```
Install-Package StackExchange.Redis
```
