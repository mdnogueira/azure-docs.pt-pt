## <a name="about-records"></a>Sobre os registos

Cada registo DNS tem um nome e um tipo. Os registos são organizados em vários tipos de acordo com os dados que contêm. O tipo mais comum é um registo “A”, que mapeia um nome para um endereço IPv4. Outro tipo é um registo “MX”, que mapeia um nome para um servidor de correio.

O DNS do Azure suporta todos os tipos de registo DNS comuns, incluindo A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Tenha em atenção que:

* Os conjuntos de registos SOA são criados automaticamente com cada zona; não podem ser criados em separado.
* Os registos SPF devem ser criados com o tipo de registo TXT. Para obter mais informações, veja [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

No DNS do Azure, os registos são especificados com nomes relativos. Um nome de domínio “completamente qualificado” (FQDN) inclui o nome da zona, enquanto um nome “relativo” não. Por exemplo, o nome do registo relativo “www” na zona “contoso.com” fornece o nome do registo completamente qualificado www.contoso.com.

## <a name="about-record-sets"></a>Sobre os conjuntos de registos

Por vezes, precisa de criar mais do que um registo DNS com um determinado nome e tipo. Por exemplo, suponha que o site “www.contoso.com” está alojado em dois endereços IP diferentes. O site necessita de dois registos A diferentes, um para cada endereço IP. Este é um exemplo de um conjunto de registos:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

O DNS do Azure gere registos DNS com conjuntos de registos. Um conjunto de registos é uma coleção de registos DNS numa zona com o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registos contém um único registo, contudo, não são incomuns exemplos como este no qual um conjunto de registos contém mais do que um registo.

Os conjuntos de registos SOA e CNAME são exceções. As normas DNS não permitem vários registos com o mesmo nome para estes tipos.

O limite de tempo, ou TTL, especifica o tempo que cada registo é colocado em cache por clientes antes de voltar a ser consultado. Neste exemplo, o TTL é de 3600 segundos ou 1 hora. O TTL é especificado para o conjunto de registos, não para cada registo, para que o mesmo valor seja utilizado em todos os registos nesse conjunto de registos.

#### <a name="wildcard-record-sets"></a>Conjuntos de registos de carateres universais

O DNS do Azure suporta [registos de carateres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estes são devolvidos para qualquer consulta com um nome correspondente (a menos que exista uma correspondência mais próxima de um conjunto de registos de carateres não universais). Os conjuntos de registos de carateres universais são suportados para todos os tipos de registos, exceto NS e SOA.

Para criar um conjunto de registos de carateres universais, utilize o nome do conjunto de registos “\*”. Alternativamente, utilize um nome com a etiqueta “\*”, por exemplo, “\*.foo”.

#### <a name="cname-record-sets"></a>Conjuntos de registos CNAME

Os conjuntos de registos CNAME não podem coexistir com outros conjuntos de registos com o mesmo nome. Por exemplo, não pode criar em simultâneo um conjunto de registos CNAME com o nome relativo “www” e um registo A com o nome relativo “www”. Uma vez que o vértice da zona (nome = ‘@’)) contém sempre os conjuntos de registos NS e SOA, criados quando a zona foi criada, não pode criar um conjunto de registos CNAME no vértice da zona. Estas restrições derivam das normas DNS e não são limitações do DNS do Azure.


<!--HONumber=Nov16_HO2-->


