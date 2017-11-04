<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> Quando efetuar alterações ao adaptador do StorSimple para a configuração do SharePoint RBS, tem de ser sessão iniciada com uma conta de utilizador que pertença ao grupo Admins do domínio. Além disso, tem de aceder a página de configuração a partir de um browser em execução no mesmo anfitrião como Administração Central.
> 
> 

#### <a name="to-configure-rbs"></a>Para configurar RBS
1. Abra a página de Administração Central do SharePoint e navegue até à **definições do sistema**. 
2. No **Azure StorSimple** secção, clique em **configurar adaptador do StorSimple**.
   
    ![Configure o adaptador do StorSimple](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. No **configurar adaptador do StorSimple** página:
   
   1. Certifique-se de que o **ativar caminho edição** caixa de verificação está selecionada.
   2. Na caixa de texto, escreva o caminho de convenção de Nomenclatura Universal (UNC) do arquivo de BLOB.
      
      > [!NOTE]
      > O volume de arquivo BLOB tem de estar alojado num volume iSCSI configurado no dispositivo StorSimple.

   3. Clique em de **ativar** no botão abaixo cada uma das bases de dados conteúdos que pretende configurar para armazenamento remoto.
      
      > [!NOTE]
      > O arquivo de BLOB tem de ser partilhados e pode ser acedida por todos os servidores (WFE) front-end web e a conta de utilizador que está configurada para o farm de servidores do SharePoint tem de ter acesso à partilha.
      
      ![Ativar o fornecedor RBS](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Quando ativar ou desativar RBS, também verá a mensagem seguinte.
      
      ![Configurar StorSimple adaptador Ativar desativar](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Clique em de **atualização** botão para aplicar a configuração. Ao clicar no **atualização** botão, o estado de configuração RBS será atualizado em todos os servidores WFE, e todo o farm irá ser RBS-ativado. É apresentada a mensagem seguinte.
      
      ![Mensagem de configuração do adaptador](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Se estiver a configurar RBS para um farm do SharePoint com um número muito elevado de bases de dados (superior a 200), a página web de Administração Central do SharePoint poderá tempo limite. Se isso ocorrer, atualize a página. Isto não afeta o processo de configuração.

4. Certifique-se a configuração:
   
   1. Inicie sessão para o site de Administração Central do SharePoint e navegue para o **configurar adaptador do StorSimple** página.
   2. Verifique os detalhes de configuração para se certificar de que correspondem às definições que introduziu. 
5. Certifique-se de que RBS funciona corretamente:
   
   1. Carregar um documento para o SharePoint. 
   2. Aceda ao caminho UNC que configurou. Certifique-se de que a estrutura de diretórios RBS foi criada e que contém o objeto foi carregado.
6. (Opcional) Pode utilizar o Microsoft RBS `Migrate()` cmdlet do PowerShell incluído com o SharePoint para migrar conteúdo BLOB existente para o dispositivo StorSimple. Para obter mais informações, consulte [migrar conteúdo ou a sair RBS no SharePoint 2013] [ 6] ou [migrar conteúdo ou a sair RBS (SharePoint Foundation 2010)] [7].
7. (Opcional) Nas instalações de teste, pode verificar que os BLOBs foram movidos fora da base de dados de conteúdo da seguinte forma: 
   
   1. Inicie o SQL Server Management Studio.
   2. Execute a consulta ListBlobsInDB_2010.sql ou ListBlobsInDB_2013.sql, da seguinte forma.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Se RBS foi configurada corretamente, deverá aparecer um valor nulo na coluna SizeOfContentInDB para qualquer objeto que foi carregada e externalized com êxito com RBS.
8. (Opcional) Depois de configurar RBS e mover todo o conteúdo BLOB para o dispositivo StorSimple, pode mover a base de dados de conteúdo para o dispositivo. Se optar por mover a base de dados de conteúdos, recomendamos que configure o armazenamento de conteúdo da base de dados no dispositivo como um volume primário. Em seguida, utilize estabelecida recomendados do SQL Server para migrar a base de dados de conteúdo para o dispositivo StorSimple. 
   
   > [!NOTE]
   > Mover a base de dados de conteúdo para o dispositivo só é suportada para a série 8000 do StorSimple (que não é suportado para a série de 5000 ou 7000).
   
   Se armazenar os BLOBs e a base de dados de conteúdo em volumes separados no dispositivo StorSimple, recomendamos que configurar no mesmo contentor de volume. Isto garante que estes irão ser copiados em segurança em conjunto.
   
   > [!WARNING]
   > Se não tiver ativado RBS, não recomendamos a mover a base de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração untested.
   
9. Vá para o próximo passo: [configurar recolha de lixo](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
