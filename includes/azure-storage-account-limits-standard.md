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
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79381679"
---
A tabela a seguir descreve os limites padrão das contas de uso geral v1, de uso geral v2, do Armazenamento de Blobs e do Armazenamento de Blob de Blocos do Azure, bem como de contas de armazenamento habilitadas para o Data Lake Storage Gen2. O limite de *entrada* refere-se a todos os dados enviados a uma conta de armazenamento. O limite de *saída* refere-se a todos os dados recebidos de uma conta de armazenamento.

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas Standard, Premium e contas de armazenamento habilitadas para o Data Lake Storage Gen2.<sup>3</sup> | 250 |
| Capacidade máxima da conta de armazenamento | 5 PiB <sup>1</sup>|
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA e Europa) | 25 Gbps |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões fora dos EUA e da Europa) | 5 Gbps se o RA-GRS/GRS estiver habilitado, 10 Gbps para o LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento de Blobs e de uso geral v2 (todas as regiões) | 50 Gbps |
| Saída máxima para contas de armazenamento de uso geral v1 (regiões dos EUA) | 20 Gbps se o RA-GRS/GRS estiver habilitado, 30 Gbps para o LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento de uso geral v1 (regiões fora dos EUA) | 10 Gbps se o RA-GRS/GRS estiver habilitado, 15 Gbps para o LRS/ZRS<sup>2</sup> |
| Número máximo de regras de rede virtual por conta de armazenamento | 200 |
| Número máximo de regras de endereço IP por conta de armazenamento | 200 |

<sup>1</sup> As contas padrão do Armazenamento do Azure dão suporte a limites mais altos de capacidade e de entrada por solicitação. Para solicitar um aumento nos limites de conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Se sua conta de armazenamento tiver acesso de leitura habilitado com RA-GRS (armazenamento com redundância geográfica) ou RA-GZRS (armazenamento com redundância de zona geográfica), os destinos de saída do local secundário serão idênticos aos do local principal. As opções de [replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> O [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) é um conjunto de funcionalidades dedicadas à análise de Big Data, criado sobre o Armazenamento de Blobs do Azure. As limitações do Armazenamento de Blobs e do Armazenamento do Azure aplicam-se ao Data Lake Storage Gen2.

> [!NOTE]
> A Microsoft recomenda o uso de contas de armazenamento de uso geral v2 na maioria dos cenários. É possível atualizar facilmente uma conta do Armazenamento de Blobs do Azure ou de uso geral v1 para uma conta de uso geral v2 sem tempo de inatividade e sem precisar copiar dados. Para obter mais informações, confira [Atualizar para uma conta de armazenamento de uso geral v2](../articles/storage/common/storage-account-upgrade.md).

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Para obter informações sobre o preço por volume, confira [Preço do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento são executadas em uma topologia de rede simples, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, confira [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
