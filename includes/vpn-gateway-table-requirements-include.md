A tabela abaixo lista os requisitos para gateways de VPN baseados na rota e em políticas. Esta tabela aplica-se aos modelos de implementação clássica e Resource Manager. Para o modelo clássico, os gateways de VPN basos em políticas são os mesmos que os Gateways estáticos e os gateways baseados na rota são os mesmos que os Gateways dinâmicos.


|   | **VPN Gateway Básico baseado em políticas** | **VPN Gateway Básico baseado na rota** | **VPN Gateway Standard baseado na rota**   | **VPN Gateway de Elevado Desempenho baseado na rota** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Conetividade Site a Site (S2S)**  | Configuração de VPN baseada em políticas        | Configuração de VPN baseada na rota  | Configuração de VPN baseada na rota     | Configuração de VPN baseada na rota    |
| **Conetividade Site a Site (P2S**)      | Não suportado   | Suportado (pode coexistir com S2S)  | Suportado (pode coexistir com S2S)  | Suportado (pode coexistir com S2S) |
| **Método de autenticação**                 |    Chave pré-partilhada  | Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S | Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S | Chave pré-partilhada para a conetividade S2S, Certificados para a conetividade P2S |
| **Número máximo de ligações S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Número máximo de ligações P2S**       | Não suportado                  | 128                                                                   | 128                               | 128                              |
|**Suporte de encaminhamento ativo (BGP)**           | Não suportado                  | Não suportado                                                         | Não suportado                     | Não suportado                    |
 


<!--HONumber=Jun16_HO2-->


