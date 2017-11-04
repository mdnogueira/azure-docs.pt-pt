<!--author=v-sharos last changed: 11/06/15-->

#### <a name="to-add-a-storsimple-backup-policy"></a>Para adicionar uma política de cópias de segurança StorSimple
1. Na página **Início Rápido** do dispositivo, clique no separador **Políticas de Cópia de Segurança**. Esta ação irá direcioná-lo para a página **Políticas de Cópia de Segurança**.
2. Na parte inferior da página, clique em **Adicionar** para iniciar o assistente Adicionar Política de Cópias de Segurança.
   
    ![Adicionar uma política de cópias de segurança 1](./media/storsimple-add-backup-policy-u2/AddBackupPolicy1.png)
3. Na caixa de diálogo **Adicionar Política de Cópias de Segurança**, em **Definir a política de cópias de segurança**, faça o seguinte:
   
   1. Especifique um nome de política de cópias de segurança que contenha entre 3 e 150 carateres.
   2. Clique nas caixas de verificação para atribuir um ou mais volumes a esta política de cópias de segurança. Tenha em atenção que não é possível selecionar volumes que utilizam fornecedores de serviços cloud diferentes. Se estiver a utilizar vários fornecedores de serviços cloud, com base na sua primeira seleção, a lista mostrará os volumes pertencentes apenas a esse fornecedor de serviços cloud. Isto permitirá agrupar os volumes pertencentes a um único fornecedor de serviços cloud num instantâneo.
   3. Clique no ícone de seta ![ícone de seta](./media/storsimple-add-backup-policy-u2/HCS_ArrowIcon-include.png) para ir para a página seguinte.
      
      ![Adicionar uma política de cópias de segurança 2](./media/storsimple-add-backup-policy-u2/AddBackupPolicy2.png)
4. Em **Definir um agendamento**, faça o seguinte:
   
   1. Na caixa **Tipo de Cópia de Segurança**, selecione **Instantâneo de Cloud** ou **Instantâneo Local** na lista pendente.
   2. Indique a frequência de cópias de segurança (especifique um número e, em seguida, selecione **Dias** ou **Semanas** na lista pendente).
   3. Introduza um agendamento de retenção.
   4. Introduza a hora e data de início da política de cópias de segurança.  
   5. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-add-backup-policy-u2/HCS_CheckIcon-include.png) para guardar a política.

A política recentemente adicionada será apresentada na vista tabular na página **Políticas de Cópia de Segurança**.

