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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334667"
---
A tabela a seguir descreve os limites padrão para o Azure general-purpose v1, v2, blob storage, block blob storage e Data Lake Storage Gen2 habilitados para contas de armazenamento. O limite *de ingestão* refere-se a todos os dados enviados para uma conta de armazenamento. O limite *de saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas de armazenamento padrão, premium e Data Lake Storage Gen2 habilitadas. <sup>3</sup> | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de recipientes blob, blobs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Máximo de ingestão<sup>1</sup> por conta de armazenamento (EUA, regiões da Europa) | 25 Gbps |
| Máximo de ingestão<sup>1</sup> por conta de armazenamento (regiões diferentes dos EUA e Europa) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento v2 e Blob de uso geral (todas as regiões) | 50 Gbps |
| Egresso máximo para contas de armazenamento v1 de uso geral (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento v1 de uso geral (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtual por conta de armazenamento | 200 |
| Número máximo de regras de endereço IP por conta de armazenamento | 200 |

<sup>1</sup> As contas padrão do Azure Storage suportam limites de capacidade mais altos e limites mais altos para entrada por solicitação. Para solicitar um aumento nos limites de conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se a sua conta de armazenamento tiver acesso à leitura habilitado com armazenamento geo-redundante (RA-GRS) ou armazenamento redundante em geo-zona (RA-GZRS), os alvos de saída para o local secundário são idênticos aos do local principal. As opções [de replicação do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) é um conjunto de recursos dedicados à análise de big data, construído no armazenamento Azure Blob. As limitações de armazenamento e blob do Azure se aplicam ao Data Lake Storage Gen2.

> [!NOTE]
> A Microsoft recomenda que você use uma conta de armazenamento v2 de uso geral para a maioria dos cenários. Você pode facilmente atualizar uma conta de armazenamento V1 ou Azure Blob para uma conta v2 de uso geral sem tempo de inatividade e sem a necessidade de copiar dados. Para obter mais informações, consulte [Atualizar para uma conta de armazenamento v2 de uso geral](../articles/storage/common/storage-account-upgrade.md).

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Para obter informações sobre preços de volume, consulte [preços do Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento são executadas em uma topologia de rede plana, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, consulte [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
