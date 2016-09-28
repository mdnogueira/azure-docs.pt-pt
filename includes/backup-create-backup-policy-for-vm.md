## Definir uma política de cópia de segurança

Uma política de cópia de segurança define uma matriz de quando os instantâneos de dados são executados e quanto tempo os instantâneos são retidos. Ao definir uma política para a cópia de segurança de uma VM, pode acionar uma tarefa de cópia de segurança *uma vez por dia*. Quando cria uma nova política, esta é aplicada ao cofre. A interface da política de cópia de segurança tem o seguinte aspeto:

![Política de cópia de segurança](./media/backup-create-policy-for-vms/backup-policy.png)

Para criar uma política:

1. Introduza um nome para o **Nome da política**.

2. Os instantâneos dos seus dados podem ser efetuados num intervalo Diário ou Semanal. Utilize o menu pendente **Frequência da Cópia de segurança** para escolher se os instantâneos de dados são efetuados num intervalo Diário ou Semanal.

    - Se escolher um intervalo Diário, utilize o controlo realçado para selecionar a hora do dia para o instantâneo. Para alterar a hora, anule a seleção da hora e selecione novamente a hora.

    ![Política de cópia de segurança diária](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Se escolher um intervalo Semanal, utilize os controlos realçados para selecionar o(s) dia(s) da semana e a hora do dia para efetuar o instantâneo. No menu dos dias, selecione um ou vários dias. No menu das horas, selecione uma hora. Para alterar a hora, anule a seleção da hora e selecione a nova hora.

    ![Política de cópia de segurança semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Por predefinição, todas as opções do **Intervalo de Retenção** estão selecionadas. Desmarque os limites do intervalo de retenção que não pretende utilizar. Em seguida, especifique o intervalo(s) a utilizar.

    Os intervalos de retenção Mensal e Anual permitem-lhe especificar os instantâneos com base num incremento semanal ou diário.

    >[AZURE.NOTE] Ao proteger uma VM, é executada uma tarefa de cópia de segurança uma vez por dia. A hora quando a cópia de segurança é executada é a mesma para cada intervalo de retenção.

4. Depois de definir todas as opções para a política, na parte superior do painel, clique em **Guardar**.

    A nova política é imediatamente aplicada ao cofre.


<!--HONumber=Sep16_HO3-->


