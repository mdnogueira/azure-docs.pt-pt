<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>Para efetuar uma cópia de segurança
1. Na página **Início Rápido** do dispositivo, clique em **Adicionar uma política de cópia de segurança**. Esta ação irá iniciar o assistente Adicionar Política de Cópia de Segurança. 
2. Na página **Definir a política de cópia de segurança**:
   
   1. Forneça um nome que contenha entre 3 e 150 carateres para a sua política de cópia de segurança.
   2. Selecione os volumes cuja cópia de segurança efetuar. Se selecionar mais do que um volume, esses volumes serão agrupados para criar uma cópia de segurança consistente de falhas.
   3. Clique no ícone de seta ![ícone de seta](./media/storsimple-take-backup/HCS_ArrowIcon-include.png). 
      
      ![Adicionar-política-cópia-de-segurança](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. Na página **Definir um agendamento**:
   
   1. Selecione o tipo de cópia de segurança na lista pendente. Para restauros mais rápidos, selecione **Instantâneo Local**. Para resiliência de dados, selecione **Instantâneo Nuvem**.
   2. Especifique a frequência da cópia de segurança em minutos, horas, dias ou semanas.
   3. Selecione um tempo de retenção. As opções de período de retenção dependem da frequência da cópia de segurança. Por exemplo, para uma política diária, o período de retenção pode ser especificado em semanas, ao passo que o período de retenção para uma política mensal é em meses.
   4. Selecione a hora e data de início para a política de cópia de segurança.
   5. Selecione a caixa de verificação **Ativar** para ativar a política de cópia de segurança. 
   6. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-take-backup/HCS_CheckIcon-include.png) para guardar a política.
      
      ![Adicionar-política-cópia-de-segurança](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      Tem agora uma política de cópia de segurança que criará as cópias de segurança agendadas dos dados do seu volume.

Concluiu a configuração do dispositivo. 

![Vídeo disponível](./media/storsimple-take-backup/Video_icon.png) **Vídeo disponível**

Para ver um vídeo que demonstra como fazer uma cópia de segurança do StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/).

