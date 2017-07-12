O Azure oferece os seguintes SKU de gateway de VPN:

|**SKU**   | **S2S/VNet para VNet<br>Túneis** | **P2S<br>Ligações** | **Agregação<br>Taxa de transferência** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Um máximo de 30                         | Um máximo de 128               | 500 Mbps                    |
|**VpnGw2**| Um máximo de 30                         | Um máximo de 128               | 1 Gbps                      |
|**VpnGw3**| Um máximo de 30                         | Um máximo de 128               | 1,25 Gbps                   |
|**Básica** | Um máximo de 10                         | Um máximo de 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- A taxa de transferência é baseada nas medidas de vários túneis agregados por meio de um único gateway. Não é uma taxa de transferência garantida devido a condições de tráfego de Internet e aos comportamentos da sua aplicação.

- As informações sobre os preços estão disponíveis na página [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Pode encontrar informações sobre SLA (contrato de nível de serviço) na página do [SLA](https://azure.microsoft.com/en-us/support/legal/sla/vpn-gateway/).