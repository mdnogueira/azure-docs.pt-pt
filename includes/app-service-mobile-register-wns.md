
1. No Explorador de soluções do Visual Studio, clique com o botão direito do projeto de aplicação da loja Windows e clique em **arquivo** > **associar aplicação à loja**.

    ![Associar aplicação à loja Windows](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, clique em **seguinte**e inicie sessão com a sua conta Microsoft. Escreva um nome para a sua aplicação no **reservar um novo nome de aplicação**e, em seguida, clique em **reserva**.
3. Após o registo de aplicação é criado com êxito, selecione o novo nome de aplicação, clique em **seguinte**e, em seguida, clique em **associar**. Esta ação adiciona ao manifesto da aplicação as informações de registo da Loja Windows necessárias.
4. Repita os passos 1 e 3 para o projeto de aplicação da loja Windows Phone utilizando o mesmo registo que criou anteriormente para a aplicação da loja Windows.  
5. Navegue para o [Windows Dev Center](https://dev.windows.com/en-us/overview)e inicie sessão com a sua conta Microsoft. Clique em novo registo de aplicação no **as minhas aplicações**e, em seguida, expanda **serviços** > **notificações Push**.
6. No **notificações Push** página, clique em **site dos Serviços Live** em **Push notificação serviços Windows (WNS) e as Mobile Apps do Microsoft Azure**. Tome nota dos valores do **SID do pacote** e *atual* valor **segredo da aplicação**. 

    ![Definição de aplicação no Centro de programadores](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo da aplicação e o SID do pacote são credenciais de segurança importantes. Não partilhe estes valores com ninguém e não os distribua com a aplicação.
   >
   >
