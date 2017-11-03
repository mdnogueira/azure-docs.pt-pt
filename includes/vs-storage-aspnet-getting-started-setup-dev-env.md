## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Esta secção explica-lhe como configurar o ambiente de desenvolvimento, incluindo a criação de uma aplicação ASP.NET MVC, adicionar uma ligação a serviços ligados, adicionar um controlador e especificar as diretivas de espaço de nomes necessários.

### <a name="create-an-aspnet-mvc-app-project"></a>Criar um projeto de aplicação de ASP.NET MVC

1. Abra o Visual Studio.

1. Selecione **ficheiro -> novo -> projeto** no menu principal

1. No **novo projeto** caixa de diálogo, especifique as opções como realçados na figura seguinte:

    ![Criar projeto ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Selecione **OK**.

1. No **novo projeto ASP.NET** caixa de diálogo, especifique as opções como realçados na figura seguinte:

    ![Especifique o MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Selecione **OK**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utilizar os serviços ligados para ligar a uma conta de armazenamento do Azure

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **adicionar -> serviço ligado**.

1. No **adicionar o serviço ligado** caixa de diálogo, selecione **Storage do Azure**e, em seguida, selecione **configurar**.

    ![Caixa de diálogo de serviço ligada](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. No **Storage do Azure** caixa de diálogo, selecione a conta de armazenamento do Azure pretendido com a qual pretende trabalhar e selecione **adicionar**.
