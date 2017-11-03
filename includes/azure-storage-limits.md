| Recurso | Limite Predefinido |
| --- | --- |
| Número de contas do storage por subscrição | 200<sup>1</sup> |
| Capacidade de conta de armazenamento máximo | 500 TiB<sup>2</sup> |
| Número máx. de contentores de BLOBs, blobs, partilhas de ficheiros, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Entrada máximo<sup>3</sup> por conta de armazenamento (regiões EUA) | 10 Gbps se GRS/ZRS<sup>4</sup> ativada, 20 Gbps para LRS<sup>2</sup> |
| Saída máximo<sup>3</sup> por conta de armazenamento (regiões EUA) | 20 Gbps se RA-GRS/GRS/ZRS<sup>4</sup> ativada, 30 Gbps para LRS<sup>2</sup> |
| Entrada máximo<sup>3</sup> por conta de armazenamento (regiões de não-US) | 5 Gbps se GRS/ZRS<sup>4</sup> ativada, 10 Gbps para LRS<sup>2</sup> |
| Saída máximo<sup>3</sup> por conta de armazenamento (regiões de não-US) | 10 Gbps se RA-GRS/GRS/ZRS<sup>4</sup> ativada, 15 Gbps para LRS<sup>2</sup> |

<sup>1</sup>inclui as contas do storage Standard e Premium. Se necessitar de mais de 200 contas de armazenamento, efetue um pedido através do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipa do Armazenamento do Azure analisará o seu caso comercial e pode aprovar até 250 contas de armazenamento. 

<sup>2</sup> para obter as contas de armazenamento standard para aumentar passado os limites anunciados na taxa de capacidade, a entrada/saída e a pedido, certifique-se um pedido de [suporte do Azure](https://azure.microsoft.com/support/faq/). A equipa de armazenamento do Azure irá examinar o pedido e pode aprovar os limites superiores numa base de maiúsculas e minúsculas, maiúsculas e minúsculas.

<sup>3</sup> limitado apenas por limites de entrada/saída da conta. *Entrada* refere-se a todos os dados (pedidos) que está a ser enviados para uma conta de armazenamento. *Entrada* refere-se a todos os dados (respostas) recebidas de uma conta de armazenamento.  

<sup>4</sup>as opções de redundância do armazenamento do azure incluem:
* **RA-GRS**: armazenamento georredundante com acesso de leitura. Se estiver ativada RA-GRS, destinos de saída para a localização secundária são idênticos para a localização primária.
* **GRS**: armazenamento georredundante. 
* **O ZRS**: armazenamento com redundância de zona. Disponível apenas para blobs de blocos. 
* **LRS**: armazenamento localmente redundante. 