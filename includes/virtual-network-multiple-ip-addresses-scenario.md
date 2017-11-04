## <a name="scenario"></a>Cenário
Uma VM com um único NIC está criada e ligada a uma rede virtual. A VM requer três diferentes *privada* IP endereços e dois *pública* endereços IP. Os endereços IP são atribuídos para as seguintes configurações de IP:

* **IPConfig-1:** atribui um *estático* endereço IP privado e um *estático* endereço IP público.
* **IPConfig-2:** atribui um *estático* endereço IP privado e um *estático* endereço IP público.
* **IPConfig-3:** atribui um *estático* endereço IP privado e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

As configurações de IP associadas para o NIC quando a NIC está criada e a NIC está ligada à VM quando é criada a VM. Os tipos de endereços IP utilizados para o cenário são para fins de ilustração. Pode atribuir qualquer tipos de endereço e a atribuição de IP precisa.

> [!NOTE]
> Apesar dos passos neste artigo atribui todas as configurações de IP para um único NIC, também pode atribuir várias configurações de IP para qualquer NIC na VM de vários NICs. Para saber como criar uma VM com vários NICs, leia o [criar uma VM com vários NICs](../articles/virtual-machines/windows/multiple-nics.md) artigo.