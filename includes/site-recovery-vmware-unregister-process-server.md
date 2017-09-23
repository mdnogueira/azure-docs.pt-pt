Os passos para anular o registo de um servidor de processos difere consoante o estado de ligação com o Servidor de Configuração.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Anule o registo de um servidor de processos que está num estado ligado

1. Inicie sessão no servidor de processos como Administrador.
2. Inicie o **Painel de Controlo** e abra **Programas > Desinstalar um programa**
3. Desinstale um programa pelo nome **Configuração/Servidor de Processos do Microsoft Azure Site Recovery**
4. Depois de concluído o passo 3, pode desinstalar **Configuração/Dependências do Servidor de Processos do Microsoft Azure Site Recovery**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Anule o registo de um servidor de processos que está num estado desligado

> [!WARNING]
> Os passos abaixo devem ser utilizados se não existir nenhuma forma de reativar a máquina virtual em que o Servidor de Processos estava instalado.

1. Inicie sessão no seu servidor de configuração como Administrador.
2. Abra uma linha de comandos Administrativa e procure o diretório `%ProgramData%\ASR\home\svsystems\bin`.
3. Agora, execute o comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Isto irá remover os detalhes do servidor de processos do sistema.
