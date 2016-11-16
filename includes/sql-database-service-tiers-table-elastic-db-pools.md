
### <a name="basic-elastic-pool-limits"></a>Limites do conjunto elástico básico
|  |  |
| --- |:---:|
| Máximo de eDTUs por conjunto |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Armazenamento máximo por conjunto (GB)* |&nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| Número máximo de bases de dados por conjunto |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Armazenamento OLTP máximo em memória (GB) por conjunto |N/D |
| Máximo de trabalhadores simultâneos por conjunto |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Máximo de inícios de sessão simultâneos por conjunto |&nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Máximo de sessões simultâneas por conjunto |4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| Máximo de eDTUs por base de dados* |5 |
| Mínimo de eDTUs por base de dados* |0,5 |
| Armazenamento máximo por base de dados (GB)** |2 |
| Restauro para um ponto anterior no tempo |Qualquer ponto nos últimos 7 dias |
| Recuperação após desastre |Georreplicação Ativa |
|  | |

* O número máximo e mínimo de eDTUs por base de dados pode ser definido como qualquer um dos valores listados, desde que o tamanho da DTU do conjunto selecionado seja, pelo menos, igual ao máximo de eDTUs por DB 

** A base de dados elástica partilha o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante ou ao armazenamento máximo por base de dados

### <a name="standard-elastic-pool-limits"></a>Limites do conjunto elástico padrão
|  |  |
| --- |:---:|
| Máximo de eDTUs por conjunto |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Armazenamento máximo por conjunto (GB)* |&nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Número máximo de bases de dados por conjunto |&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Armazenamento OLTP máximo em memória (GB) por conjunto |N/D |
| Máximo de trabalhadores simultâneos por conjunto |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de inícios de sessão simultâneos por conjunto |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de sessões simultâneas por conjunto |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Máximo de eDTUs por base de dados* |10, 20, 50, 100 |
| Mínimo de eDTUs por base de dados* |0, 10, 20, 50, 100 |
| Armazenamento máximo por base de dados (GB)** |250 |
| Restauro para um ponto anterior no tempo |Qualquer ponto nos últimos 35 dias |
| Recuperação após desastre |Georreplicação Ativa |
|  | |

* O número máximo e mínimo de eDTUs por base de dados pode ser definido como qualquer um dos valores listados, desde que o tamanho da DTU do conjunto selecionado seja, pelo menos, igual ao máximo de eDTUs por DB 

** A base de dados elástica partilha o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante ou ao armazenamento máximo por base de dados

### <a name="premium-elastic-pool-limits"></a>Limites do conjunto elástico premium
|  |  |
| --- |:---:|
| Máximo de eDTUs por conjunto |125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| Armazenamento máximo por conjunto (GB)* |250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| Número máximo de bases de dados por conjunto |50 |
| Armazenamento OLTP máximo em memória (GB) por conjunto |N/D |
| Máximo de trabalhadores simultâneos por conjunto |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de inícios de sessão simultâneos por conjunto |&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Máximo de sessões simultâneas por conjunto |4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Máximo de eDTUs por base de dados* |125, 250, 500, 1000 |
| Mínimo de eDTUs por base de dados* |0, 125, 250, 500, 1000 |
| Armazenamento máximo por base de dados (GB)** |500 |
| Restauro para um ponto anterior no tempo |Qualquer ponto nos últimos 35 dias |
| Recuperação após desastre |Georreplicação Ativa |
|  | |

* O número máximo e mínimo de eDTUs por base de dados pode ser definido como qualquer um dos valores listados, desde que o tamanho da DTU do conjunto selecionado seja, pelo menos, igual ao máximo de eDTUs por DB 

** A base de dados elástica partilha o armazenamento do conjunto, pelo que o armazenamento da base de dados é limitado ao armazenamento mais pequeno do conjunto restante ou ao armazenamento máximo por base de dados



<!--HONumber=Nov16_HO2-->


