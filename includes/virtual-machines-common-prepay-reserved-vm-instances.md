# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Prepay para máquinas virtuais com instâncias de VM reservado

Prepay para máquinas virtuais e a poupar dinheiro com instâncias de Máquina Virtual reservada. Para obter mais informações, consulte [oferta de instâncias de Máquina Virtual reservada](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Pode comprar instâncias de Máquina Virtual reservada [portal do Azure](https://portal.azure.com). Para comprar uma instância de Máquina Virtual reservado:
-   Tem de ser uma função de proprietário para, pelo menos, uma empresa ou de subscrição pay as you go.
-   Para as subscrições empresariais, tem de estar ativadas compras de reserva no [EA portal](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Comprar uma instância de Máquina Virtual reservada
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **mais serviços** > **reservas**.
3. Selecione **adicionar** para comprar uma nova reserva.
4. Preencha os campos obrigatórios. VM em execução instâncias que correspondam os atributos que selecionar elegíveis para obter o desconto de reserva. O número real das instâncias de VM que obtenha o desconto depende no âmbito e na quantidade selecionado.

    | Campo      | Descrição|
    |:------------|:--------------|
    |Nome        |O nome desta reserva.| 
    |Subscrição|A subscrição utilizada para pagar a reserva. O método de pagamento de subscrição é-lhe cobrado os custos de compromisso de reserva. O tipo de subscrição tem de ser um enterprise agreement (oferecem número: MS-AZR - 0017P) ou pay as you go (oferecem número: MS-AZR - 0003P). Para uma subscrição do enterprise, os encargos são deducted do saldo de compromisso monetário a inscrição ou cobrados como excedido. Para a subscrição pay as you go, os encargos são cobrados para o método de pagamento de cartão de crédito ou fatura na subscrição.|    
    |Âmbito       |Âmbito da reserva pode abranger uma subscrição ou várias subscrições (âmbito partilhada). Se selecionar: <ul><li>Única subscrição - o desconto de reserva é aplicada às VMs nesta subscrição. </li><li>Partilhado - o desconto de reserva é aplicado às VMs em execução em quaisquer subscrições dentro do contexto de faturação. Para os clientes empresariais, o âmbito partilhado é a inscrição e inclui todas as subscrições (exceto as subscrições de programador/teste) dentro de inscrição. Para clientes de pay as you go, o âmbito partilhado é todas as subscrições de pay as you go criadas pelo administrador de conta.</li></ul>|
    |Localização    |A região do Azure que é abrangida pela reserva.|    
    |Tamanho da VM     |O tamanho das instâncias da VM.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias que está a ser adquirido dentro de reserva. A quantidade é o número de instâncias VM que podem obter o desconto faturação em execução. Por exemplo, se estiver a executar o 10 Standard_D2 VMs nos EUA leste, em seguida, tem de especificar quantidade como 10 para maximizar o benefício de todas as máquinas em execução. |
5. Pode ver o custo da reserva ao selecionar **calcular o custo**.

    ![Captura de ecrã antes de submeter a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selecione **Compra**.
7. Selecione **ver esta reserva** para ver o estado da compra.

    ![Captura de ecrã antes de submeter a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps-after-buying-a-reservation"></a>Próximos passos depois de comprar uma reserva
O desconto de reserva é aplicado automaticamente para o número de máquinas virtuais que corresponder ao âmbito de reserva e atributos em execução. Pode atualizar o âmbito da reserva através de [portal do Azure](https://portal.azure.com), PowerShell, CLI ou através da API. 

Para saber como gerir uma reserva, consulte [gerir Azure reservado instâncias de Máquina Virtual](https://go.microsoft.com/fwlink/?linkid=861613).

