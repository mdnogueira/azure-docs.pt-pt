<properties 
    pageTitle="Adicionar funcionalidades à sua primeira aplicação Web" 
    description="Adicione funcionalidades interessantes à sua primeira aplicação Web em apenas alguns minutos." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin" 
    manager="wpickett" 
    editor="" 
/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="05/12/2016" 
    ms.author="cephalin"
/>

# Adicionar funcionalidades à sua primeira aplicação Web

Em [Implementar uma aplicação Web no Azure em 5 minutos](app-service-web-get-started.md), implementou uma aplicação Web de exemplo no [App Service do Azure](../app-service/app-service-value-prop-what-is.md). Neste artigo, irá adicionar rapidamente algumas funcionalidades excelentes à sua aplicação Web implementada. Em apenas alguns minutos, irá:

- impor a autenticação para os seus utilizadores
- dimensionar a sua aplicação automaticamente
- receber alertas sobre o desempenho da aplicação

Independentemente da aplicação de exemplo implementada no artigo anterior, pode acompanhar o tutorial.

As três atividades deste tutorial são apenas alguns exemplos de várias funcionalidades que pode obter quando coloca a sua aplicação Web no App Service. Muitas das funcionalidades estão disponíveis no Escalão **gratuito** (no qual a sua primeira aplicação Web está a ser executada), sendo que pode utilizar os créditos das avaliação gratuita para experimentar funcionalidades que requerem escalões de preço superiores. Pode ter a certeza de que a sua aplicação Web permanece no Escalão **gratuito**, exceto se a alterar explicitamente para um escalão de preço diferente.

>[AZURE.NOTE] A aplicação Web que criou com a CLI do Azure é executada no Escalão **gratuito**, que apenas permite uma instância de VM partilhada com quotas de recursos. Para obter mais informações sobre o que obtém com o Escalão **gratuito**, consulte [Limites do App Service](../azure-subscription-service-limits.md#app-service-limits).

## Autenticar os utilizadores

Agora, vamos ver como é fácil adicionar a autenticação à sua aplicação (leia mais em [Autenticação/Autorização do App Service](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. No painel do portal relativo à sua aplicação, que acabou de abrir, clique em **Definições** > **Autenticação/Autorização**.  
    ![Autenticar – painel de definições](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Clique em **Ativar** para ativar a autenticação.  
    
4. Em **Fornecedores de Autenticação**, clique em **Azure Active Directory**.  
    ![Autenticar – selecione Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. No painel **Definições do Azure Active Directory**, clique em **Express** e, em seguida, em **OK**. As predefinições criam uma nova aplicação do Azure AD no seu diretório predefinido.  
 ![Autenticar – configuração express](./media/app-service-web-get-started/aad-login-express.png)

6. Clique em **Guardar**.  
    ![Autenticar – guardar configuração](./media/app-service-web-get-started/aad-login-save.png)

    Depois de concluir a alteração com êxito, o ícone de sino fica verde e é apresentada uma mensagem amigável.

7. Novamente no painel do portal da sua aplicação, clique na ligação do **URL** ligação (ou em **Procurar** na barra de menus). A ligação é um endereço HTTP.  
    ![Autenticar – navegue até ao URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Mas, depois de abrir a aplicação num novo separador, a caixa do URL redireciona várias vezes e termina na sua aplicação com um endereço HTTPS. O que está a ver é que já tem sessão iniciada na sua subscrição do Azure e é automaticamente autenticado na aplicação.  
    ![Autenticar – com sessão iniciada](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Por isso, se abrir agora uma sessão não autenticada num browser diferente, verá um ecrã de início de sessão quando navegar para o mesmo URL.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Se nunca fez nada com o Azure Active Directory, o diretório predefinido poderá não ter quaisquer utilizadores do Azure AD. Neste caso, provavelmente, a única conta no mesmo é a conta Microsoft com a sua subscrição do Azure. Este é o motivo por que a sua sessão na aplicação foi automaticamente iniciada no mesmo browser anteriormente. Também pode utilizar essa mesma conta Microsoft para iniciar sessão nesta página de início de sessão.

Parabéns! Está a autenticar todo o tráfego para a sua aplicação Web.

É possível que tenha reparado no painel **Autenticação/Autorização** que pode fazer muito mais, nomeadamente:

- Ativar o início de sessão nas redes sociais
- Ativar várias opções de início de sessão
- Alterar o comportamento predefinido quando as pessoas navegam pela primeira vez para a sua aplicação

O App Service fornece uma solução “chave na mão” para algumas das necessidades comuns de autenticação, pelo que não precisa de fornecer a lógica de autenticação. Para obter mais informações, consulte [Aplicação/Autorização do App Service](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## Dimensionar a sua aplicação automaticamente com base no pedido

Em seguida, vamos dimensionar a sua aplicação automaticamente, de modo a que a mesma ajuste a sua capacidade de forma automática para satisfazer o pedido do utilizador (leia mais em [Dimensionar o escalão de preço no App Service do Azure](app-service-scale) e [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-portal/insights-how-to-scale.md)). 

Resumidamente, pode dimensionar a sua aplicação Web de duas formas:

- [Aumentar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e funcionalidades adicionais, como VMs dedicadas, domínios e certificados personalizados, ranhuras de teste, dimensionamento automático e muito mais. Pode aumentar horizontalmente ao alterar o escalão de preço do plano do App Service ao qual pertence a aplicação.
- [Aumentar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumentar o número de instâncias de VM que executam a aplicação.
É possível aumentar horizontalmente até 50 instâncias, consoante o escalão de preço.

Passemos de imediato à configuração do dimensionamento automático.

1. Em primeiro lugar, vamos aumentar verticalmente para ativar o dimensionamento automático. No painel do portal da sua aplicação, clique em **Definições** > **Aumentar Verticalmente (Plano do App Service)**.  
    ![Aumentar verticalmente – painel de definições](./media/app-service-web-get-started/scale-up-settings.png)

2. Desloque-se e selecione o escalão **S1 Standard**, o escalão mais baixo que suporta o dimensionamento automático (dentro de um círculo na captura de ecrã) e, em seguida, clique em **Selecionar**.  
    ![Aumentar verticalmente – escolher escalão](./media/app-service-web-get-started/scale-up-select.png)

    A ação de aumentar verticalmente está concluída.
    
    >[AZURE.IMPORTANT] Este escalão consome os créditos da avaliação gratuita. Se tiver uma conta de pagamento por utilização, são aplicadas despesas à mesma.
    
3. Em seguida, vamos configurar o dimensionamento automático. No painel do portal da sua aplicação, clique em **Definições** > **Aumentar Horizontalmente (Plano do App Service)**.  
    ![Aumentar horizontalmente – painel de definições](./media/app-service-web-get-started/scale-out-settings.png)

4. Altere **Dimensionar por** para **Percentagem de CPU**. Os controlos de deslize por baixo da lista pendente são atualizados em conformidade. Em seguida, defina um intervalo de **Instâncias** entre **1** e **2** e um **intervalo de Destinos** entre **40** e **80**. Faça-o ao escrever nas caixas ou ao mover os controlos de deslize.  
 ![Aumentar horizontalmente – configurar o dimensionamento automático](./media/app-service-web-get-started/scale-out-configure.png)
    
    Com base nesta configuração, a aplicação aumenta horizontalmente de forma automática quando a utilização da CPU é superior a 80% e reduz horizontalmente quando a utilização da CPU é inferior a 40%. 
    
5. Clique em **Guardar** na barra de menus.

Parabéns! A sua aplicação está a efetuar o dimensionamento automático.

É possível que tenha reparado no painel **Definições de Dimensionamento** que pode fazer muito mais, nomeadamente:

- Dimensionar manualmente para um número específico de instâncias
- Dimensionar por outras métricas de desempenho, tais como a fila de discos ou a percentagem de memória
- Personalizar o comportamento de dimensionamento quando é acionada uma regra de desempenho
- Dimensionar automaticamente conforme agendado
- Definir o comportamento de dimensionamento automático num evento futuro

Para obter mais informações sobre como aumentar verticalmente a sua aplicação, consulte [Dimensionar o escalão de preço no App Service do Azure](../app-service/app-service-scale.md). Para obter mais informações sobre como aumentar horizontalmente, consulte [Dimensionar a contagem de instâncias manual ou automaticamente](../azure-portal/insights-how-to-scale.md).

## Receber alertas para a sua aplicação

Agora que a aplicação está a efetuar o dimensionamento automático, o que acontece quando atinge o número máximo de instâncias (2) e a CPU está acima da utilização pretendida (80%)? Pode configurar um alerta (ler mais em [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md)) para o informar sobre esta situação, de modo a poder aumentar verticalmente/horizontalmente ainda mais a sua aplicação, por exemplo. Vamos configurar rapidamente um alerta para este cenário.

1. No painel do portal da sua aplicação, clique em **Ferramentas** > **Alertas**.  
    ![Alertas – painel de definições](./media/app-service-web-get-started/alert-settings.png)

2. Clique em **Adicionar alerta**. Em seguida, na caixa **Recurso**, selecione o recurso que termina com **(serverfarms)**. Este é o seu plano do App Service.  
    ![Alertas – adicionar alerta para o plano do App Service](./media/app-service-web-get-started/alert-add.png)

3. Especifique o **Nome** como `CPU Maxed`, a **Métrica** como **Percentagem de CPU** e o **Limiar** como `90`. Em seguida, selecione **Proprietários de e-mail, contribuintes e leitores** e, por fim, clique em **OK**.   
 ![Alertas – configurar alerta](./media/app-service-web-get-started/alert-configure.png)
    
    Quando o Azure conclui a criação do alerta, este é apresentado no painel **Alertas**.  
    ![Alertas – vista concluída](./media/app-service-web-get-started/alert-done.png)

Parabéns! Agora, já recebe alertas. 

Esta definição de alerta verifica a cada cinco minutos a utilização da CPU. Se esse número ultrapassar os 90%, recebe um alerta por e-mail, juntamente com qualquer pessoa que esteja autorizada. Para ver todas as pessoas autorizadas a receber os alertas, volte para o painel do portal da sua aplicação e clique no botão **Aceder**.  
![Ver quem recebe alertas](./media/app-service-web-get-started/alert-rbac.png)

Deverá ver que os **Administradores da subscrição** já são o **Proprietário** da aplicação. Este grupo incluí-lo-ia se fosse o administrador da conta da sua subscrição do Azure (por exemplo, a subscrição de avaliação). Para obter mais informações sobre o controlo de acesso baseado em funções do Azure, consulte [Controlo de Acesso Baseado em Funções do Azure](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] As regras de alerta são uma funcionalidade do Azure. Para obter mais informações, consulte [Receber notificações de alerta](../azure-portal/insights-receive-alert-notifications.md). 

## Passos Seguintes

No processo de configuração do alerta, é possível que tenha reparado num conjunto avançado de ferramentas no painel **Ferramentas**. Aqui, pode resolver problemas, monitorizar o desempenho, testar a existência de vulnerabilidades, gerir recursos, interagir com a consola da VM e adicionar extensões úteis. Convidamo-lo a clicar em cada uma destas ferramentas para detetar estas ferramentas simples, mas eficazes, sempre à sua disposição. 

Saiba como fazer mais com a sua aplicação implementada. Segue-se apenas uma lista parcial:

- [Comprar e configurar um nome de domínio personalizado](custom-dns-web-site-buydomains-web-app.md) – Compre um domínio apelativo para a sua aplicação Web em vez do domínio *.azurewebsites.net. Em alternativa, utilize um domínio que já tenha.
- [Configurar ambientes de teste](web-sites-staged-publishing.md) – Implemente a aplicação num URL de teste antes de a passar para produção. Atualize a sua aplicação Web online com confiança. Configure uma solução de DevOps elaborada com várias ranhuras de implementação. 
- [Configurar uma implementação contínua](web-sites-publish-source-control.md) – Integre a implementação da aplicação no seu sistema de controlo de origem. Implemente no Azure com cada consolidação.
- [Aceder a recursos no local](web-sites-hybrid-connection-get-started.md) – aceder a uma base de dados existente no local ou ao sistema de CRM.
- [Fazer uma cópia de segurança da aplicação](web-sites-backup.md) – configure a cópia de segurança e o restauro para a sua aplicação Web. Esteja preparado para falhas inesperadas e recupere.
- [Ativar registos de diagnóstico](web-sites-enable-diagnostic-log.md) – leia os registos de IIS a partir de rastreios do Azure ou da aplicação. Leia-os num fluxo, transfira-os ou migre-os para o [Application Insights](../application-insights/app-insights-overview.md) para uma análise “chave na mão”.
- [Analisar a aplicação quanto a vulnerabilidades](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
Analise a sua aplicação Web quanto a ameaças modernas utilizando o serviço fornecido pela [Tinfoil Security](https://www.tinfoilsecurity.com/).
- [Executar tarefas em segundo plano](../azure-functions/functions-overview.md) –execute tarefas de processamento de dados, relatórios, etc.
- [Saiba como funciona o App Service](../app-service/app-service-how-works-readme.md) 


<!--HONumber=Jun16_HO2-->


