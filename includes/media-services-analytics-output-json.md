A tarefa produz um ficheiro de saída JSON que contém metadados sobre faces detetados e controladas. Os metadados incluem coordenadas que indica a localização do faces, bem como um número de ID de rostos em que indica que o controlo de que individuais. Números de ID de rostos em são suscetíveis a repor circunstâncias quando o enfrentam frontal é perdido ou que se sobreponham a num intervalo, resultando em alguns indivíduos obter atribuídos a vários IDs.

A saída JSON inclui os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| Versão |Isto refere-se para a versão da API de vídeo. |
| Índice | (Aplicável apenas ao Redactor de suporte de dados do Azure) define o índice de moldura do evento atual. |
| escala temporal |"Ticks" por segundo do vídeo. |
| deslocamento |Este é o desvio da hora para carimbos de hora. Na versão 1.0 do vídeo APIs, este será sempre 0. No futuro cenários que suportamos, pode alterar este valor. |
| framerate |Fotogramas por segundo do vídeo. |
| fragmentos |Os metadados é partes cópias de segurança em diferentes segmentos chamados fragmentos. Cada fragmento contém um início, a duração, o número do intervalo e o evento (s). |
| start |A hora de início do primeiro evento na 'ticks'. |
| Duração |O comprimento do fragmento, no "ticks". |
| intervalo |Intervalo de cada entrada de evento dentro de fragmento, no "ticks". |
| eventos |Cada evento contém faces detetados e monitorizados dentro desse duração de tempo. É uma matriz de matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituído por 0 ou mais eventos que ocorreram neste ponto no tempo. Uma [Reto vazia] significa que nenhuma faces foram detetados. |
| ID |O ID de rosto que está a ser controlado. Este número poderá, inadvertidamente, alterar se um rosto torna-se determinados. Um indivíduo fornecido deve ter o mesmo ID em toda o vídeo geral, mas este não poderá ser garantida devido a limitações de algoritmo de deteção (occlusion, etc.) |
| x, y |O canto superior esquerdo X e Y coordenadas de rosto delimitadora caixa numa escala normalizada entre 0,0 e 1,0. <br/>-X e Y coordenadas são relativas ao horizontal sempre, pelo que o se tiver um vertical vídeo (ou upside pendente, no caso de iOS), terá de transpor as coordenadas em conformidade. |
| largura, altura |A largura e altura de rosto delimitadora caixa numa escala normalizada entre 0,0 e 1,0. |
| facesDetected |Este encontra-se no fim dos resultados JSON e resume o número de faces que o algoritmo detetado durante o vídeo. Porque os IDs podem ser repostos inadvertidamente, se um rosto torna-se determinados (por exemplo, enfrentam fica desligar o ecrã, procura ausente), este número poderá não sempre igual a verdadeira diversas faces as vídeo. |

