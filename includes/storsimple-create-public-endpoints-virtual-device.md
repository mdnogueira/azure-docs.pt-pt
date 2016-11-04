#### Para criar pontos finais públicos no dispositivo virtual
1. Inicie sessão no Portal Clássico do Azure.

* Clique em **Virtual Machines** e selecione a máquina virtual que está a ser utilizada como o seu dispositivo virtual.
* Clique em **Pontos Finais**. A página **Pontos Finais** apresenta uma lista de todos os pontos finais da máquina virtual.
* Clique em **Adicionar**. É apresentada a caixa de diálogo **Adicionar Ponto final**. Clique na seta para continuar.
* Em **Nome**, escreva o seguinte nome para o ponto final: **WinRMHttps**.
* Em **Protocolo**, especifique **TCP**.
* Em **Porta Pública**, escreva os números de porta que pretende utilizar para a ligação.
* Em **Porta Privada**, escreva **5986**.
* Clique na marca de verificação para criar o ponto final.

Uma vez criado o ponto final, pode ver os respetivos detalhes para determinar o endereço IP Virtual Público (VIP). Registe esse endereço.

<!--HONumber=Sep16_HO3-->


