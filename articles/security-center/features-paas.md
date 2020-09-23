---
title: Recursos da central de segurança do Azure para recursos de PaaS do Azure com suporte.
description: Esta página mostra a disponibilidade dos recursos da central de segurança do Azure para os recursos de PaaS do Azure com suporte.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 77bf0f4cd60ba6e85763334a4d864975945ec2c3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894786"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Cobertura de recursos para serviços de PaaS do Azure <a name="paas-services"></a>

A tabela a seguir mostra a disponibilidade dos recursos da central de segurança do Azure para os recursos de PaaS do Azure com suporte.

|Serviço|Recomendações (gratuito)|Alertas de segurança (Azure defender)|Avaliação de vulnerabilidade (Azure defender)|
|:----|:----:|:----:|:----:|
|Serviço de aplicativo do Azure|✔|✔|-|
|Conta de Automação do Azure|✔|-|-|
|Conta do Lote do Azure|✔|-|-|
|Armazenamento do Blobs do Azure|✔|✔|-|
|Cache do Azure para Redis|✔|-|-|
|Serviços de nuvem do Azure|✔|-|-|
|Pesquisa Cognitiva do Azure|✔|-|-|
|Registro de Contêiner do Azure|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Análise Azure Data Lake|✔|-|-|
|Armazenamento do Azure Data Lake|✔|-|-|
|Banco de dados do Azure para MySQL *|✔|✔|-|
|Banco de dados do Azure para PostgreSQL *|✔|✔|-|
|Namespace de hubs de eventos do Azure|✔|-|-|
|Aplicativo Azure Functions|✔|-|-|
|Cofre de Chave do Azure|✔|✔|-|
|Serviço de Kubernetes do Azure|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Aplicativos Lógicos do Azure|✔|-|-|
|Banco de Dados SQL do Azure|✔|✔|✔|
|Instância Gerenciada do Azure SQL|✔|✔|✔|
|Namespace do barramento de serviço do Azure|✔|-|-|
|Conta de Service Fabric do Azure|✔|-|-|
|Contas de armazenamento do Azure|✔|-|-|
|Stream Analytics do Azure|✔|-|-|
|Assinatura do Azure|✔ **|✔|-|
|Rede Virtual do Azure</br> (incluindo sub-redes, NICs e grupos de segurança de rede)|✔|-|-|

\* Atualmente, há suporte para esses recursos na versão prévia.

\*\* As recomendações do Azure Active Directory (AD do Azure) estão disponíveis somente para assinaturas com o Azure defender habilitado.