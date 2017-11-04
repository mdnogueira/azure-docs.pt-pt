Pode verificar se a ligação teve êxito com o cmdlet 'Get-AzureVNetConnection'.

1. Utilize o seguinte exemplo de cmdlet, configurando os valores para corresponder aos seus. O nome da rede virtual tem de ser aspas se contiver espaços.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Quando o cmdlet terminar, veja os valores. No exemplo abaixo, mostra o estado de conectividade como 'Ligado' e pode ver bytes de entrada e de saída.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal