

| **Identificador de limite** | **Limite** | **Comentários** |
| --- | --- | --- |
| Capacidade total (incluindo a nuvem) |Por dispositivo virtual até 64 TB |Pode falhar uma matriz de Virtual StorSimple completa para outra matriz vazia. Se tentar restaurar ao mesmo dispositivo, certifique-se de que tem espaço suficiente no dispositivo para concluir esta operação. Após ter excedido 32 TB, não é possível restaurar para o mesmo dispositivo. |
| Número máximo de credenciais da conta do storage por dispositivo |1 | |
| Número máximo de volumes/partilhas |16 | |
| Tamanho mínimo de uma partilha em camadas |500 GB | |
| Tamanho mínimo de um volume em camadas |500 GB | |
| Tamanho máximo de uma partilha em camadas |20 TB | |
| Tamanho máximo de um volume em camadas |5 TB | |
| Tamanho mínimo de uma partilha afixado localmente |50 GB | |
| Tamanho mínimo de um volume localmente afixado |50 GB | |
| Tamanho máximo de uma partilha afixado localmente |2 TB | |
| Tamanho máximo de um volume localmente afixado |200 GB | |
| Número máximo de ligações de iSCSI de iniciadores |512 | |
| Número máximo de registos de controlo de acesso por dispositivo |64 | |
| Número máximo de cópias de segurança mantido pelo dispositivo virtual no *.backups* pasta para o servidor de ficheiros |5 |Isto inclui o mais recente agendada (gerado pela política de cópia de segurança predefinido) e as cópias de segurança manuais. |
| Número máximo de cópias de segurança agendadas mantido pelo dispositivo |55 |30 cópias de segurança diárias<br>12 cópias de segurança mensais<br>13 cópias de segurança anuais |
| Número máximo de cópias de segurança manuais mantido pelo dispositivo |45 | |
| Número máximo de ficheiros por partilha para um servidor de ficheiros |1 milhão |Quando efetuar um restauro de dispositivo, os tempos de restauro são proporcional ao número de ficheiros em todas as partilhas no dispositivo. |
| Número máximo de ficheiros por volume para um servidor de iSCSI |1 milhão | |
| Número máximo de ficheiros por cada matriz virtual |milhões de 4 | |
| Restaurar o tempo de recuperação |Restauro rápido |O restauro baseia-se no mapa térmico e depende do tamanho do volume.<br>Operações de cópia de segurança podem ocorrer durante uma operação de restauro está em curso. |

