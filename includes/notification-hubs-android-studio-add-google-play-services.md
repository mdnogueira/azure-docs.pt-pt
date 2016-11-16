1. Abra o Gestor do Android SDK clicando no ícone na barra de ferramentas do Android Studio ou em **Ferramentas** -> **Android** -> **Gestor de SDKs** no menu. Localize a versão de destino do Android SDK utilizada no projeto, abra-a clicando em **Mostrar Detalhes do Pacote** e escolha **APIs do Google**, se ainda não estiver instalada.
2. Clique no separador **Ferramentas SDK **. Se ainda não instalou o Serviço do Google Play, clique em **Serviços do Google Play**, como mostrado abaixo. Em seguida, clique em **Aplicar** para instalar. 
   
    Anote o caminho do SDK, para utilização num passo posterior. 
   
       ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Abra o ficheiro **build.gradle** no diretório da aplicação.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. Adicione esta linha sob *dependências*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. Clique no ícone **Sincronizar Projeto com os Ficheiros Gradle** na barra de ferramentas.
6. Abra **AndroidManifest.xml** e adicione esta etiqueta à etiqueta *aplicação*.
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />



<!--HONumber=Nov16_HO2-->


