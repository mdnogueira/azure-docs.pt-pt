Transações da chave (transações máxima permitidas dentro de 10 segundos, por cofre por região<sup>1</sup>):

|tipo de chave|Chave HSM<br>CRIAR a chave|Chave HSM<br>Todas as outras transações|Chave de software<br>CRIAR a chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2048 bits|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096 bits|5|125|10|250|
|

Segredos, chaves de conta de armazenamento gerida e transações do cofre:
| Tipo de transações | Transações de máxima permitidas dentro de 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2000 |
|

<sup>1</sup> há um limite de toda a subscrição para todos os tipos de transação, o que é x 5 por limite de Cofre de chaves. Por exemplo, HSM - outras transações por subscrição estão limitadas a 5000 transações dentro de 10 segundos por subscrição.
