| Recurso | Gratuito | Partilhado (Pré-visualização) | Básica | Standard | Premium (pré-visualização)</th> |
| --- | --- | --- | --- | --- | --- |
| [Aplicações Web, móveis ou API](https://azure.microsoft.com/services/app-service/) por [plano do App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Ilimitados<sup>2</sup> |Ilimitados<sup>2</sup> |Ilimitados<sup>2</sup> |
| [As Logic apps](https://azure.microsoft.com/services/app-service/logic/) por [plano do App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 por núcleo |20 por núcleo |
| [Plano do Serviço de Aplicações](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |
| Tipo de instância de computação |Partilhado |Partilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |
| [Escalamento horizontal](../articles/app-service/web-sites-scale.md) (máximo de instâncias) |1 partilhado |1 partilhado |3 dedicado<sup>3</sup> |10 dedicado<sup>3</sup> |20 dedicado (50 no ASE)<sup>3,4</sup> |
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Tempo de CPU (5 min)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pague em padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitada, pay às taxas padrão |Ilimitada, pay às taxas padrão |
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pague em padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a> |Ilimitada, pay às taxas padrão |Ilimitada, pay às taxas padrão |
| Memória (1 hora) |1024 MB por plano do App Service |1024 MB por aplicação |N/D |N/D |N/D |
| Largura de Banda |165 MB |Ilimitado, [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) aplicar |Ilimitada, são aplicáveis tarifas de transferência de dados |Ilimitada, são aplicáveis tarifas de transferência de dados |Ilimitada, são aplicáveis tarifas de transferência de dados |
| Arquitetura da aplicação |32 bits |32 bits |32-bit/64 bits |32-bit/64 bits |32-bit/64 bits |
| Web Sockets por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |
| Em simultâneo [depurador ligações](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicação |1 |1 |1 |5 |5 |
| [subdomínio azurewebsites.NET com FTP/S e SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Domínio personalizado](../articles/app-service/app-service-web-tutorial-custom-domain.md) suportar | |X |X |X |X |
| Domínio personalizado [suporte SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Ilimitados ligações de SNI SSL |Ilimitados SSL SNI e ligações de IP SSL 1 incluídos |Ilimitados SSL SNI e ligações de IP SSL 1 incluídos |
| Load Balancer Integrado | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Cópias de segurança agendadas](../articles/app-service/web-sites-backup.md) | | | |Uma vez por dia |Uma vez a cada 5 minutos<sup>8</sup> |
| [Escala automática](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>9</sup> |X |X |X |X |X |
| [O agendador do Azure](https://azure.microsoft.com/services/scheduler/) suportar | |X |X |X |X |
| [Monitorização de pontos finais](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Ranhuras de teste](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Domínios personalizados por aplicação</a> | |500 |500 |500 |500 |
| SLA | |<p> |99,9% |99.95%<sup>10</sup> |99.95%<sup>10</sup> |

<sup>1</sup>aplicações quotas de armazenamento são e por plano do App Service, a menos que haja aviso em contrário.  
<sup>2</sup>o número real de aplicações que podem alojar nestas máquinas depende a atividade das aplicações, o tamanho das instâncias da máquina e a utilização de recursos correspondentes.  
<sup>3</sup>instâncias dedicadas podem ser de tamanhos diferentes. Consulte [preços do App Service](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) para obter mais detalhes.  
<sup>4</sup>escalão premium permite até 50 calcula instâncias (sujeitos a disponibilidade) e 500 GB de espaço em disco ao utilizar ambientes do App Service e 20 de computação de instâncias e 250 GB de armazenamento; caso contrário.  
<sup>5</sup>o limite de armazenamento é o tamanho total do conteúdo em todas as aplicações no mesmo plano de serviço de aplicações. Mais opções de armazenamento estão disponíveis no [ambiente de serviço de aplicações](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>estes recursos estão limitados por recursos físicos nas instâncias do dedicado (o tamanho da instância e o número de instâncias).  
<sup>7</sup>se pode dimensionar uma aplicação de camada básica para duas instâncias, tiver 350 ligações em simultâneo para cada uma das duas instâncias.  
<sup>8</sup>escalão premium permite que os intervalos de cópia de segurança para baixo até a cada 5 minutos quando utilizar ambientes do App Service e 50 vezes por dia, caso contrário.  
<sup>9</sup>executar executáveis personalizados e/ou de scripts a pedido, com base numa agenda, ou instância continuamente como uma tarefa em segundo plano dentro do seu serviço de aplicações. Sempre Ativo é necessário para a execução contínua do WebJobs. É necessário o Azure Scheduler, Gratuito ou Padrão, para WebJobs agendados. Não existe nenhum limite predefinido do número de trabalhos Web que pode ser executado numa instância do serviço de aplicações, mas existem limites práticos que dependem de que o código de aplicação está a tentar fazer.   
<sup>10</sup>SLA de 99,95% fornecido para implementações que utilizem várias instâncias com o Azure Traffic Manager configurado para ativação pós-falha.  

