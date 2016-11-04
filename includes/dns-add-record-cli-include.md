#### Criar um conjunto de registos A com um único registo
Para criar um conjunto de registos, utilize `azure network dns record-set create`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registos, o tipo de registo e o limite de tempo (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Depois de criar o conjunto de registos A, adicione o endereço IPv4 ao conjunto de registos com `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### Criar um conjunto de registos AAAA com um único registo
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### Criar um conjunto de registos CNAME com um único registo
Os registos CNAME permitem apenas um valor de cadeia única.

    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### Criar um conjunto de registos MX com um único registo
Neste exemplo, utilizamos o nome do conjunto de registos “@” para criar o registo MX no vértice da zona (neste caso, “contoso.com”). Isto é comum para registos MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### Criar um conjunto de registos NS com um único registo
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### Criar um conjunto de registos PTR com um único registo
Neste caso, “my-arpa-zone.com” representa a zona ARPA, que representa o seu intervalo de IP.  Cada conjunto de registos PTR nesta zona corresponde a um endereço IP neste intervalo de IPs.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### Criar um conjunto de registos SRV com um único registo
Se estiver a criar um registo SRV na raiz da zona, pode especificar “_service” e “_protocol” no nome do registo. Não é necessário incluir “@” no nome do registo.

    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### Criar um conjunto de registos TXT com um único registo
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"


<!--HONumber=Sep16_HO4-->


