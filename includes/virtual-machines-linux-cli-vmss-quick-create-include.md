Se ainda não o fez, pode obter uma [Versão de avaliação gratuita da subscrição do Azure](https://azure.microsoft.com/pricing/free-trial/) e a [CLI do Azure](../articles/xplat-cli-install.md) [ligada à sua conta do Azure](../articles/xplat-cli-connect.md). Confirme que a CLI do Azure está no modo Resource Manager da seguinte forma:

```azurecli
azure config mode arm
```

Agora, utilize o comando `azure vmss quick-create` para criar o conjunto de dimensionamento. O exemplo seguinte cria um conjunto de dimensionamento com o nome `myVMSS`, com cinco instâncias de VM no grupo de recursos com o nome `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

Se pretender personalizar a localização ou a imagem_urn, veja os comandos `azure location list` e `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

Quando este comando for devolvido, significa que o conjunto de dimensionamento foi criado. Este conjunto de dimensionamento terá um balanceador de carga com regras NAT para mapear a porta 50,000+ i no balanceador de carga para a porta 22 na VM. Assim, depois de descobrir o FQDN do balanceador de carga, poderemos ligar através de ssh às nossas VMs:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Nov16_HO2-->


