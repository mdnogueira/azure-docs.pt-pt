1. Inicie sessão no [Portal do Azure].
2. Clique em **+NOVO** > **Web + Móvel** > **Aplicação Móvel** e indique um nome para o back-end da Aplicação Móvel.
3. Para o **Grupo de Recursos**, selecione um grupo de recursos existente ou crie um novo (utilizando o mesmo nome que a sua aplicação). 
   
    Pode selecionar outro plano do Serviço de Aplicações ou criar um novo. Para obter mais informações sobre os planos dos Serviços de Aplicações e como criar um novo plano com um escalão de preço diferente na localização pretendida, veja [Azure App Service plans in-depth overview (Descrição geral aprofundada dos planos do Serviço de Aplicações do Azure)](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Para o **Plano do Serviço de Aplicações**, encontra-se selecionado o plano predefinido (no [Escalão Standard](https://azure.microsoft.com/pricing/details/app-service/)). Também pode selecionar um plano diferente ou [criar um novo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). As definições do plano do Serviço de Aplicações determinam [a localização, as funcionalidades, o custo e os recursos de computação](https://azure.microsoft.com/pricing/details/app-service/) associados à aplicação. 
   
    Depois de decidir o plano, clique em **Criar**. Esta ação cria o back-end da Aplicação Móvel. 
5. No painel **Definições** do back-end da nova Aplicação Móvel, clique em **Início rápido** > plataforma da sua aplicação cliente > **Ligar uma base de dados**. 
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
6. No painel **Adicionar ligação de dados**, clique em **SQL Database** > **Criar uma nova base de dados**, escreva o **Nome** da base de dados, escolha um escalão de preço e clique em **Servidor**.  Pode reutilizar esta nova base de dados. Se já tiver uma base de dados na mesma localização, pode escolher **Utilizar uma base de dados existente**. Não é recomendado utilizar uma base de dados numa localização diferente devido aos custos da largura de banda e latência superior.
   
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
7. No painel **Novo servidor**, escreva um nome de servidor exclusivo no campo **Nome do servidor**, forneça um início de sessão e palavra-passe, marque **Permitir o acesso dos serviços do Azure ao servidor** e clique em **OK**. Esta ação cria a nova base de dados.
8. De regresso ao painel **Adicionar ligação de dados**, clique em **Cadeia de ligação**, escreva os valores do início de sessão e da palavra-passe para a sua base de dados e clique em **OK**. Antes de prosseguir, aguarde alguns minutos até a base de dados ser implementada com êxito.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
