<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Para instalar a atualização 1.2 do portal clássico do Azure
1. No portal clássico do Azure, visite o **dispositivos** página e selecione o seu dispositivo.
2. Navegue para **dispositivos** > **configurar**.
3. Em **Interfaces de rede**, primeiro certifique-se de que tem, pelo menos, uma interface de rede que está preparada para iSCSI. Em seguida, localize a interface de rede (que não seja dados 0) que tem um gateway atribuído.
4. Desativar a interface de rede que tenha um gateway atribuído e guardar a configuração modificada. Tenha em atenção as definições de interface de rede são mantidas e, por isso, quando voltar a ativar esta interface de rede mais tarde, o portal irá reverter para as definições originais.
5. Agora, pode [utilizar o portal clássico do Azure para instalar a atualização 1.2](#install-update-12-via-the-azure-classic-portal). Siga as instruções a partir do passo 3 deste procedimento. Depois de instalar todas as atualizações, pode reativar a interface de rede que tiver desativado.

