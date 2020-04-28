---
title: Integração com as políticas do Azure
description: Azure Policy é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas que impõem regras sobre seus recursos.
ms.topic: article
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: a160de1277afea026a16f470c8f76cdc2ec1733f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184258"
---
# <a name="integration-with-azure-policy"></a>Integração com o Azure Policy

Azure Policy é um serviço no Azure que você usa para criar, atribuir e gerenciar políticas que impõem regras sobre seus recursos para garantir que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço. Azure Policy avalia seus recursos para não conformidade com as políticas que você atribui. 

O lote do Azure tem duas extensões internas para ajudá-lo a gerenciar a conformidade da política. 

|**Nome**...|   **Descrição**|**Efeito(s)**|  **Versão**|    **Fonte**
|----------------|----------|----------|----------------|---------------|
|Os logs de diagnóstico em contas do Lote devem ser habilitados|   Habilitação da auditoria de logs de diagnóstico. Permite recriar trilhas de atividades a serem usadas para fins de investigação; quando ocorrer um incidente de segurança ou quando sua rede estiver comprometida|AuditIfNotExists, desabilitado|  2.0.0|  GitHub|
|As regras de alerta de métrica devem ser configuradas em contas do Lote| Audite a configuração das regras de alerta de métricas na conta do Lote para habilitar a métrica necessária|   AuditIfNotExists, desabilitado| 1.0.0|  GitHub|

As definições de política descrevem as condições que precisam ser atendidas. Uma condição compara a propriedade de recurso com um valor obrigatório. Os campos de propriedade de recurso são acessados usando aliases predefinidos. Você usa aliases de propriedade para acessar propriedades específicas de um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

Os recursos exigidos pelo lote incluem: conta, nó de computação, pool, trabalho e tarefa. Portanto, você usaria aliases de propriedade para acessar propriedades específicas para esses recursos. Saiba mais sobre [aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Para certificar-se de que você conhece os aliases atuais e examinar seus recursos e políticas, use a extensão Azure Policy para Visual Studio Code. Ele pode ser instalado em todas as plataformas com suporte pelo Visual Studio Code. Esse suporte inclui Windows, Linux e macOS. Consulte as [diretrizes de instalação](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



