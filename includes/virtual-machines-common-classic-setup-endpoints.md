
Cada ponto final tem um *Porta pública* e um *porta privada*:

* A porta pública é utilizada pelo balanceador de carga do Azure para escutar para tráfego de entrada para a máquina virtual a partir da Internet.
* A porta privada é utilizada pela máquina virtual para escutar de tráfego de entrada, normalmente destinado para uma aplicação ou serviço em execução na máquina virtual.

Valores predefinidos para o protocolo de endereço IP e portas TCP ou UDP bem conhecida rede protocolos são fornecidos quando criar pontos finais com o portal do Azure. Para os pontos finais personalizados, terá de especificar o protocolo correto de IP (TCP ou UDP) e as portas públicas e privadas. Para distribuir o tráfego de entrada aleatoriamente em várias máquinas virtuais, terá de criar um conjunto com balanceamento de carga constituídas por vários pontos finais.

Depois de criar um ponto final, pode utilizar uma lista de controlo de acesso (ACL) para definir regras que permitem ou negam o tráfego de entrada para a porta pública do ponto final com base no respetivo endereço IP de origem. No entanto, se a máquina virtual está numa rede virtual do Azure, deve de utilizar grupos de segurança de rede em vez disso. Para obter mais informações, consulte [sobre grupos de segurança de rede](../articles/virtual-network/virtual-networks-nsg.md).

> [!NOTE]
> Configuração da firewall para máquinas virtuais do Azure é efetuada automaticamente para as portas associadas a pontos finais de conectividade remota que Azure configura automaticamente. Para as portas especificadas para todos os outros pontos finais, nenhuma configuração é efetuada automaticamente para a firewall da máquina virtual. Quando cria um ponto final da máquina virtual, terá de garantir que a firewall da máquina virtual também permite que o tráfego para o protocolo e porta privada correspondente à configuração do ponto final. Para configurar a firewall, consulte a documentação ou a ajuda online para o sistema operativo em execução na máquina virtual.
>
>

## <a name="create-an-endpoint"></a>Criar um ponto final
1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Clique em **máquinas virtuais**e, em seguida, clique no nome da máquina virtual que pretende configurar.
3. Clique em **pontos finais** no **definições** grupo. O **pontos finais** página apresenta uma lista de todos os pontos finais atuais para a máquina virtual. (Este exemplo é uma VM do Windows. Um VM do Linux por predefinição apresentará um ponto final de SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Pontos Finais](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)

4. Na barra de comando acima das entradas de ponto final, clique em **adicionar**.
5. No **adicionar ponto final** , escreva um nome para o ponto final no **nome**.
6. No **protocolo**, escolha o **TCP** ou **UDP**.
7. No **Porta pública**, escreva o número de porta para o tráfego de entrada da Internet. No **porta privada**, escreva o número de porta em que a máquina virtual está a escutar. Estes números de porta podem ser diferentes. Certifique-se de que a firewall na máquina virtual tiver sido configurada para permitir o tráfego correspondente para o protocolo (no passo 6) e a porta privada.
10. Clique em **OK**.

O novo ponto final será listado no **pontos finais** página.

![Criação do ponto final com êxito](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Gerir a ACL num ponto final
Para definir o conjunto de computadores que podem enviar tráfego, a ACL num ponto final pode restringir o tráfego com base no endereço IP de origem. Siga estes passos para adicionar, modificar ou remover uma ACL num ponto final.

> [!NOTE]
> Se o ponto final faz parte de um conjunto com balanceamento de carga, quaisquer alterações efetuadas a um ponto final a ACL são aplicadas a todos os pontos finais no conjunto.
>
>

Se a máquina virtual está numa rede virtual do Azure, recomendamos que grupos de segurança de rede em vez de ACLs. Para obter mais informações, consulte [sobre grupos de segurança de rede](../articles/virtual-network/virtual-networks-nsg.md).

1. Se ainda não o tiver feito, iniciar sessão no portal do Azure.
2. Clique em **máquinas virtuais**e, em seguida, clique no nome da máquina virtual que pretende configurar.
3. Clique em **Pontos Finais**. Na lista, selecione o ponto final adequado. A lista ACL é na parte inferior da página.

   ![Especifique os detalhes ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Utilize as linhas na lista para adicionar, eliminar, ou editar regras para uma ACL e alterar a sua ordem. O **sub-rede remota** valor é um intervalo de endereços IP para o tráfego de entrada de Internet que o Azure load balancer utiliza para permitir ou negar o tráfego com base no respetivo endereço IP de origem. Lembre-se de que especifique o intervalo de endereços IP no formato CIDR, também conhecido como formato de prefixo de endereço. Um exemplo é `10.1.0.0/8`.

 ![Nova entrada ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Pode utilizar regras para permitir o tráfego apenas de computadores específicos correspondentes aos computadores na Internet ou para negar o tráfego de intervalos de endereços específica, conhecidos.

As regras são avaliadas por ordem a partir da primeira regra e termina com a última regra. Isto significa que as regras devem ser ordenadas menos restritivo para mais restritivas. Para obter exemplos e obter mais informações, consulte [o que é uma lista de controlo de acesso de rede](../articles/virtual-network/virtual-networks-acl.md).
