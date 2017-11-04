<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Atualizar o SharePoint 2010 para o SharePoint 2013 e, em seguida, instalar o adaptador de StorSomple para SharePoint
> [!IMPORTANT]
> Todos os ficheiros que anteriormente foram movidos para armazenamento externo através de RBS não estarão disponíveis até que a atualização estiver concluída e a funcionalidade RBS seja ativada de novo. Para limitar o impacto de utilizador, execute qualquer atualização ou a reinstalação durante uma janela de manutenção planeada.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>Para atualizar o SharePoint 2010 para SharePoint 2013 e, em seguida, instalar o adaptador
1. No farm do SharePoint 2010, tenha em atenção o caminho de arquivo de BLOBS para os BLOBs externalized e as bases de dados de conteúdo para o qual RBS está ativada. 
2. Instalar e configurar o novo farm do SharePoint 2013. 
3. Mova bases de dados, aplicações e coleções de sites do farm do SharePoint 2010 para o novo farm do SharePoint 2013. Para obter instruções, aceda a [descrição geral do processo de atualização para o SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Instale o adaptador do StorSimple para o SharePoint no farm de novo. Aceda a [instalar o adaptador do StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint) para obter os procedimentos.
5. Utilizar as informações que anotou no passo 1, ativar RBS para o mesmo conjunto de bases de dados de conteúdos e fornecer o mesmo caminho de arquivo BLOB que utilizou na instalação do SharePoint 2010. Aceda a [configurar RBS](#configure-rbs) para obter os procedimentos. Depois de concluir este passo, os ficheiros anteriormente externalized devem ser acessíveis a partir do novo farm. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Atualizar o adaptador do StorSimple para SharePoint
> [!IMPORTANT]
> Deve agendar esta atualização para ocorrer durante uma janela de manutenção planeada pelos seguintes motivos:
> 
> * Anteriormente conteúdo externalized não estarão disponível até que o adaptador é reinstalado.
> * Todos os conteúdos carregados para o site depois de desinstalar a versão anterior do StorSimple adaptador para o SharePoint, mas antes de instalar a nova versão, serão armazenados na base de dados de conteúdo. Terá de mover esse conteúdo para o dispositivo StorSimple, depois de instalar o adaptador de novo. Pode utilizar o Microsoft` RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint para migrar o conteúdo. Para obter mais informações, consulte [migrar conteúdo ou a sair RBS](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>Para atualizar o adaptador do StorSimple para SharePoint
1. Desinstale a versão anterior do StorSimple adaptador para o SharePoint.
   
   > [!NOTE]
   > Este procedimento desactivará automaticamente o RBS nas bases de dados de conteúdo. No entanto, os BLOBs existentes permanecem no dispositivo StorSimple. Uma vez RBS está desativada e os BLOBs não terem sido migrados para as bases de dados de conteúdos, todos os pedidos para os BLOBs irão falhar. 
   > 
   > 
2. Instale o novo adaptador de StorSimple para SharePoint. Nova placa automaticamente reconhecerá as bases de dados de conteúdo que anteriormente foram ativadas ou desativadas para RBS e irá utilizar as definições anteriores.

