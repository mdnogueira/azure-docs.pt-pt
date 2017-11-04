<!--author=SharS last changed: 9/17/15-->

Neste procedimento, irá:

1. [Preparar para executar o executável responsável pela manutenção](#to-prepare-to-run-the-maintainer) .
2. [Preparar a base de dados de conteúdo e a Reciclagem para eliminação imediata de BLOBs órfãos](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Executar Maintainer.exe](#to-run-the-maintainer).
4. [Reverter o conteúdo da base de dados e definições de reciclagem](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Para se preparar para executar o responsável pela manutenção
1. No servidor Web front-end, abra a Shell de gestão do SharePoint 2013 como administrador.
2. Navegue para a pasta *unidade de arranque*: \Programas\Microsoft SQL remoto Blob Storage 10.50\Maintainer\.
3. Mudar o nome **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** para **Web. config**.
4. Utilize `aspnet_regiis -pdf connectionStrings` para desencriptar o ficheiro Web. config.
5. No ficheiro Web. config desencriptados, sob o `connectionStrings` nó, adicione a cadeia de ligação para a instância do SQL server e o nome de base de dados de conteúdo. Veja o seguinte exemplo.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Utilize `aspnet_regiis –pef connectionStrings` encriptar novamente o ficheiro Web. config. 
7. Mudar o nome do Web. config para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Para preparar o conteúdo da base de dados e a Reciclagem para eliminar imediatamente órfão BLOBs
1. No SQL Server, no SQL Server Management Studio, execute as seguintes consultas de atualização da base de dados de conteúdo de destino: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. No servidor web front-end, em **Administração Central**, edite o **definições gerais de aplicação Web** pretendido conteúdo da base de dados desativar temporariamente a Reciclagem. Esta ação também esvaziará a Reciclagem para quaisquer relacionadas com coleções de sites. Para tal, clique em **Administração Central** -> **gestão de aplicações** -> **aplicações Web (gerir web Apps)**  ->  **SharePoint - 80** -> **definições de aplicações gerais**. Definir o **reciclar Bin estado** para **OFF**.
   
    ![Definições gerais de aplicação Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Para executar o responsável pela manutenção
* No servidor web front-end, na Shell de gestão do SharePoint 2013, execute o responsável pela manutenção da seguinte forma:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Apenas o `GarbageCollection` operação é suportada para StorSimple neste momento. Note também que os parâmetros emitidos para Microsoft.Data.SqlRemoteBlobs.Maintainer.exe são sensíveis a maiúsculas e minúsculas. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Para reverter o conteúdo da base de dados e definições de reciclagem
1. No SQL Server, no SQL Server Management Studio, execute as seguintes consultas de atualização da base de dados de conteúdo de destino:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. No servidor web front-end, no **Administração Central**, edite o **definições gerais de aplicação Web** pretendido conteúdo da base de dados voltar a ativar a Reciclagem. Para tal, clique em **Administração Central** -> **gestão de aplicações** -> **aplicações Web (gerir web Apps)**  ->  **SharePoint - 80** -> **definições de aplicações gerais**. Definir o estado de reciclagem de reciclagem **ON**.

