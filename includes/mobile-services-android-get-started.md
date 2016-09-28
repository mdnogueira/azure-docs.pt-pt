A etapa final deste tutorial consiste em criar e executar a sua nova aplicação.

### Carregar o projeto para o Android Studio e sincronizar o Gradle

1. Navegue até à localização onde guardou os ficheiros comprimidos do projeto e expanda os ficheiros no computador, no diretório de projetos Android Studio.

2. Abra o Android Studio. Se estiver a trabalhar com um projeto e este aparecer, feche o projeto (Ficheiro = > Fechar Projeto).

3. Selecione **Abrir um projeto existente do Android Studio**, navegue até à localização do projeto e clique em **OK**. Este procedimento permitirá carregar o projeto e começar a sincronizá-lo com o Gradle.

    ![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Aguarde que a atividade de sincronização do Gradle fique concluída. Se vir um erro “falha ao localizar o destino”, significa que a versão utilizada no Android Studio não corresponde à do exemplo. O modo mais fácil de corrigir este problema é clicar na ligação **Instalar platforma(s) em falta e sincronizar projeto** na mensagem de erro. Poderá obter mensagens de erro da versão adicionais, basta repetir este processo até que deixem de aparecer erros.
    - Há um outro modo de corrigir esta situação caso pretenda executar a versão “mais recente e melhor” do Android. Pode atualizar o **targetSdkVersion** no ficheiro *build.gradle* no diretório *app* para que corresponda à versão já instalada no computador, que pode descobrir clicando no ícone **SDK Manager** e vendo a versão que está listada. Em seguida, prima **Sincronizar Projeto com os Ficheiros do Gradle**. Se for apresentada uma mensagem de erro relativamente à versão das Ferramentas de Compilação, proceda da mesma forma para corrigir o erro.

### Executar a aplicação

Pode executar a aplicação com o emulador ou com um dispositivo real.

1. Para executar num dispositivo, ligue-o ao computador com um cabo USB. Tem de [configurar o dispositivo para programação](https://developer.android.com/training/basics/firstapp/running-app.html). Se estiver a programar num computador Windows, deverá também transferir e instalar um controlador USB.

2. Para executar com o emulador do Android, tem de definir, no mínimo, um Dispositivo Virtual Android (AVD). Clique no ícone do Gestor do AVD para criar e gerir estes dispositivos.

3. No menu **Executar**, clique em **Executar** para iniciar o projeto e escolha um dispositivo ou emulador na caixa de diálogo apresentada.

4. Quando vir a aplicação, digite um texto significativo, como _Concluir o tutorial_, e clique em **Adicionar**.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo serviço móvel e os dados são apresentados na lista.

    > [AZURE.NOTE] Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, que poderá encontrar no ficheiro ToDoActivity.java.

8. No Portal Clássico do Azure, clique no separador **Dados** e, em seguida, na tabela **TodoItems**.

    ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

    ![](./media/mobile-services-android-get-started/mobile-data-browse.png)


<!--HONumber=Sep16_HO3-->


