Neste passo, testar o serviço de escuta do grupo de disponibilidade, utilizando uma aplicação cliente que está a ser executado na mesma rede.

Conectividade de clientes tem os seguintes requisitos:

* Ligações de cliente para o serviço de escuta tem provenientes de máquinas que residem num serviço cloud diferente daquela que aloja as réplicas de disponibilidade Always On.
* Se as réplicas Always On estiverem em sub-redes diferentes, os clientes têm de especificar *MultisubnetFailover = True* na cadeia de ligação. Esta condição resulta em tentativas de ligação de paralelo para réplicas nas várias sub-redes. Este cenário inclui a uma implementação de por várias regiões Always On disponibilidade grupo.

Um exemplo é para ligar ao serviço de escuta de uma das VMs na mesma rede virtual do Azure (mas não um que aloja uma réplica). Uma forma fácil para concluir este teste está a tentar estabelecer a ligação SQL Server Management Studio para o serviço de escuta do grupo de disponibilidade. Outro método simple está a ser executado [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), da seguinte forma:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Se o valor de EndpointPort for *1433*, não é necessário especificá-la na chamada. A chamada anterior também assume que o computador de cliente está associado ao mesmo domínio e se o autor da chamada foram concedida as permissões na base de dados utilizando a autenticação do Windows.
> 
> 

Quando testar o serviço de escuta, lembre-se de que a ativação pós-falha o grupo de disponibilidade para se certificar de que os clientes podem ligar ao serviço de escuta em ativações pós-falha.

