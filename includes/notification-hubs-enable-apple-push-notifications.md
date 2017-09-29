

## <a name="generate-the-certificate-signing-request-file"></a>Gerar o ficheiro de Solicitação de Assinatura de Certificado
O Serviço Apple Push Notification (APNS) utiliza certificados para autenticar as suas notificações push. Siga estas instruções para criar o certificado push necessário para enviar e receber notificações. Para mais informações sobre estes conceitos, veja a documentação oficial do [Serviço Apple Push Notification](http://go.microsoft.com/fwlink/p/?LinkId=272584).

Crie o ficheiro de Solicitação de Assinatura do Certificado (CSR), que é utilizado pela Apple para gerar um certificado push assinado.

1. No Mac, execute a ferramenta de Acesso Keychain. Pode ser aberta a partir da pasta **Utilitários** ou da pasta **Outros** no painel de arranque.
2. Clique em **Acesso Keychain**, expanda **Assistente de Certificado** e, em seguida, clique em **Pedir um Certificado a uma Autoridade de Certificação...**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selecione o **Endereço de E-mail do Utilizador** e o **Nome Comum**. Verifique se **Guardar no disco** está selecionado e clique em **Continuar**. Deixe o campo **Endereço de E-mail da AC** em branco, uma vez que não é necessário.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Escreva um nome para o ficheiro de Solicitação de Assinatura do Certificado (CSR) em **Guardar Como**, selecione a localização em **Onde** e clique em **Guardar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Esta ação guarda o ficheiro CSR na localização selecionada; a localização predefinida é no Ambiente de Trabalho. Memorize a localização escolhida deste ficheiro.

Em seguida, irá registar a aplicação na Apple, ativar as notificações push e carregar este CSR exportado para criar um certificado push.

## <a name="register-your-app-for-push-notifications"></a>Registar a aplicação para notificações push
Para poder enviar notificações push para uma aplicação iOS, tem de registar a sua aplicação na Apple e registar-se também para notificações push.  

1. Se ainda não registou a aplicação, navegue até ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Aprovisionamento do iOS </a> no Centro de Programadores da Apple, inicie sessão com o seu ID da Apple, clique em **Identificadores**, clique em **IDs de Aplicações** e, finalmente, clique no sinal **+** para registar uma nova aplicação.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Atualize os três campos seguintes para a nova aplicação e clique em **Continuar**:
   
   * **Nome**: escreva um nome descritivo para a aplicação no campo **Nome**, na secção **Descrição do ID da Aplicação**.
   * **Identificador do Pacote**: na secção **ID da Aplicação Explícito**, introduza um **Identificador do Pacote** no formato `<Organization Identifier>.<Product Name>`, como mencionado no [App Distribution Guide (Guia de Distribuição de Aplicações)](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). O *Identificador da Organização* e o *Nome do Produto* que utilizar têm de corresponder ao identificador da organização e ao nome do produto que irá utilizar quando criar o projeto XCode. Na captura de ecrã abaixo, utiliza-se *NotificationHubs* como identificador da organização e *GetStarted* como nome do produto. A utilização dos mesmos valores que irá utilizar no projeto XCode, permitir-lhe-á utilizar o perfil de publicação correto com o XCode. 
   * **Notificações Push**: marque a opção **Notificações Push** na secção **Serviços Aplicacionais**.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Esta ação gera o ID da Aplicação e pede-lhe que confirme as informações. Clique em **Registar** para confirmar o novo ID da Aplicação.
     
      Assim que clicar em **Registar**, verá o ecrã **Registo concluído**, como mostrado abaixo. Clique em **Concluído**.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. No Centro de Programadores, em IDs de Aplicações, localize o ID da aplicação que acabou de criar e clique na respetiva linha.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Se clicar no ID da aplicação, verá os detalhes dessa aplicação. Clique no botão **Editar** na parte inferior.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Desloque-se para a parte inferior do ecrã e clique no botão **Criar Certificado…** na secção **Certificado SSL do Push de Programação**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      Ao clicar no botão será apresentado o assistente “Adicionar Certificado iOS”.
   
   > [!NOTE]
   > Este tutorial utiliza um certificado de programação. É utilizado o mesmo processo ao registar um certificado de produção. Não se esqueça de verificar se está a utilizar o mesmo tipo de certificado ao enviar notificações.
   > 
   > 
3. Clique em **Escolher Ficheiro**, navegue até à localização onde guardou o ficheiro CSR que criou na primeira tarefa e, em seguida, clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Uma vez criado o certificado pelo portal, clique no botão **Transferir** e em **Concluído**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Esta ação transfere o certificado e guarda-o no seu computador na pasta Transferências.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Por predefinição, o ficheiro transferido, um certificado de desenvolvimento, tem o nome **aps_development.cer**.
   > 
   > 
5. Faça duplo clique no certificado push **aps_development.cer** transferido.
   
      Esta ação instala o novo certificado na Keychain, como mostrado abaixo:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > O nome do seu certificado poderá ser diferente, mas terá como prefixo “**Apple Development iOS Push Services:**”.
   > 
   > 
6. No Acesso Keychain, clique com o botão direito do rato no novo certificado push que criou na categoria **Certificados**. Clique em **Exportar**, dê um nome ao ficheiro, selecione o formato **.p12** e clique em **Guardar**.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Tome nota do nome do ficheiro e da localização do certificado .p12 exportado. Será utilizado para ativar a autenticação com o APNs.
   
   > [!NOTE]
   > Este tutorial mostra como criar um ficheiro QuickStart.p12. O nome do ficheiro e a localização poderão ser diferentes.
   > 
   > 

## <a name="create-a-provisioning-profile-for-the-app"></a>Criar um perfil de aprovisionamento para a aplicação
1. De volta ao <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portal de Aprovisionamento do iOS</a>, selecione **Perfis de Aprovisionamento**, selecione **Todos** e clique no botão **+** para criar um novo perfil. O Assistente **Adicionar Perfil de Aprovisionamento do iOS** será iniciado.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Selecione **Desenvolvimento de Aplicações iOS**, em **Programação**, como o tipo de perfil de aprovisionamento e clique em **Continuar**. 
3. Em seguida, selecione o ID da aplicação que acabou de criar na lista pendente **ID da Aplicação** e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. No ecrã **Selecionar certificados**, selecione o seu certificado de programação habitual utilizado para a assinatura de código e clique em **Continuar**. Este não é o certificado push que acabou de criar.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Em seguida, selecione os **Dispositivos** a utilizar para os testes e clique em **Continuar**
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Por fim, escolha um nome para o perfil em **Nome do Perfil** e clique em **Gerar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Após criar o novo perfil de aprovisionamento, clique para o transferir e instalar no seu computador de programação do Xcode. Em seguida, clique em **Guardar**.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
