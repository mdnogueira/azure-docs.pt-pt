
1. Visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Mobile Apps** > o back-end que acabou de criar. Nas definições da aplicação móvel, clique em **Início Rápido** > **Cordova**. Em **Configurar a aplicação cliente**, selecione **Criar uma Nova Aplicação** e clique em **Transferir**. Esta ação transfere um projeto Cordova concluído para uma aplicação pré-configurada para ligar ao back-end.

2. Descompacte o ficheiro ZIP transferido para um diretório no disco rígido, navegue para o ficheiro de solução (.sln) e abra-o com o Visual Studio.

5. No Visual Studio, selecione a plataforma de solução (Android, iOS ou Windows) no menu pendente junto à seta de início e, em seguida, selecione um dispositivo de implementação ou emulador específicos, ao clicar no menu pendente na seta verde. Tenha em atenção que pode utilizar a plataforma Android e o emulador Ripple predefinidos. Tutoriais mais avançados irão requerer que selecione um dispositivo ou emulador suportados. 

6. Prima F5 ou clique na seta verde para criar e executar a aplicação Cordova. Se for apresentada uma caixa de diálogo de segurança no emulador a solicitar acesso à rede, aceite.   

7. Depois de iniciada a aplicação no dispositivo ou no emulador, introduza texto relevante em **Introduzir novo texto**, como _Concluir o tutorial_ e, em seguida, clique no botão **Adicionar**.  
Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem na Base de Dados SQL e devolvem informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)
    
8. Repita os três passos anteriores para cada plataforma de dispositivo que pretende suportar.

[Portal do Azure]: https://portal.azure.com/


<!--HONumber=Sep16_HO3-->


