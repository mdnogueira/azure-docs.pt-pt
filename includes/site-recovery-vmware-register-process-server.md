1. Ligue à máquina virtual do servidor de processo através da Ligação de Ambiente de Trabalho Remoto.
2. Assim que o início de sessão estiver concluído, verá que a ferramenta de Configuração do servidor de processo é iniciada automaticamente e necessita que introduza o seguinte
  * Nome completamente qualificado do Servidor de Configuração (FQDN) ou Endereço IP
  * Porta em que o servidor de Configuração está a ouvir. O valor deve ser 443
  * Frase de Acesso de ligação para ligar ao servidor de configuração.
  * Porta de Transferência de Dados a ser configurada para este servidor de processos. Deixe o valor predefinido conforme está, a menos que tenha alterado para um número de porta diferente no seu ambiente.

    ![Registar servidor de processos](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Clique no botão guardar para guardar a configuração.
4. Assim que o registo estiver concluído, o servidor de processos começa a aparecer no seu servidor de Configuração e pode ser utilizado para reativação pós-falha.

> [!TIP]
> O utilitário de configuração do servidor de processo pode ser iniciado ao clicar duas vezes no atalho **cspsconfigtool**, disponível no ambiente de trabalho da máquina virtual.


<!--HONumber=Feb17_HO1-->


