1. Inicie sessão no [Portal Clássico do Azure](http://manage.windowsazure.com).  
2. Na barra de comando na parte inferior da janela, clique em **novo**.
3. Em **computação**, clique em **Máquina Virtual**e, em seguida, clique em **da galeria**.
   
    ![Criar uma nova máquina Virtual][Image1]
4. Sob o **SUSE** grupo, selecione uma imagem de máquina virtual OpenSUSE e, em seguida, clique na seta para continuar.
5. No primeiro **configuração da Máquina Virtual** página:
   
   * Escreva um **nome da Máquina Virtual**, tais como "testlinuxvm". O nome tem de conter entre 3 e 15 carateres, pode conter apenas letras, números e hífenes e tem de começar com uma letra e terminar com uma letra ou número.
   * Certifique-se a **camada** e escolha um **tamanho**. A camada determina os tamanhos que pode escolher. O tamanho afeta o custo de utilização, bem como as opções de configuração, tais como dados quantos discos podem anexar. Para obter mais informações, consulte [tamanhos das virtual machines](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Escreva um **novo nome de utilizador**, ou aceite a predefinição **azureuser**. Este nome é adicionado ao ficheiro de lista Sudoers.
   * Decida qual o tipo de **autenticação** a utilizar. Para Diretrizes gerais da palavra-passe, consulte [palavras-passe seguras](http://msdn.microsoft.com/library/ms161962.aspx).
6. No próximo **configuração da Máquina Virtual** página:
   
   * Utilizar a predefinição **criar um novo serviço de nuvem**.
   * No **nome DNS** caixa, escreva um nome DNS exclusivo para utilizar como parte do endereço, tais como "testlinuxvm".
   * No **afinidade de região/grupo/Virtual Network** caixa, selecione uma região onde será alojada esta imagem de virtual.
   * Em **pontos finais**, mantenha o ponto final SSH. Pode adicionar outros agora, ou adicionar, alterar ou eliminá-los depois da máquina virtual é criada.
     
     > [!NOTE]
     > Se pretender que uma máquina virtual para utilizar uma rede virtual, **tem** especificar a rede virtual ao criar a máquina virtual. Não é possível adicionar uma máquina virtual a uma rede virtual depois de criar a máquina virtual. Para obter mais informações, consulte [descrição geral de rede Virtual](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. No último **configuração da Máquina Virtual** página, manter as predefinições e, em seguida, clique na marca de verificação para concluir.

O portal apresenta a nova máquina virtual em **máquinas virtuais**. Enquanto o estado é comunicado como **(aprovisionamento)**, a máquina virtual está a ser configurada. Quando o estado é comunicado como **executar**, pode passar para o passo seguinte.

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina Virtual
Vai utilizar o SSH ou PuTTY para ligar à máquina virtual, dependendo do sistema operativo no computador que irá ligar a partir de:

* A partir de um computador com Linux, utilize SSH. Na linha de comandos, escreva:
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Escreva a palavra-passe do utilizador.
* A partir de um computador com o Windows, utilize o PuTTY. Se não o tiver instalado, transfira-à partir de [a página de transferências PuTTY][PuTTYDownload].
  
    Guardar **putty.exe** para um diretório no seu computador. Abra uma linha de comandos, navegue para essa pasta e executar **putty.exe**.
  
    Escreva o nome de anfitrião, tais como "testlinuxvm.cloudapp.net" e escreva "22" para o **porta**.
  
    ![Ecrã puTTY][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Atualizar a Máquina Virtual (opcional)
1. Depois de se estiver ligado à máquina virtual, opcionalmente, pode instalar o sistema atualizações e correções de erros. Para executar a atualização, escreva:
   
    `$ sudo zypper update`
2. Selecione **Software**, em seguida, **actualização Online** para listar as atualizações disponíveis. Selecione **aceitar** para iniciar a instalação e aplicar todas as correções disponíveis novo (exceto as opcional).
3. Após a instalação for efetuada, selecione **concluir**.  O sistema está atualizado.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
