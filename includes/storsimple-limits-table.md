<!--author=alkohli last changed: 12/15/15-->

| Identificador de limite | Limite | Comentários |
| --- | --- | --- |
| Número máximo de credenciais da conta de armazenamento |64 | |
| Número máximo de contentores de volume |64 | |
| Número máximo de volumes |255 | |
| Número máximo de agendas por modelo de largura de banda |168 |Uma agenda para cada hora, todos os dias da semana (24 * 7). |
| Tamanho máximo de um volume em camadas em dispositivos físicos |64 TB para 8100 e 8600 |8100 e 8600 são dispositivos físicos. |
| Tamanho máximo de um volume em camadas em dispositivos virtuais no Azure |30 TB para 8010 <br></br> 64 TB para o 8020 |dispositivos 8010 e 8020 são dispositivos virtuais no Azure que utilizam o armazenamento Standard e Premium, respetivamente. |
| Tamanho máximo de um volume localmente afixado nos dispositivos físicos |9 TB para 8100 <br></br> 24 TB para 8600 |8100 e 8600 são dispositivos físicos. |
| Número máximo de ligações de iSCSI |512 | |
| Número máximo de ligações de iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de volumes de acordo com a política de cópia de segurança |24 | |
| Número máximo de cópias de segurança retidos por política de cópia de segurança |64 | |
| Número máximo de agendas de acordo com a política de cópia de segurança |10 | |
| Número máximo de instantâneos de qualquer tipo que podem ser mantidas por volume |256 |Isto inclui instantâneos locais e instantâneos de nuvem. |
| Número máximo de instantâneos que podem constar de qualquer dispositivo |10,000 | |
| Número máximo de volumes que podem ser processados em paralelo para cópia de segurança, restauro ou clonar |16 |<ul><li>Se existirem mais de 16 volumes, serão processados sequencialmente como processamento ranhuras fiquem disponíveis.</li><li>Novas cópias de segurança de um clonado ou restaurado volume em camadas não pode ocorrer até que a operação esteja concluída. No entanto, para um volume local, as cópias de segurança são permitidas depois do volume está online.</li></ul> |
| Restaurar e clonar o tempo de recuperar volumes em camadas |< 2 minutos |<ul><li>O volume é disponibilizado em dois minutos da operação de restauro ou um clone, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente pode ser mais lento do que o normal como a maioria dos dados e de metadados ainda reside na nuvem. Pode aumentar o desempenho como fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para transferir metadados depende o tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano à taxa de 5 minutos por TB de dados do volume alocado. Esta taxa pode ser afetada pela largura de banda de Internet para a nuvem.</li><li>O restauro ou a operação de clonagem está concluída quando todos os metadados do dispositivo.</li><li>Não não possível efetuar operações de cópia de segurança até o restauro ou operação de clonagem é totalmente concluída. |
| Restaurar o tempo de recuperação de volumes localmente afixados |< 2 minutos |<ul><li>O volume é disponibilizado em dois minutos da operação de restauro, independentemente do tamanho do volume.</li><li>O desempenho de volume inicialmente pode ser mais lento do que o normal como a maioria dos dados e de metadados ainda reside na nuvem. Pode aumentar o desempenho como fluxos de dados da nuvem para o dispositivo StorSimple.</li><li>O tempo total para transferir metadados depende o tamanho do volume alocado. Metadados automaticamente é colocado no dispositivo em segundo plano à taxa de 5 minutos por TB de dados do volume alocado. Esta taxa pode ser afetada pela largura de banda de Internet para a nuvem.</li><li>Ao contrário de volumes em camadas, no caso de volumes localmente afixados, os dados do volume também são transferidos localmente no dispositivo. A operação de restauro está concluída quando todos os dados de volume esteja no dispositivo.</li><li>As operações de restauro poderão ser longas e o total de tempo para concluir o restauro dependerá do tamanho do volume aprovisionado local, a largura de banda de Internet e os dados existentes no dispositivo. Operações de cópia de segurança no volume localmente afixado são permitidas enquanto a operação de restauro está em curso. |
| Disponibilidade de restauro dinâmico |Última ativação pós-falha | |
| Débito de leitura/escrita do máximo de clientes (quando servido a partir da camada SSD) * |920/720 MB/s com um único 10GbE interface de rede |Até 2 x com MPIO e duas interfaces de rede. |
| Débito de leitura/escrita do máximo de clientes (quando servido a partir da camada HDD) * |120/250 MB/s | |
| Débito de leitura/escrita do máximo de clientes (quando servido a partir da camada de nuvem) * |11/41 MB/s |Débito de leitura depende clientes gerar e manter a profundidade de fila de e/s suficiente. |

&#42; Débito máximo por tipo de e/s foi medido com cenários de 100 por cento escrita e leitura de 100 por cento. Débito real pode ser mais baixo e depende de e/s misturar e condições de rede.

