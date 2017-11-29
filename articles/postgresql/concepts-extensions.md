---
title: "Utilizar extensões de PostgreSQL na base de dados do Azure para PostgreSQL | Microsoft Docs"
description: "Descreve a capacidade de expandir a funcionalidade da base de dados utilizando as extensões na base de dados do Azure para PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/28/2017
ms.openlocfilehash: f02588495e7107b34dac7e076cf3612de12b51d4
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>Extensões de PostgreSQL na base de dados do Azure para PostgreSQL
PostgreSQL fornece a capacidade de expandir a funcionalidade da base de dados utilizando extensões. As extensões permitem para agrupamento vários objetos relacionados do SQL Server em conjunto num pacote único que pode ser carregado ou removido da sua base de dados com um único comando. Após a ser carregado na base de dados, as extensões podem funcionar como funções incorporadas. Para obter mais informações sobre PostgreSQL extensões, consulte [empacotamento objetos relacionados para uma extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>Como utilizar PostgreSQL extensões
Extensões de PostgreSQL tem de estar instaladas na base de dados antes de poder utilizá-los. Para instalar uma extensão específica, execute o [criar extensão](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) comando da ferramenta de psql para carregar os objetos em pacote na sua base de dados.

Base de dados do Azure para PostgreSQL suporta atualmente um subconjunto das extensões de chaves, conforme listado abaixo. Além dos listados não são suportadas; Não é possível criar o seus próprios extensão com a base de dados do Azure para o serviço de PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões suportadas pela base de dados do Azure para PostgreSQL
As tabelas seguintes listam as extensões de PostgreSQL padrão que são atualmente suportadas pela base de dados do Azure para PostgreSQL. Estas informações também estão disponíveis consultando `pg\_available\_extensions`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Fornece um tipo de dados encriptados automaticamente palavras-passe. |
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de carateres sensível. |
| [cubo](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados de cubos multidimensionais. |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave/valor. |
| [se encontra](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece os tipos de dados para o produto internacional numeração normas. |
| [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas de semelhante de árvore hierárquicas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular as distâncias de ótimo círculo na superfície terrestre. |
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e distância entre cadeias. |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece operadores e funções de manipulação de matrizes de libertação de um valor nulo de números inteiros. |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções criptográficas. |
| [PG\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gere as tabelas particionadas por hora ou ID. |
| [PG\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece operadores e as funções para determinar a semelhança de texto alfanumérico com base na correspondência de trigram. |
| [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece as funções que manipular tabelas completa, incluindo tabela cruzada. |
| [UUID ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera identificadores universalmente exclusivos (UUIDs não). |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa em texto completo

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto de números inteiros. |
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove o lexemes accents (sinais diacritic). |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece exemplo GIN operador classes que implementam árvore B, como o comportamento para determinados tipos de dados. |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam árvore B. |

### <a name="language-extensions"></a>Extensões de idioma

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | LP/pgSQL idioma procedimento carregado. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [PG\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que acontece na cache de memória intermédia partilhada em tempo real. |
| [PG\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma forma para carregar dados de relação para a cache de memória intermédia. |
| [PG\_stat\_instruções](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para controlar as estatísticas de execução de todas as declarações de SQL foi executadas por um servidor. |
| [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para que mostra informações de bloqueio ao nível da linha. |
| [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para que mostra estatísticas de nível de cadeia de identificação. |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externa utilizado para aceder a dados armazenados em servidores de PostgreSQL externos. |

### <a name="postgis-extensions"></a>Extensões de PostGIS

> [!div class="mx-tableFixed"]
| **Extensão** | **Descrição** |
|---|---|
| [PostGIS](http://www.postgis.net/), postgis\_topologia, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos geográficos e geográficos para PostgreSQL. |
| endereço\_standardizer, endereço\_standardizer\_dados\_-nos | Utilizado para analisar um endereço em elementos que constituem. Utilizado para suportar o passo de normalização de endereço a codificação geográfica. |
| [pgrouting](http://pgrouting.org/) | Expande os PostGIS / funcionalidade de encaminhamento de base de dados para fornecer geoespacial PostgreSQL geoespacial. |

## <a name="next-steps"></a>Passos seguintes
Se não vir uma extensão que pretende utilizar, informe-nos. Votar para pedidos existentes ou criar novos comentários e pedidos na nossa [fórum de comentários do cliente](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
