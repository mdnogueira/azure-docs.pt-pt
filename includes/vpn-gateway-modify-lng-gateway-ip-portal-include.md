### <a name="gwipnoconnection"></a>Para modificar o endereço IP de gateway de rede local - nenhuma ligação de gateway

Utilize o exemplo para modificar um gateway de rede local que não tem uma ligação de gateway. Ao modificar este valor, também pode modificar os prefixos de endereços em simultâneo.

1. No recurso de Gateway de rede Local, no **definições** secção, clique em **configuração**.
2. No **endereço IP** caixa, modifique o endereço IP.
3. Clique em **guardar** para guardar as definições.

### <a name="gwipwithconnection"></a>Para modificar o endereço IP de gateway da gateway de rede local - existente a ligação de gateway

Para modificar um gateway de rede local que tenha uma ligação, terá de remover primeiro a ligação. Depois de a ligação ser removida, pode modificar o endereço IP do gateway e recriar uma nova ligação. Também pode modificar os prefixos de endereços em simultâneo. Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os endereços IP de gateway, não precisa de eliminar o gateway de VPN. Só tem de remover a ligação.
 
#### <a name="1-remove-the-connection"></a>1. Remova a ligação.

1. No recurso de Gateway de rede Local, no **definições** secção, clique em **ligações**.
2. Clique em de **...**  na linha para a ligação, em seguida, clique em **eliminar**.
3. Clique em **guardar** para guardar as definições.

#### <a name="2-modify-the-ip-address"></a>2. Modificar o endereço IP.

Também pode modificar os prefixos de endereços em simultâneo.

1. No **endereço IP** caixa, modifique o endereço IP.
2. Clique em **guardar** para guardar as definições.

#### <a name="3-recreate-the-connection"></a>3. Recrie a ligação.

1. Navegue para o Gateway de rede Virtual para a sua VNet. (Não o Gateway de rede Local.)
2. No Gateway de rede Virtual, no **definições** secção, clique em **ligações**.
3. Clique em de **+ adicionar** para abrir o **adicionar ligação** painel.
4. Recrie a ligação.
5. Clique em **OK** para criar a ligação.