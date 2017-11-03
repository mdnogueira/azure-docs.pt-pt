Uma máquina virtual do Azure suporta anexar um número de discos de dados. Para obter o melhor desempenho, deverá limitar o número dos discos mais utilizados anexados à máquina virtual para evitar uma possível limitação. Se todos os discos não estiverem a ser muito utilizados ao mesmo tempo, a conta de armazenamento pode suportar um maior número de discos.

* **Para discos gerida do Azure:** limite de contagem de discos geridos é regional e também depende do tipo de armazenamento. A predefinição e também o limite máximo é de 10 000 por subscrição, por região e por tipo de armazenamento. Por exemplo, pode criar até 10 000 padrão geridos discos e também 10 000 premium geridos discos numa subscrição e numa região. 

    Os Instantâneos Geridos e as imagens são contadas de acordo com o limite dos Managed Disks.

* **Para contas de armazenamento standard:** uma conta de armazenamento standard tem uma taxa de pedidos total máxima de 20.000 IOPS. O IOPS total em todos os discos da máquina virtual numa conta de armazenamento standard não deve exceder este limite.
  
    Pode calcular aproximadamente o número de discos muito utilizados suportados por uma única conta de armazenamento standard com base no limite da taxa de pedido. Por exemplo, para uma VM de Escalão Básico, o número máximo de discos muito utilizados é cerca de 66 (20.000/300 IOPS por disco) e para uma VM de escalão Standard, é cerca de 40 (20.000/500 IOPS por disco), conforme mostrado na tabela abaixo. 
* **Para contas de armazenamento premium:** uma conta de armazenamento premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

