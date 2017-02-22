## <a name="overview"></a>Descrição geral
O Armazenamento de ficheiros do Azure é um serviço que oferece partilhas de ficheiros na nuvem com o [Protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão. O SMB 2.1 e o SMB 3.0 são suportados. Com o Armazenamento de ficheiros do Azure, pode migrar aplicações antigas que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais ou serviços em nuvem do Azure ou em clientes no local podem montar uma partilha de ficheiros na nuvem, tal como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Uma vez que as partilhas do Armazenamento de ficheiros são partilhas de ficheiros SMB padrão, as aplicações em execução no Azure podem aceder a dados na partilha através das APIs de E/S do sistema de ficheiros. Os programadores, por conseguinte, podem tirar partido do respetivo código existente e competências para migrar as aplicações existentes. Os profissionais de TI podem utilizar os cmdlets do PowerShell para criar, montar e gerir partilhas do Armazenamento de ficheiros como parte da administração de aplicações Azure.

Pode criar partilhas de ficheiros do Azure através do [Portal do Azure](https://portal.azure.com), dos cmdlets do PowerShell do Storage do Azure, das bibliotecas de cliente do Storage do Azure ou da API REST do Storage do Azure. Além disso, uma vez que estas partilhas de ficheiros são partilhas SMB, pode aceder às mesmas através de APIs do sistema de ficheiros padrão e familiares.



<!--HONumber=Jan17_HO3-->


