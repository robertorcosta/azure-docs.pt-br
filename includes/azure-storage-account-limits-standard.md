---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334667"
---
A tabela a seguir descreve os limites padrão para a finalidade geral do Azure v1, v2, armazenamento de BLOBs, armazenamento de blobs de blocos e Data Lake Storage Gen2 contas de armazenamento habilitadas. O limite de *entrada* refere-se a todos os dados que são enviados para uma conta de armazenamento. O limite de *saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas de armazenamento padrão, Premium e Data Lake Storage Gen2 habilitadas. <sup>3</sup> | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de contêineres de BLOB, BLOBs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (EUA, regiões da Europa) | 25 Gbps |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões diferentes de EUA e Europa) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Egresso máxima para contas de armazenamento de BLOBs V2 e de uso geral (todas as regiões) | 50 Gbps |
| Egresso máxima para contas de armazenamento v1 de uso geral (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Egresso máxima para contas de armazenamento v1 de uso geral (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtual por conta de armazenamento | 200 |
| Número máximo de regras de endereço IP por conta de armazenamento | 200 |

<sup>1</sup> as contas padrão de armazenamento do Azure dão suporte a limites de capacidade mais altos e limites mais altos para entrada por solicitação. Para solicitar um aumento nos limites de conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> se sua conta de armazenamento tiver acesso de leitura habilitado com armazenamento com redundância geográfica (ra-grs) ou armazenamento com redundância de zona geográfica (ra-GZRS), os destinos de saída para o local secundário serão idênticos aos do local principal. As opções de [replicação do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) é um conjunto de recursos dedicados à análise de Big data, criado no armazenamento de BLOBs do Azure. As limitações do armazenamento de BLOBs e armazenamento do Azure aplicam-se a Data Lake Storage Gen2.

> [!NOTE]
> A Microsoft recomenda que você use uma conta de armazenamento de uso geral v2 para a maioria dos cenários. Você pode atualizar facilmente uma conta de uso geral v1 ou de armazenamento de BLOBs do Azure para uma conta v2 de finalidade geral sem tempo de inatividade e sem a necessidade de copiar dados. Para obter mais informações, consulte [atualizar para uma conta de armazenamento v2 de uso geral](../articles/storage/common/storage-account-upgrade.md).

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Para obter informações sobre o preço do volume, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento são executadas em uma topologia de rede simples, independentemente de quando elas foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, consulte [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
