Para criar uma cache, primeiro inicie sessão no [portal do Azure](https://portal.azure.com) e clique em **Novo** > **Bases de Dados** > **Cache de Redis**.

> [!NOTE]
> Se não tiver uma conta do Azure, pode [Criar uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) em apenas alguns minutos.
> 
> 

![Nova cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

> [!NOTE]
> Para além de criar caches no Portal do Azure, pode também criá-las com os modelos do Resource Manager, o PowerShell ou a CLI do Azure.
> 
> * Para criar uma cache com os modelos do Resource Manager, veja [Criar uma cache de Redis com um modelo](../articles/redis-cache/cache-redis-cache-arm-provision.md).
> * Para criar uma cache com o Azure PowerShell, veja [Gerir Cache de Redis do Azure com o Azure PowerShell](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md).
> * Para criar uma cache com a CLI do Azure, veja [Como criar e gerir uma Cache de Redis do Azure com a Interface de Linha de Comandos do Azure (CLI do Azure)](../articles/redis-cache/cache-manage-cli.md).
> 
> 

No painel **Nova Cache de Redis**, especifique a configuração pretendida para a cache.

![Criar cache](media/redis-cache-create/redis-cache-cache-create.png) 

* Em **Nome DNS**, introduza um nome de cache exclusivo a utilizar para o ponto final da cache. O nome da cache tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome da cache não pode começar nem terminar com o caráter `-` e os carateres `-` consecutivos não são válidos.
* Para **Subscrição**, selecione a subscrição do Azure que pretende utilizar para a cache. Se a sua conta tiver apenas uma subscrição, esta será selecionada automaticamente e não será apresentado o menu pendente **Subscrição**.
* Em **Grupo de recursos**, selecione ou crie um grupo de recursos para a sua cache. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md). 
* Utilize **Localização** para especificar a localização geográfica onde a sua cache será alojada. Para obter o melhor desempenho, a Microsoft recomenda vivamente que crie a cache na mesma região da aplicação cliente da cache.
* Utilize **Escalão de preço** para selecionar o tamanho e as funcionalidades que pretende para a cache.
* O **Cluster de Redis** permite-lhe criar caches superiores a 53 GB e dividir os dados entre os diversos nós de Redis. Para obter mais informações, veja [Como configurar o clustering de uma Cache de Redis do Azure Premium](../articles/redis-cache/cache-how-to-premium-clustering.md).
* A **Persistência Redis** permite-lhe persistir a cache numa conta de armazenamento do Azure. Para obter instruções sobre como configurar a persistência, veja [Como configurar a persistência para uma Cache de Redis do Azure Premium](../articles/redis-cache/cache-how-to-premium-persistence.md).
* A **Rede Virtual** fornece segurança e isolamento melhorados ao restringir o acesso à cache apenas aos clientes dentro da Rede Virtual do Azure especificada. Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis. Para obter mais informações, veja [Como configurar o suporte da Rede Virtual de uma Cache de Redis do Azure Premium](../articles/redis-cache/cache-how-to-premium-vnet.md).
* Por predefinição, o acesso não SSL está desativado para as novas caches. Para ativar a porta não SSL, verifique **Unblock port 6379 (not SSL encrypted) (Desbloquear porta 6379 (sem encriptação SSL))**.

Uma vez configuradas as opções da nova cache, clique em **Criar**. A criação da cache pode demorar alguns minutos. Para verificar o estado, pode monitorizar o progresso no Startboard. Uma vez criada, a nova cache tem um estado **Em Execução** e está pronta para ser utilizada com as [predefinições](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

![Cache criada](media/redis-cache-create/redis-cache-cache-created.png)

