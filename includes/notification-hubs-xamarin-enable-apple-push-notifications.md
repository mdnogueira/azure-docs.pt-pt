

Para registar a aplicação para as notificações push através do Serviço Apple Push Notification (APNS), tem de criar um novo certificado push, o ID da Aplicação e o perfil de aprovisionamento para o projeto no portal de programador da Apple. O ID da Aplicação irá conter as definições de configuração que permitem que a sua aplicação envie e receba as notificações push. Estas definições irão incluir o certificado de notificação push necessário para autenticar com o Serviço Apple Push Notification (APNS), ao enviar e receber as notificações push. Para mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](http://go.microsoft.com/fwlink/p/?LinkId=272584).

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Gere o ficheiro de Pedido de Assinatura de Certificado para o certificado push
Estes passos irão guiá-lo na criação do pedido de assinatura de certificado. Esta ação será utilizada para gerar um certificado push para ser utilizado com APNS.

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberta a partir da pasta **Utilitários** ou da pasta **Outros** no painel de arranque.
2. Clique em **Acesso Keychain**, expanda **Assistente de Certificado** e, em seguida, clique em **Pedir um Certificado a uma Autoridade de Certificação...**
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione o **Endereço de E-mail do Utilizador** e o **Nome Comum**. Verifique se **Guardar no disco** está selecionado e clique em **Continuar**. Deixe o campo **Endereço de E-mail da AC** em branco, uma vez que não é necessário.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Escreva um nome para o ficheiro de Solicitação de Assinatura do Certificado (CSR) em **Guardar Como**, selecione a localização em **Onde** e clique em **Guardar**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Esta ação guarda o ficheiro CSR na localização selecionada; a localização predefinida é no Ambiente de Trabalho. Memorize a localização escolhida deste ficheiro.

#### <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push
Crie um novo ID de Aplicação Explícito para a sua aplicação com a Apple e configure-a para as notificações push.  

1. Navegue até ao [Portal de Aprovisionamento do iOS ](http://go.microsoft.com/fwlink/p/?LinkId=272456) no Centro de Programadores da Apple, inicie sessão com o seu ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicações** e, finalmente, clique no sinal **+** para registar uma nova aplicação.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. Atualize os três campos seguintes para a nova aplicação e clique em **Continuar**:
   
   * **Nome**: escreva um nome descritivo para a aplicação no campo **Nome**, na secção **Descrição do ID da Aplicação**.
   * **Identificador do Pacote**: na secção **ID da Aplicação Explícito**, introduza um **Identificador do Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [App Distribution Guide (Guia de Distribuição de Aplicações)](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Esta ação tem de corresponder ao que também já é utilizado no projeto XCode, Xamarin ou Cordova para a sua aplicação.
   * **Notificações Push**: marque a opção **Notificações Push** na secção **Serviços Aplicacionais**.
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. No ecrã Confirme o seu ID da Aplicação, consulte as definições e depois as ter verificado, clique em **Submeter**
4. Assim que submeter o novo ID da Aplicação, verá o ecrã **Registo concluído**. Clique em **Concluído**.
5. No Centro de Programadores, em IDs de Aplicações, localize o ID da aplicação que acabou de criar e clique na respetiva linha. Se clicar na linha do ID da aplicação, verá os detalhes dessa aplicação. Clique no botão **Editar** na parte inferior.
6. Desloque-se para a parte inferior do ecrã e clique no botão **Criar Certificado…** na secção **Certificado SSL do Push de Programação**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This will display the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.
   > 
   > 
7. Clique em **Escolher Ficheiro**, navegue para a localização onde guardou o CSR para o seu certificado push. Em seguida, clique em **Gerar**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. Uma vez criado o certificado pelo portal, clique no botão **Transferir**.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Por predefinição, o ficheiro transferido, um certificado de desenvolvimento, tem o nome **aps_development.cer**.
   > 
   > 
9. Faça duplo clique no certificado push **aps_development.cer** transferido. Esta ação instala o novo certificado na Keychain, como mostrado abaixo:
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > O nome do seu certificado poderá ser diferente, mas terá como prefixo “**Apple Development iOS Push Services:**”.
   > 
   > 
10. No Acesso Keychain, clique com o rato premindo a tecla control no novo certificado push que criou na categoria **Certificados**. Clique em **Exportar**, dê um nome ao ficheiro, selecione o formato **.p12** e clique em **Guardar**.
    
    Lembre-se do nome do ficheiro e da localização do certificado push .p12 exportado. Será utilizado para ativar a autenticação com APNS ao carregá-lo no Portal Clássico do Azure.

#### <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Aprovisionamento do iOS</a>, selecione **Perfis de Aprovisionamento**, selecione **Todos** e clique no botão **+** para criar um novo perfil. O Assistente **Adicionar Perfil de Aprovisionamento do iOS** será iniciado.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Selecione **Desenvolvimento de Aplicações iOS**, em **Programação**, como o tipo de perfil de aprovisionamento e clique em **Continuar**.
3. Em seguida, selecione o ID da aplicação que acabou de criar na lista pendente **ID da Aplicação** e clique em **Continuar**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. No ecrã **Selecionar certificados**, selecione o seu certificado de programação utilizado para a assinatura de código e clique em **Continuar**. Este é um certificado de assinatura, e não o push que acabou de criar.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Em seguida, selecione os **Dispositivos** a utilizar para os testes e clique em **Continuar**
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)



<!--HONumber=Feb17_HO1-->


