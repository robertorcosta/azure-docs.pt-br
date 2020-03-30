---
title: Integração com políticas do Azure - Lote Azure | Microsoft Docs
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618367"
---
# <a name="integration-with-azure-policy"></a>Integração com a Política do Azure

O Azure Policy é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas que aplicam regras sobre seus recursos para garantir que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. A Azure Policy avalia seus recursos para não conformidade com as políticas que você atribui. 

O Azure Batch tem duas extensões incorporadas para ajudá-lo a gerenciar a conformidade com a política. 

|**Nome...**|   **Descrição**|**Efeito(s)**|  **Versão**|    **Fonte**
|----------------|----------|----------|----------------|---------------|
|Os logs de diagnóstico em contas do Lote devem ser habilitados|   Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida|AuditIfNotExists, desabilitado|  2.0.0|  GitHub|
|As regras de alerta de métrica devem ser configuradas em contas do Lote| Audite a configuração das regras de alerta de métricas na conta do Lote para habilitar a métrica necessária|   AuditIfNotExists, desabilitado| 1.0.0|  GitHub|

As definições de políticas descrevem as condições que precisam ser atendidas. Uma condição compara a propriedade do recurso com um valor necessário. Os campos de propriedade de recursos são acessados usando aliases pré-definidas. Você usa pseudônimos de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

Os recursos exigidos pelo Batch incluem: conta, nó de computação, pool, trabalho e tarefa. Assim, você usaria pseudônimos de propriedade para acessar propriedades específicas para esses recursos. Saiba mais sobre [aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Para garantir que você conheça os alias atuais e revise seus recursos e políticas, use a extensão de política do Azure para o Visual Studio Code. Ele pode ser instalado em todas as plataformas que são suportadas pelo Visual Studio Code. Esse suporte inclui Windows, Linux e macOS. Consulte [as diretrizes de instalação](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



