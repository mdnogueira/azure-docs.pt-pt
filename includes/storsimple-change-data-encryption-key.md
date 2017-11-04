<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>Passo 1: Autorizar um dispositivo para alterar a chave de encriptação de dados de serviço no portal clássico do Azure
Normalmente, o administrador do dispositivo irá solicitar que o administrador de serviço autorizar um dispositivo para alterar as chaves de encriptação de dados do serviço. O administrador de serviço, em seguida, irá autorizar o dispositivo para alterar a chave.

Este passo é efetuado no portal clássico do Azure. O administrador de serviço pode selecionar um dispositivo de uma lista apresentada dos dispositivos que são elegíveis para autorização. O dispositivo, em seguida, está autorizado a iniciar o processo de alteração de chave de encriptação de dados do serviço.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Os dispositivos que podem estar autorizados a alterar as chaves de encriptação de dados do serviço?
Pode ser autorizado para iniciar a alterações de chave de encriptação de dados de serviço, um dispositivo tem de cumprir os seguintes critérios:

* O dispositivo tem de estar online para ser elegível para autorização de alteração de chave de encriptação de dados de serviço.
* Podem autorizar o mesmo dispositivo novamente após 30 minutos, se a alteração da chave não foi iniciada.
* Podem autorizar um dispositivo diferente, desde que a alteração da chave não foi iniciada pelo dispositivo anteriormente autorizado. Depois do novo dispositivo tenha sido autorizado, o dispositivo antigo não é possível iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto o rollover da chave de encriptação de dados do serviço está em curso.
* Podem autorizar um dispositivo quando alguns dos dispositivos registados com o serviço tem distribuída a encriptação enquanto outros utilizadores não têm. Nestes casos, os dispositivos elegíveis são aqueles que concluíram a chave de encriptação de dados do serviço alterar.

> [!NOTE]
> No portal clássico do Azure, os dispositivos virtuais StorSimple não são apresentados na lista de dispositivos que podem estar autorizado a iniciar a alteração de chave.
> 
> 

Execute os seguintes passos para selecionar e autorizar um dispositivo para iniciar a alteração chave de encriptação de dados do serviço.

#### <a name="to-authorize-a-device-to-change-the-key"></a>Para autorizar um dispositivo para alterar a chave
1. Na página do dashboard de serviço, clique em **chave de encriptação de dados de serviço de alteração**.
   
    ![Chave de encriptação do serviço de alteração](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. No **chave de encriptação de dados de serviço de alteração** diálogo caixa, selecionar e autorizar um dispositivo para iniciar a alteração chave de encriptação de dados do serviço. A lista pendente tem todos os dispositivos elegíveis que podem ser autorizados.
3. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passo 2: Utilize o Windows PowerShell para StorSimple iniciar a alteração de chave de encriptação de dados serviço
Este passo é executado no Windows PowerShell para StorSimple interface no dispositivo StorSimple autorizado.

> [!NOTE]
> Não existem operações podem ser efetuadas no portal clássico do Azure do seu serviço StorSimple Manager até que o rollover da chave esteja concluída.
> 
> 

Se estiver a utilizar a consola de série do dispositivo para ligar a interface do Windows PowerShell, execute os seguintes passos.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração de chave de encriptação de dados serviço
1. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois do cmdlet foi concluída com êxito, irá receber uma nova chave de encriptação de dados do serviço. Copie e guarde esta chave para utilização no passo 3 deste processo. Esta chave será utilizada para atualizar todos os restantes dispositivos registados com o serviço StorSimple Manager.
   
   > [!NOTE]
   > Este processo pode ser iniciado no prazo de quatro horas de autorização de um dispositivo StorSimple.
   > 
   > 
   
   Esta nova chave, em seguida, é enviada para o serviço para fazer o push para todos os dispositivos que são registados com o serviço. Um alerta, em seguida, serão apresentados no dashboard do serviço. O serviço irá desativar todas as operações em dispositivos registados, e o administrador do dispositivo, em seguida, tem de atualizar a chave de encriptação de dados do serviço nos outros dispositivos. No entanto, a e/s (anfitriões enviar dados para a nuvem) não serão interrompidos.
   
   Se tiver um único dispositivo registado ao seu serviço, o processo de rollover está agora concluído e pode ignorar o passo seguinte. Se tiver vários dispositivos registados ao seu serviço, avance para o passo 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passo 3: Atualizar a chave de encriptação de dados do serviço em outros dispositivos StorSimple
Estes passos devem ser executados na interface do Windows PowerShell do dispositivo StorSimple, se tiver vários dispositivos registados no serviço StorSimple Manager. A chave que obteve no passo 2: Utilize o Windows PowerShell para StorSimple iniciar a alteração de chave de encriptação de dados serviço tem de ser utilizado para atualizar todos os restantes dispositivo StorSimple registado com o serviço StorSimple Manager.

Execute os seguintes passos para atualizar a encriptação de dados do serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key"></a>Para atualizar a chave de encriptação de dados do serviço
1. Utilize o Windows PowerShell para StorSimple para ligar à consola do. Selecione a opção 1 para iniciar sessão com acesso total.
2. Na linha de comandos, escreva:
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de encriptação de dados de serviço que obteve no [passo 2: Utilize o Windows PowerShell para StorSimple iniciar a alteração de chave de encriptação de dados serviço](#to-initiate-the-service-data-encryption-key-change).

