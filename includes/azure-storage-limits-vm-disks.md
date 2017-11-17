Uma máquina virtual do Azure suporta anexar um número de discos de dados. Este artigo descreve metas de desempenho e escalabilidade para discos de dados de uma VM. Utilize nestes destinos para ajudar a decidir o número e tipo de disco que é necessário para satisfazer os seus requisitos de desempenho e a capacidade. 

> [!IMPORTANT]
> Para um desempenho ideal, limite o número de elevada disponibilidade sobreutilizados discos ligados à máquina virtual para evitar possíveis limitação. Se todos os discos ligados não são altamente utilizados ao mesmo tempo, em seguida, a máquina virtual pode suportar um grande número de discos.

* **Para discos gerida do Azure:** o limite de disco para discos geridos é por região e por tipo de disco. O limite máximo bem como a predefinição limite é 10 000 discos geridos por região e por tipo de disco para uma subscrição. Por exemplo, pode criar até 10 000 padrão geridos discos e também 10 000 premium geridos discos numa região por subscrição.

    Gerido instantâneos e imagens contagem contra o limite de discos geridos.

* **Para contas de armazenamento standard:** uma conta de armazenamento standard tem uma taxa de pedidos total máxima de 20.000 IOPS. O IOPS total em todos os discos da máquina virtual numa conta de armazenamento standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos muito utilizados suportados por uma única conta de armazenamento standard com base no limite da taxa de pedido. Por exemplo, para uma VM de escalão básicas, o número máximo de discos altamente sobreutilizados é sobre 66 (20.000/300 IOPS por disco) e para uma VM de escalão Standard, está prestes a 40 (20.000/500 IOPS por disco). 

* **Para contas de armazenamento premium:** uma conta de armazenamento premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

