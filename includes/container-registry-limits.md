---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117097"
---
| Recurso | Basic | Standard | Premium |
|---|---|---|---|
| Armazenamento<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Tamanho máximo de camada de imagem | 200 GiB | 200 GiB | 200 GiB |
| ReadOps por minuto<sup>2, 3</sup> | 1,000 | 3\.000 | 10.000 |
| WriteOps por minuto<sup>2, 4</sup> | 100 | 500 | 2\.000 |
| MBps de largura de banda de download<sup>2</sup> | 30 | 60 | 100 |
| MBps de largura de banda de upload<sup>2</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| Replicação geográfica | N/D | N/D | [Com suporte][geo-replication] |
| Confiança em conteúdo | N/D | N/D | [Com suporte][content-trust] |
| Acesso à rede virtual | N/D | N/D | [Visualização][vnet] |
| Integração de link privado | N/D | N/D | [Visualização][plink] |
| Chaves gerenciadas pelo cliente | N/D | N/D | [Visualização][cmk] |
| Permissões no escopo do repositório | N/D | N/D | [Visualização][token]|
| &bull; Tokens | N/D | N/D | 20,000 |
| &bull; Mapas de escopo | N/D | N/D | 20,000 |
| &bull; Repositórios por mapa de escopo | N/D | N/D | 500 |


<sup>1</sup>Os limites de armazenamento especificados são a quantidade de armazenamento *incluída* para cada camada. Será cobrada uma taxa diária adicional por GiB para o armazenamento de imagens acima desses limites. Para obter informações de taxa, confira [Preço de Registro de Contêiner do Azure][pricing].

<sup>2</sup>*ReadOps*, *WriteOps* e *Largura de Banda* são estimativas mínimas. O Registro de Contêiner do Azure se esforça para aprimorar o desempenho conforme os requisitos de uso.

<sup>3</sup>Um [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduz em várias operações de leitura com base no número de camadas na imagem, além da recuperação do manifesto.

<sup>4</sup>Um [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduz em várias operações de gravação com base no número de camadas que devem ser enviadas por push. Um `docker push` inclui *ReadOps* para recuperar um manifesto para uma imagem existente.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md