Ao adicionar discos de dados para uma VM com Linux, pode deparar-se com erros se não existir um disco no LUN 0. Se estiver a adicionar um disco manualmente utilizando o `azure vm disk attach-new` comandos e especificar um LUN (`--lun`) em vez de permitir que a plataforma do Azure determinar o LUN adequado, asseguramos que um disco já existe de colunas / existirá em LUN 0. 

Considere o seguinte exemplo que mostra um fragmento de saída de `lsscsi`:

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

Os discos de dados de dois existem no LUN 0 e LUN 1 (a primeira coluna no `lsscsi` detalhes de saída `[host:channel:target:lun]`). Os dois discos devem ser accessbile a partir de dentro da VM. Se tinha especificado manualmente o primeiro disco a ser adicionado em LUN 1 e o segundo disco em LUN 2, poderá não ver os discos corretamente da dentro da VM.

> [!NOTE]
> O Azure `host` valor é 5 nestes exemplos, mas isto pode variar consoante o tipo de armazenamento que selecionar.
> 
> 

Este comportamento de disco não é um problema do Azure, mas a forma no qual o kernel do Linux segue as especificações de SCSI. Quando o kernel do Linux analisa o barramento SCSI para dispositivos ligados, um dispositivo tem de ser encontrado em LUN 0 para que o sistema para continuar a análise de dispositivos adicionais. Como tal:

* Reveja o resultado de `lsscsi` depois de adicionar um disco de dados para verificar que tem um disco no LUN 0.
* Se o disco não aparecer corretamente na VM, certifique-se de que existe um disco no LUN 0.

