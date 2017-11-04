Pode criar máquinas virtuais no Azure utilizando o Explorador de servidores no Visual Studio.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>Criar uma máquina virtual do Azure no Explorador de servidores
Embora seja possível criar uma máquina virtual no [Azure Management Portal](http://go.microsoft.com/fwlink/?LinkID=253103), também pode criar uma máquina virtual no Azure através da utilização de comandos no Explorador de servidores. Máquinas virtuais pode ser utilizadas, por exemplo, para fornecer um front-end por trás de um comuns com balanceamento de carga ponto final público.

### <a name="to-create-a-new-virtual-machine"></a>Para criar uma nova máquina virtual
1. No Explorador de servidores, abra o **Azure** nó e clique em **máquinas virtuais**.
2. No menu de contexto, clique em **criar Máquina Virtual**.
   
    O **criar uma nova máquina Virtual** é apresentado o assistente.
   
    ![O comando Criar Máquina Virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. No **escolha uma subscrição** página, selecione uma subscrição para utilizar quando criar a máquina virtual e, em seguida, clique em **seguinte**.
   
    Se não tem sessão iniciada Azure, clique em **sessão** para iniciar sessão. Em seguida, selecione a subscrição do Azure na caixa de lista pendente, se ainda não estiver selecionada.
4. No **selecionar uma imagem de Máquina Virtual** página, selecione um tipo de imagem no **tipo de imagem** pendente caixa de lista e, em seguida, selecione um imagens da máquina virtual no **nome da imagem** caixa de listagem . Quando tiver terminado, clique em **seguinte**.
   
    ![Selecionar uma página de imagem de máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Pode escolher os seguintes tipos de imagem.
   
   * **Imagens públicas** apresenta uma lista de imagens da máquina virtual de sistemas operativos e o software de servidor, tais como o Windows Server e SQL Server.
   * **Imagens MSDN** apresenta uma lista de imagens da máquina virtual de software disponível para subscritores do MSDN, como o Visual Studio e o Microsoft Dynamics.
   * **Imagens de privada** listas especializada e generalizado imagens da máquina virtual que criou.
     
     Para saber mais sobre máquinas virtuais especializadas e generalizadas, consulte [imagem de VM](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Consulte [como capturar uma Máquina Virtual do Windows para utilização como um modelo](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) para obter informações sobre como ativar uma máquina virtual a um modelo que pode utilizar para rapidamente criar um novo pré-configurados máquinas virtuais.
     
     Pode clicar num nome de imagem de máquina virtual para ver informações sobre a imagem no lado direito da página.
     
     > [!NOTE]
     > Não é possível adicionar imagens da máquina virtual para o **imagens pública** ou **imagens MSDN** lista porque são só de leitura. Todas as máquinas virtuais que criar são adicionadas para o **privada imagens** lista.
     > 
     > 
     
     Se tiver um subscritor do MSDN com uma subscrição do Visual Studio-nível, pode criar uma máquina de virtual do Azure pré-criadas que contém o Visual Studio, bem como várias outras imagens. Para obter mais informações, consulte [criar uma Máquina Virtual no Visual Studio pela imagem a utilizar imagens Visual Studio 2013 Galeria para subscritores do MSDN](http://visualstudio2013msdngalleryimage.azurewebsites.net) e [subscrições MSDN](https://www.visualstudio.com/products/msdn-subscriptions-vs). |
5. No **definições básicas de Máquina Virtual** página, introduza um nome de máquina e, em seguida, adicionar as especificações para a máquina virtual, incluindo o tamanho e um nome de utilizador e palavra-passe. Quando tiver terminado, clique em **seguinte**.
   
    Irá utilizar o novo nome e a palavra-passe para iniciar sessão no computador utilizando o ambiente de trabalho remoto, pelo que é uma boa ideia anotá-los caso se esqueça. Depois de criar uma máquina virtual do Azure no Visual Studio, pode alterar o tamanho e outras definições no [Azure Management Portal](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Se escolher tamanhos maiores para a máquina virtual, podem aplicar encargos adicionais. Consulte [detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) para obter mais informações.
   > 
   > 
6. Máquinas virtuais criadas no Visual Studio requer um serviço em nuvem. No **definições do serviço de nuvem** página, selecione um serviço em nuvem da máquina virtual ou clique em **< criar novo … >** na lista pendente, se ainda não tiver uma nuvem de serviço ou pretende utilizar um novo. Também é necessária uma conta de armazenamento, por isso, escolha uma conta de armazenamento (ou crie uma nova conta de armazenamento) no **conta de armazenamento** caixa de lista pendente. Consulte [introdução ao Storage do Microsoft Azure](../articles/storage/common/storage-introduction.md) para obter mais informações.
7. Se pretender especificar uma rede virtual (o que é opcional), selecione-o nas caixas de lista pendente de rede Virtual e sub-rede.
   
    Máquinas virtuais que são membros de um conjunto de disponibilidade são implementadas em domínios diferentes de falhas. Consulte [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) para obter mais informações.
8. Se pretender que a máquina virtual pertence a um conjunto de disponibilidade (também opcional), selecione o **Especifique um conjunto de disponibilidade** caixa de verificação e, em seguida, escolha um conjunto de disponibilidade no caixa de lista pendente. Quando tiver terminado, escolha o **seguinte** botão.
   
    Adicionar a máquina virtual para um conjunto de disponibilidade ajuda a sua aplicação permanecem disponíveis durante falhas de rede, falhas de hardware de disco local e qualquer período de indisponibilidade planeado. Tem de utilizar o [Azure Management Portal](http://go.microsoft.com/fwlink/?LinkID=253103) para criar redes virtuais, sub-redes e disponibilidade conjuntos. Consulte [gerir a disponibilidade das Virtual Machines](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) para obter mais informações.
9. No **pontos finais** página, especifique os pontos finais públicos que pretende que sejam disponíveis para os utilizadores da sua máquina virtual. Por exemplo, pode optar por ativar HTTP (porta 80), para além do ambiente de trabalho remoto e os pontos finais do PowerShell, que estão ativados por predefinição. Para adicionar um ponto final, escolha um no **nome de porta** lista caixa de lista pendente e, em seguida, escolha o **adicionar** botão. Para remover um ponto final, escolha a vermelho **X** junto ao nome na lista de pontos finais.
   
    ![A página pontos finais no Assistente de máquinas virtuais.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    Os pontos finais que estão disponíveis dependem do serviço em nuvem selecionado para a máquina virtual. Consulte [pontos finais de serviço do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) para obter mais informações.
   
   > [!NOTE]
   > Ativar pontos finais públicos disponibiliza serviços na sua máquina virtual para a internet. Não se esqueça de instalar e configurar corretamente os pontos finais e os serviços na sua máquina virtual, tais como listas de controlo de acesso de definição (ACL) para os pontos finais. Consulte [como conjunto de cópias de segurança de pontos finais para uma Máquina Virtual](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) para obter mais informações.
   > 
   > 
10. Depois de terminar, configurar as definições de máquina virtual, escolha o **criar** botão para criar a máquina virtual.
    
     Como o Azure cria a máquina virtual, o **registo de atividade do Azure** mostra o progresso da operação de criação de máquina virtual.
    
     ![Registo de atividade de máquina virtual - em curso.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Para ver apenas as informações de máquina virtual, escolha o **máquinas virtuais** separador o **registo de atividade do Azure**.
    
     ![Registo de atividades do virtual machine - foi concluído.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Se a operação for concluída com êxito, a nova máquina virtual é apresentado sob o **máquinas virtuais** nó no Explorador de servidores. Pode iniciar sessão para a mesma clicando a **estabeleçam ligação através do ambiente de trabalho remoto** atalho.
    
     ![Máquina virtual no Explorador de servidores.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>Gerir as máquinas virtuais
Na página de configuração de máquina virtual, para além de ser encerrada, ligar, atualizar e adicionar pontos de verificação para a máquina virtual selecionada, também pode ver ou alterar as definições para a máquina virtual. Pode:

* Altere o tamanho de máquina virtual.
* Selecione a conjunto de disponibilidade para utilizar com a máquina virtual.
* Adicionar, remover ou alterar as definições de pontos finais públicos.
* Adicionar, remover ou configurar extensões de máquina virtual.
* Ver informações sobre os discos associados à máquina virtual.

### <a name="view-or-change-virtual-machine-settings"></a>Ver ou alterar as definições da máquina virtual
1. No Explorador de servidores, selecione a máquina virtual no **Virtual Machines do Azure** nós.
2. No menu de atalho, escolha **configurar** para ver a página de configuração da máquina virtual.
   
    ![Página de configuração da máquina virtual do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. Ver informações da máquina virtual ou alterá-la.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Guardar ou restaurar o estado da máquina virtual
Como configurar a máquina virtual e instalar software, é boa ideia regularmente guardar o seu progresso através da criação de pontos de verificação de máquina virtual. Um ponto de verificação é um instantâneo ou imagem, do estado atual da máquina virtual. Se houver algum problema com a máquina virtual ou que pretende reconfigurar a máquina virtual, pode poupar tempo ao restaurado para um estado anterior do ponto de verificação em vez de iniciar através do zero.

### <a name="to-create-a-virtual-machine-checkpoint"></a>Para criar um ponto de verificação de máquina virtual
1. No Explorador de servidores, selecione a máquina virtual no **Virtual Machines do Azure** nós.
2. No menu de atalho, escolha **configurar** para ver a página de configuração da máquina virtual.
3. Na página de configuração, escolha o **Capturar imagem** botão.
   
    ![Botão de captura da página de configuração do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    O **capturar o Virtual Machine** é apresentada a caixa de diálogo.
   
    ![Caixa de diálogo de máquina virtual de captura do Azure](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Forneça uma etiqueta da imagem e uma descrição. São fornecidos uma etiqueta predefinida e uma descrição, mas pode substitui-los com os seus próprios se assim o desejar.
5. Se já tiver executado o Sysprep na máquina virtual, selecione o **executei o Sysprep na máquina virtual** caixa.
   
    Sysprep é uma ferramenta que, entre outras coisas, remove dados específicos de sistemas a versão da máquina virtual do Windows, tornando-o modelo que outros utilizadores podem utilizar. Consulte [como capturar uma Máquina Virtual do Windows para utilização como um modelo](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) para obter mais informações. Cópia de segurança da VM antes de executar o Sysprep.
6. Depois de terminar, configurar as definições de captura, escolha o **capturar** botão para criar o ponto de verificação.
   
    Como o ponto de verificação, o Azure cria a **registo de atividade do Azure** mostra o progresso da operação.
   
    ![Captura de um ponto de verificação de máquina virtual](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Quando a operação de ponto de verificação estiver concluída, irá vê-lo no **registo de atividade do Azure**.
   
    ![Operação de ponto de verificação foi concluída](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Para gerir pontos de verificação de máquina virtual
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>Para restaurar uma máquina virtual para um estado guardado anteriormente
* Siga os passos descritos em [passo a passo: efetuar nuvem restaura Microsoft Virtual Machines do Azure com o PowerShell - parte 2](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Para eliminar um ponto de verificação
1. Vá para o [Portal de gestão do Azure](http://go.microsoft.com/fwlink/?LinkID=253103).
2. Na página de configuração da máquina virtual, escolha o **imagens** separador no topo da página.
3. Escolha o ponto de verificação que pretende eliminar e, em seguida, escolha o **eliminar** na parte inferior da página.

## <a name="shut-down-your-virtual-machine"></a>Encerre a máquina virtual
1. No Explorador de servidores, selecione a máquina virtual que pretende encerrar no **Virtual Machines do Azure** nós.
2. No menu de atalho, escolha o **encerramento** comando ou escolha **configurar** para ver a página de configuração da máquina virtual e, em seguida, escolha o **encerramento** botão.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a criação de máquinas virtuais, consulte [criar com uma Máquina Virtual a executar Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [criar uma máquina virtual com o Windows no portal de pré-visualização do Azure](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

