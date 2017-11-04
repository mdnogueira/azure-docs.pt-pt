Siga estes passos para instalar e executar MongoDB numa máquina virtual com o Windows Server.

> [!IMPORTANT]
> Funcionalidades de segurança do MongoDB, como a autenticação e o enlace de endereço IP, não estão ativadas por predefinição. Funcionalidades de segurança devem ser ativadas antes de implementar o MongoDB para um ambiente de produção.  Para obter mais informações, consulte [segurança e autenticação](http://www.mongodb.org/display/DOCS/Security+and+Authentication).
>
>

1. Depois de se ligar à máquina virtual utilizando o ambiente de trabalho remoto, abra o Internet Explorer a partir de **iniciar** menu na máquina virtual.
2. Selecione o **ferramentas** botão no canto superior direito.  No **opções da Internet**, selecione o **segurança** separador e, em seguida, selecione o **Sites fidedignos** ícone e, finalmente, clique o **Sites** botão. Adicionar *https://\*. mongodb.org* à lista de sites fidedignos.
3. Aceda a [transfere - MongoDB](https://www.mongodb.com/download-center#community).
4. Localizar o **atual versão estável** de **Comunidade servidor**, selecione a versão mais recente **64-bit** versão na coluna Windows. Transferir e, em seguida, execute o instalador do MSI.
5. MongoDB é normalmente instalado na c:\Programas\Microsoft Files\MongoDB. Procure as variáveis de ambiente no ambiente de trabalho e adicione o caminho de binários do MongoDB à variável de caminho. Por exemplo, poderá determinar os binários em c:\Programas\Microsoft Files\MongoDB\Server\3.4\bin no seu computador.
6. Criar os diretórios de dados e de registo do MongoDB no disco de dados (por exemplo, unidade **f:**) que criou nos passos anteriores. De **iniciar**, selecione **linha de comandos** para abrir uma janela de linha de comandos.  Escreva:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. Para executar a base de dados, execute:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Todas as mensagens de registo são direcionadas para o *F:\MongoLogs\mongolog.log* de ficheiros mongod.exe servidor é iniciado e preallocates ficheiros do diário. Pode demorar alguns minutos para o MongoDB prealocar os ficheiros do diário de alterações e começar a escutar ligações. A linha de comandos permanece direcionada para esta tarefa enquanto a instância do MongoDB está em execução.
8. Para iniciar a shell de administrativa do MongoDB, abrir outra janela de comando de **iniciar** e escreva os seguintes comandos:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    A base de dados é criado pela inserção.
9. Em alternativa, pode instalar mongod.exe como um serviço:

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    Um serviço é instalado com o nome MongoDB com uma descrição do "BD de Mongo". O `--logpath` opção tem de ser utilizada para especificar um ficheiro de registo, uma vez que o serviço em execução não tem uma janela de comando para apresentar os resultados.  O `--logappend` opção especifica que um reinício do serviço de faz com que a saída para acrescentar ao ficheiro de registo existente.  O `--dbpath` opção especifica a localização do diretório de dados. Para mais relacionados com o serviço opções da linha de comandos, consulte [relacionados com o serviço opções da linha de comandos][MongoWindowsSvcOptions].

    Para iniciar o serviço, execute este comando:

        C:\> net start MongoDB
10. Agora que MongoDB está instalado e em execução, terá de abrir uma porta na Firewall do Windows, pelo que pode ligar remotamente ao MongoDB.  Do **iniciar** menu, selecione **ferramentas administrativas** e, em seguida, **Firewall do Windows com segurança avançada**.
11. a) no painel esquerdo, selecione **regras de entrada**.  No **ações** painel da direita, selecione **nova regra...** .

    ![Firewall do Windows][Image1]

    b) no **novo Assistente de regras de entrada**, selecione **porta** e, em seguida, clique em **seguinte**.

    ![Firewall do Windows][Image2]

    c) selecione **TCP** e, em seguida, **portas locais específicas**.  Especifique uma porta de "27017" (a porta predefinida MongoDB escuta) e clique em **seguinte**.

    ![Firewall do Windows][Image3]

    d) selecione **permitir a ligação** e clique em **seguinte**.

    ![Firewall do Windows][Image4]

    i) clique em **seguinte** novamente.

    ![Firewall do Windows][Image5]

    f) Especifique um nome para a regra, tal como "MongoPort" e clique em **concluir**.

    ![Firewall do Windows][Image6]

12. Se não configurar um ponto final para o MongoDB quando criou a máquina virtual, pode fazê-lo agora. Terá da regra de firewall e o ponto final para conseguir ligar remotamente para MongoDB.

  No portal do Azure, clique em **máquinas virtuais (clássicas)**, clique no nome da nova máquina virtual e, em seguida, clique em **pontos finais**.

    ![Pontos Finais][Image7]

13. Clique em **Adicionar**.

14. Adicionar um ponto final com o nome "Mongo" protocolo **TCP**e ambos **pública** e **privada** portas configuradas para "27017". Abrir esta porta permite o MongoDB ser acedido remotamente.

    ![Pontos Finais][Image9]

> [!NOTE]
> A porta 27017 é a porta predefinida utilizada por MongoDB. Pode alterar esta porta predefinida, especificando o `--port` parâmetro ao iniciar o servidor de mongod.exe. Certifique-se conceder o mesmo número de porta na firewall e o ponto final "Mongo" nas instruções anteriores.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
