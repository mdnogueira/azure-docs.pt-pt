#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Configurar o projeto do iOS no Xamarin Studio
1. No Xamarin.Studio, abra **Info. plist**e atualize o **identificador de pacote** com o ID do pacote que criou anteriormente com o ID de aplicação nova.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Desloque para baixo até **modos em segundo plano**. Selecione o **ativar modos de em segundo plano** caixa e **notificações remotas** caixa.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Faça duplo clique em projeto no painel de solução para abrir **opções do projeto**.
4. Em **criar**, escolha **iOS assinatura do pacote**e selecione a identidade correspondente e aprovisionamento de conjunto de perfis apenas cópias de segurança para este projeto.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Isto garante que o projeto utiliza o novo perfil de assinatura de código. Para o dispositivo de Xamarin oficial documentação de aprovisionamento, consulte [aprovisionamento de dispositivos de Xamarin].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Configurar o projeto do iOS no Visual Studio
1. No Visual Studio, clique com o botão direito no projeto e, em seguida, clique em **propriedades**.
2. Nas páginas de propriedades, clique em de **iOS aplicação** separador e atualizar o **identificador** com o ID que criou anteriormente.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. No **iOS assinatura do pacote** separador, selecione a identidade correspondente perfil de aprovisionamento e que acabou de configurar para este projeto.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Isto garante que o projeto utiliza o novo perfil de assinatura de código. Para o dispositivo de Xamarin oficial documentação de aprovisionamento, consulte [aprovisionamento de dispositivos de Xamarin].
4. Faça duplo clique em ficheiro info. plist para abri-lo e, em seguida, ative **RemoteNotifications** em **modos em segundo plano**.

[aprovisionamento de dispositivos de Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
