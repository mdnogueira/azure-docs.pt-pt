<!--author=alkohli last changed:01/14/2016-->


#### <a name="to-create-a-new-service"></a>Para criar um novo serviço
1. Com as credenciais da conta Microsoft, inicie sessão no Portal Clássico do Azure acedendo a este URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. No Portal Clássico do Azure, clique em **Novo** > **Serviços de Dados** > **StorSimple Manager** > **Criação Rápida**.
3. No formulário apresentado, realize as seguintes ações:
   
   1. Forneça um **Nome** exclusivo para o serviço. Este é um nome amigável que pode ser utilizado para identificar o serviço. O nome pode ter entre 2 e 50 carateres que podem ser letras, números e hífenes. O nome tem de começar e terminar com uma letra ou um número.
   2. Forneça uma **Localização** para o serviço. Em geral, escolha uma Localização mais próxima da região geográfica onde pretende implementar o dispositivo. Pode também querer ter em conta o seguinte: 
      
      * Se tiver cargas de trabalho existentes no Azure que também tenciona implementar com o dispositivo StorSimple, deve utilizar esse datacenter.
      * O serviço StorSimple Manager e o armazenamento do Azure podem estar em duas localizações diferentes. Se for esse o caso, terá de criar separadamente a conta do StorSimple Manager e a conta de armazenamento do Azure. Para criar uma conta de armazenamento do Azure, vá para o serviço Storage do Azure no Portal Clássico do Azure e siga os passos em [Criar uma Conta de armazenamento do Azure](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account). Uma vez criada esta conta, adicione-a ao StorSimple Manager seguindo os passos em [Configurar uma nova conta de armazenamento para o serviço](../articles/storsimple/storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
   3. Escolha uma **Subscrição** na lista pendente. A subscrição está ligada à sua conta de faturação. Este campo não estará presente se tiver apenas uma subscrição.
   4. Selecione **Criar uma nova conta de armazenamento** para criar automaticamente uma conta de armazenamento com o serviço. Esta conta de armazenamento terá um nome especial, como “storsimplebwv8c6dcnf”. Se precisar de ter os seus dados numa localização diferente, desmarque esta caixa. 
   5. Clique em **Criar StorSimple Manager** para criar o serviço.
   
   ![Criar StorSimple Manager](./media/storsimple-create-new-service/HCS_CreateAService-include.png)
   
   Será redirecionado para a página de destino do **Serviço**. A criação do serviço irá demorar alguns minutos. Após criar o serviço com êxito, será devidamente notificado e o estado do serviço será alterado para **Ativo**.
   
   ![Criação de serviços](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Vídeo disponível](./media/storsimple-create-new-service/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como criar um novo serviço do StorSimple Manager, clique [aqui](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).

