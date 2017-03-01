1. Ligue à máquina virtual do Servidor de Processos através da Ligação de Ambiente de Trabalho Remoto.
2. Pode iniciar o cspsconfigtool.exe, ao clicar no atalho disponível no ambiente de trabalho. (A ferramenta será iniciada de forma automática se esta for a primeira vez que inicia sessão no servidor de processos).
  * Nome completamente qualificado do Servidor de Configuração (FQDN) ou Endereço IP
  * Porta em que o servidor de Configuração está a ouvir. O valor deve ser 443
  * Frase de Acesso de ligação para ligar ao servidor de configuração.
  * Porta de Transferência de Dados a ser configurada para este Servidor de Processos. Deixe o valor predefinido conforme está, a menos que tenha alterado para um número de porta diferente no seu ambiente.

    ![Registar Servidor de Processos](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Clique no botão guardar para guardar a configuração.
4. Assim que o registo estiver concluído, o Servidor de Processos começa a aparecer no seu servidor de Configuração e pode ser utilizado para reativação pós-falha.

> [!TIP]
> O utilitário de configuração do Servidor de Processos pode ser iniciado ao clicar duas vezes no atalho **cspsconfigtool**, disponível no ambiente de trabalho da máquina virtual.


<!--HONumber=Feb17_HO4-->


