
1. Na vista solução (ou **Explorador de soluções** no Visual Studio), clique com botão direito do **componentes** pasta, clique em **obter mais componentes...** , procure o **cliente Google Cloud Messaging** componentes e adicione-ao projeto.
2. Abra o ficheiro ToDoActivity.cs e adicione o seguinte utilizando a instrução para a classe:
   
        using Gcm.Client;
3. No **ToDoActivity** classe, adicione o novo código seguinte: 
   
        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();
   
        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }
   
    Isto permite-lhe aceder a instância de cliente para dispositivos móveis a partir do processo de serviço de push do processador.
4. Adicione o seguinte código para o **OnCreate** método, após o **MobileServiceClient** é criado:
   
       // Set the current instance of TodoActivity.
       instance = this;
   
       // Make sure the GCM client is set up correctly.
       GcmClient.CheckDevice(this);
       GcmClient.CheckManifest(this);
   
       // Register the app for push notifications.
       GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

O **ToDoActivity** está agora preparado para adicionar notificações push.

