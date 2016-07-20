
1. Visite o [Portal do Azure]. Clique em **Procurar Tudo** > **Mobile Apps** > o back-end que acabou de criar. Nas definições da aplicação móvel, clique em **Início Rápido** > **Cordova**. Em **Configurar a aplicação cliente**, selecione **Criar uma Nova Aplicação** e clique em **Transferir**. Esta ação transfere um projeto Cordova concluído para uma aplicação pré-configurada para ligar ao back-end.

2. Descompacte o ficheiro ZIP transferido para um diretório no seu disco rígido.

3. Abra o projeto com o **Visual Studio**.  Clique em **Abrir** > **Projeto/Solução...**

4. Localize o ficheiro _sitename_.sln e clique em **Abrir**.

5. O emulador predefinido é **Ripple – Nexus (Galaxy)**.  Clique na seta para baixo junto do emulador e selecione **Emulador do Google Android**.

6. Clique em **Emulador do Google Android**.  O projeto será criado e executado.  Poderá ver um aviso de segurança de rede a no Emulador do Google Android solicitando acesso à rede.  Eventualmente, será apresentado o Emulador do Google Android e a aplicação executada.

7. Na aplicação, digite um texto significativo, como _Concluir o tutorial_ e, em seguida, clique no botão “Adicionar”. Esta ação envia um pedido POST ao back-end do Azure que implementou anteriormente. Os dados de inserções de back-end do pedido encontram-se na tabela TodoItem SQL e devolve informações sobre os itens recentemente armazenados na aplicação móvel. A aplicação móvel apresenta estes dados na lista.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Portal do Azure]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


