## <a name="load-balancer-differences"></a>Diferenças do Balanceador de Carga

Existem diferentes opções para distribuir o tráfego de rede com o Microsoft Azure. Estas opções funcionam de forma diferente entre si, tendo um conjunto de funcionalidades diferente e suportando diferentes cenários. Podem todos ser utilizados em isolamento ou em conjunto.

* O **Balanceador de Carga do Azure** funciona na camada de transporte (Camada 4 na pilha de referência de rede OSI). Este fornece distribuição de tráfego ao nível da rede em várias instâncias de uma aplicação em execução no mesmo Centro de dados do Azure.
* O **Gateway de Aplicação** funciona na camada da aplicação (Camada 7 na pilha de referência de rede OSI). Atua como um serviço de proxy de reversão, terminando a ligação de cliente e reencaminhando pedidos de pontos finais de back-end.
* O **Gestor de Tráfego** funciona ao nível do DNS.  Utiliza as respostas de DNS para direcionar o tráfego de utilizador final para pontos finais distribuídos globalmente. Em seguida, os clientes podem ligar diretamente a esses pontos finais.

A tabela seguinte resume as funcionalidades oferecidas por cada serviço:

| Serviço | Azure Load Balancer | Gateway de Aplicação | Gestor de Tráfego |
| --- | --- | --- | --- |
| Tecnologia |Nível de transporte (Camada 4) |Nível de aplicação (Camada 7) |Nível de DNS |
| Protocolos de aplicação suportados |Qualquer |HTTP, HTTPS e WebSockets |Qualquer (É preciso um ponto final HTTP para a monitorização de ponto final) |
| Pontos Finais |Instâncias da função de VMs do Azure e Serviços Cloud |Qualquer endereço de IP interno do Azure, endereço IP de Internet pública, VM do Azure ou Serviço Cloud do Azure |VMs do Azure, Serviços Cloud, Aplicações Web do Azure e os pontos finais externos |
| Suporte de Vnet |Serve para ambas as aplicações (Vnet) internas e com acesso à Internet |Serve para ambas as aplicações (Vnet) internas e com acesso à Internet |Suporta apenas aplicações de acesso à Internet |
| Monitorização de Pontos Finais |Suportado através de sondas |Suportado através de sondas |Suportado através de HTTP/HTTPS GET |

O Balanceador de Carga do Azure e o Gateway de Aplicação encaminham tráfego de rede para pontos finais, mas têm cenários de utilização diferentes para processar o tráfego. A tabela seguinte ajuda-o a compreender a diferença entre dois balanceadores de carga:

| Tipo | Azure Load Balancer | Gateway de Aplicação |
| --- | --- | --- |
| Protocolos |UDP/TCP |HTTP, HTTPS e WebSockets |
| Reserva de IP |Suportado |Não suportado |
| Modo de balanceamento de carga |5 cadeias de identificação (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) |Round Robin<br>Encaminhamento com base no URL |
| Modo de balanceamento de carga (IP de origem/sessões temporárias) |duas cadeias de identificação (IP de origem e destino IP),três cadeias de identificação (IP de origem, IP de destino e porta). Pode aumentar ou reduzir verticalmente, com base no número de máquinas virtuais |Afinidade com base no cookie<br>Encaminhamento com base no URL |
| Sondas do estado de funcionamento |Predefinido: intervalo da sonda - 15 seg. Retiradas da rotação: duas falhas contínuas. Suporta sondas definidas pelo utilizador |Intervalo da sonda inativo 30 seg. Retirado depois de cinco falhas consecutivas de tráfego em direto ou uma falha de sonda única no modo inativo. Suporta sondas definidas pelo utilizador |
| Descarga de SSL |Não suportado |Suportado |
| Com base no URL de encaminhamento | Não suportado | Suportado|
| Política SSL | Não suportado | Suportado|
