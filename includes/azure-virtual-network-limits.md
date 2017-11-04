<a name="virtual-networking-limits-classic"></a>Os limites seguintes só se aplicam a recursos de rede geridos através do modelo de implementação clássica por subscrição.

| Recurso | Limite predefinido | Limite máximo |
| --- | --- | --- |
| Redes virtuais |50 |100 |
| Sites de rede local |20 |contactar o suporte |
| Servidores de DNS por rede virtual |20 |100 |
| Endereços de IP privados por rede virtual |4096 |4096 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500 K |500 K |
| Grupos de Segurança de Rede (NSG) |100 |200 |
| Regras do NSG por NSG |200 |400 |
| Tabelas da rota definida pelo utilizador |100 |200 |
| Rotas definidas pelo utilizador por tabela de rota |100 |400 |
| Endereços IP públicos (dinâmico) |5 |contactar o suporte |
| Endereços IP públicos reservados |20 |contactar o suporte |
| VIP público por implementação |5 |contactar o suporte |
| VIP privado (ILB) por implementação |1 |1 |
| Listas de Controlo de Acesso de Ponto Final (ACLs) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Limites de Rede - Azure Resource Manager
Os seguintes limites só se aplicam aos recursos de rede geridos através do Azure Resource Manager por região por subscrição.

| Recurso | Limite predefinido | Limite Máximo |
| --- | --- | --- |
| Redes virtuais |50 |1000 |
| Sub-redes por rede virtual |1000 |10000 |
| Peerings de rede virtual por rede Virtual |10 |50 |
| Servidores de DNS por rede virtual |9 |25 |
| Endereços de IP privados por rede virtual |4096 |8192 |
| Endereços IP privados por interface de rede |256 |1024 |
| Em simultâneo TCP ou UDP flui por NIC de uma máquina virtual ou instância de função |500 K |500 K |
| Interfaces de Rede (NIC) |350 |20000 |
| Grupos de Segurança de Rede (NSG) |100 |5000 |
| Regras do NSG por NSG |200 |500 |
| Endereços IP e intervalos especificados para a origem ou de destino numa regra de segurança |2000 |4000 |
| Grupos de segurança de aplicações |200 |500 |
| Grupos de segurança de aplicações por configuração de IP, por NIC |10 |20 |
| Configurações de IP por grupo de segurança de aplicações |1000 |4000 |
| Grupos de segurança de aplicações que podem ser especificados em todas as regras de segurança de um grupo de segurança de rede |50 |100 |
| Tabelas da rota definida pelo utilizador |100 |200 |
| Rotas definidas pelo utilizador por tabela de rota |100 |400 |
| Endereços IP públicos - dinâmica |60 (básico) |contactar o suporte |
| Endereços IP públicos - estáticos |20 (básico) |contactar o suporte |
| Endereços IP públicos - estáticos |20 (padrão) |contactar o suporte |
| Certificados de Raiz do Ponto-a-Site por Gateway de VPN |20 |20 |

#### <a name="load-balancer"></a>Limites de Balanceador de carga

| Recurso | Limite predefinido | Limite Máximo |
| --- | --- | --- |
| Balanceadores de carga | 100 | 1000 |
| Regras por recurso, Basic | 150 | 250 |
| Regras por recurso, Standard | 1250 | 1500 |
| Regras por configuração de IP | 299 |299 |
| Configurações de IP de front-end, Basic | 10 | contactar o suporte |
| Configurações de IP de front-end, Standard | 10 | 600 |
| Conjunto de back-end, Basic | 100, único conjunto de disponibilidade | - |
| Conjunto de back-end, Standard | 1000, único VNet | contactar o suporte |
| HA portas, Standard | 1 por interno front-end | - |

Caso seja necessário aumentar os limites para lá da predefinição, [contacte o suporte](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

