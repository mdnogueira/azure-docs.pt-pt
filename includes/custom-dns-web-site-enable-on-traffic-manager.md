Depois dos registos do seu nome de domínio terem sido propagados, deverá conseguir utilizar o seu browser para confirmar que o seu nome de domínio personalizado pode ser utilizada para aceder à sua aplicação web no App Service do Azure.

> [!NOTE]
> Pode demorar algum tempo para o CNAME propagar através do sistema DNS. Pode utilizar um serviço como <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.
> 
> 

Se já não adicionou a aplicação web como um ponto final do Gestor de tráfego, deve fazer antes de resolução do nome funcione, como as rotas de nome de domínio personalizado para o Gestor de tráfego. Gestor de tráfego, em seguida, encaminha à sua aplicação web. Utilize as informações em [adicionar ou eliminar pontos finais](../articles/traffic-manager/traffic-manager-endpoints.md) para adicionar a sua aplicação web como um ponto final num perfil do Traffic Manager.

> [!NOTE]
> Se a sua aplicação web não estiver listada, ao adicionar um ponto final, certifique-se de que está configurado para **padrão** modo de plano de serviço de aplicações. Tem de utilizar **padrão** modo para a sua aplicação web para que funcionem com o Gestor de tráfego.
> 
> 

1. No seu browser, abra o [Portal do Azure](https://portal.azure.com).
2. No **Web Apps** separador, clique no nome da sua aplicação web, selecione **definições**e, em seguida, selecione **domínios personalizados**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. No **domínios personalizados** painel, clique em **adicionar hostname**.
4. Utilize o **Hostname** caixas de texto para introduzir o nome de domínio do Traffic Manager para associar esta aplicação web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Clique em **validar** para guardar a configuração de nome de domínio.
6. Após clicar em **validar** Azure irá iniciar fluxo de trabalho de verificação de domínio. Isto irá verificar para a propriedade do domínio, bem como de êxito de disponibilidade e o relatório de nome de anfitrião ou de erro detalhadas com guidence prescritiva sobre como resolver o erro.    
7. Após a validação bem-sucedida **adicionar hostname** botão deixará de Active Directory e será capaz de atribuir o nome do anfitrião. Agora, navegue para o seu nome de domínio personalizado no browser. Deverá ver a execução de aplicação utilizando o seu nome de domínio personalizado. 
   
   Depois de concluída a configuração, o nome de domínio personalizado será listado no **nomes de domínio** secção da sua aplicação web.

Nesta fase, deve ser capaz de introduzir o nome de domínio do Traffic Manager no seu browser e veja que-lo com êxito leva-o à sua aplicação web.

