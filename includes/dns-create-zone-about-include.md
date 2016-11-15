Uma zona DNS serve para alojar os registos DNS para um determinado domínio. Para iniciar o alojamento do seu domínio, terá de criar uma zona DNS. Qualquer registo DNS criado para um determinado domínio ficará dentro de uma zona DNS do domínio.

Por exemplo, o domínio “contoso.com” pode conter vários registos DNS, como “mail.contoso.com” (para um servidor de e-mail) e “www.contoso.com” (para um site).

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>Sobre os nomes das zonas DNS
* O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não pode já existir. Caso contrário, a operação falhará.
* O mesmo nome de zona pode ser reutilizado num grupo de recursos diferente ou numa subscrição diferente do Azure.
* Quando várias zonas partilham o mesmo nome, cada instância receberá endereços de servidor de nomes diferentes e apenas uma instância pode ser delegada do domínio principal. Para obter mais informações, veja [Delegar um domínio ao DNS do Azure](../articles/dns/dns-domain-delegation.md).


<!--HONumber=Nov16_HO2-->


