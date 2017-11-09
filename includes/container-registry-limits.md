| Recurso | Básica | Standard | Premium |
|---|---|---|---|---|
| Armazenamento | 10 giB | 100 giB| 500 giB |
| ReadOps por minuto<sup>1, 2</sup> | 1k | 300k | 10 000 k |
| WriteOps por minuto<sup>1, 3</sup> | 100 | 500 | k de 2 |
| Transferir a largura de banda MBps<sup>1</sup> | 30 | 60 | 100 |
| Carregar MBps da largura de banda<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Georreplicação | N/D | N/D | [Suportado *(pré-visualização)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*, *WriteOps*, e *largura de banda* são estimativas mínimo. ACR strives melhorar o desempenho porque requer a utilização.

<sup>2</sup> [solicitação docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) traduz-se várias operações de leitura, com base no número de camadas na imagem, mais a obtenção de manifesto.

<sup>3</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) traduz-se várias operações de escrita, com base no número de camadas tem de ser instalada. A `docker push` inclui *ReadOps* para obter um manifesto para uma imagem existente.