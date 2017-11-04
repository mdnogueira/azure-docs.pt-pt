

Quando cria um projeto de aplicação web do Azure, pode aprovisionar uma máquina virtual no Azure. Em seguida, pode configurar a máquina virtual com o software adicional, ou utilizar a máquina virtual para fins de depuração ou diagnóstico.

Para criar uma máquina virtual quando criar uma aplicação web, siga estes passos:

1. No Visual Studio, clique em **ficheiro** > **novo** > **projeto** > **Web**e, em seguida, escolha **Aplicação Web ASP.NET** (sob a **Visual c#** ou **Visual Basic** nós).
2. No **novo projeto ASP.NET** , selecione o tipo de aplicação web que pretende e, na secção da caixa de diálogo (no canto inferior direito) do Azure, certifique-se de que o **anfitrião na nuvem** é a caixa de verificação selecionado (assinalada como esta caixa de verificação **criar recursos remotos** em algumas instalações).
   
    ![][0]
3. Para este exemplo, na lista pendente no Microsoft Azure, escolha **Máquina Virtual (v1)**e, em seguida, clique em de **OK** botão.
4. Se lhe for pedida, inicie sessão no Azure. O **criar Máquina Virtual** é apresentada a caixa de diálogo.
   
    ![][2]
5. No **nome DNS** caixa, introduza um nome para a máquina virtual. O nome DNS tem de ser exclusivo no Azure. Se o nome que introduziu não estiver disponível, aparece um ponto de exclamação vermelho.
6. No **imagem** lista, selecione a imagem que pretende base a máquina virtual no. Pode escolher qualquer uma das imagens de padrão máquina virtual do Azure ou a imagem que carregar para o Azure.
7. Deixe o **ativar o IIS e o Web Deploy** caixa de verificação selecionada, a menos que planeia instalar um servidor web diferente. Não será capaz de publicar a partir do Visual Studio se desativar o Web Deploy. Pode adicionar o IIS e o Web Deploy para qualquer um das imagens empacotadas do Windows Server, incluindo as suas próprias imagens personalizadas.
8. No **tamanho** lista, escolha o tamanho da máquina virtual.
9. Especifique as credenciais de início de sessão para esta máquina virtual. Tome nota dos mesmos, porque terá-os para aceder à máquina através do ambiente de trabalho remoto.
10. No **localização** lista, escolha a região para alojar a máquina virtual.
11. Clique em de **OK** botão para iniciar a criação da máquina virtual. Pode acompanhar o progresso da operação no **saída** janela.
    
    ![][3]
12. Quando a máquina virtual é aprovisionada, scripts publicados são criadas num **PublishScripts** nó na sua solução. O script publicado é executado e aprovisiona uma máquina virtual no Azure. O **saída** janela mostra o estado. O script executa as seguintes ações para configurar a máquina virtual:
    
    * Cria a máquina virtual, se ainda não existir.
    * Cria uma conta de armazenamento com o nome que comece com `devtest`, mas apenas se não existe já uma conta de armazenamento na região especificada.
    * Cria um serviço em nuvem como um contentor para a máquina virtual e cria uma função da web para a aplicação web.
    * Configura o Web Deploy na máquina virtual.
    * Configura o IIS e ASP.NET na máquina virtual.
    
    ![][4]
13. (Opcional) Pode ligar para a nova máquina virtual. No **Explorador de servidores**, expanda o **máquinas virtuais** nós, escolha o nó para a máquina virtual que criou e, no respetivo menu de atalho, escolha **ligar com o ambiente de trabalho remoto**. Em alternativa, na **Cloud Explorer** pode escolher **aberto no Portal** no menu de atalho e ligar à máquina virtual não existe.
    
    ![][5]

## <a name="next-steps"></a>Passos seguintes
Se pretender personalizar os scripts publicados que criou, leia mais informações aprofundadas em [utilizando Scripts do Windows PowerShell para publicar a ambientes de teste e desenvolvimento](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
