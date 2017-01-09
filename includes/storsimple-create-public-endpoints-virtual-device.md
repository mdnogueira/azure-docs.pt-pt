#### <a name="to-create-public-endpoints-on-the-virtual-device"></a>Para criar pontos finais públicos no dispositivo virtual

1. Inicie sessão no Portal Clássico do Azure.
2. Clique em **Virtual Machines** e selecione a máquina virtual que está a ser utilizada como o seu dispositivo virtual.
3. Clique em **Pontos Finais**. A página **Pontos Finais** apresenta uma lista de todos os pontos finais da máquina virtual.
4. Clique em **Adicionar**. É apresentada a caixa de diálogo **Adicionar Ponto final**. Clique na seta para continuar.
5. Em **Nome**, escreva o seguinte nome para o ponto final: **WinRMHttps**.
6. Em **Protocolo**, especifique **TCP**.
7. Em **Porta Pública**, escreva os números de porta que pretende utilizar para a ligação.
8. Em **Porta Privada**, escreva **5986**.
9. Clique na marca de verificação para criar o ponto final.

Uma vez criado o ponto final, pode ver os respetivos detalhes para determinar o endereço IP Virtual Público (VIP). Registe esse endereço.



<!--HONumber=Jan17_HO1-->


