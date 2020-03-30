---
title: Exceções do Azure Service Bus Resource Manager | Microsoft Docs
description: Lista de exceções de Ônibus de Serviço apareceu pelo Azure Resource Manager e sugestou ações.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 0f328651ac4422226071d2de12e9cbc787ef64be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978266"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções do Service Bus Resource Manager

Este artigo lista as exceções geradas ao interagir com o Azure Service Bus usando o Azure Resource Manager - através de modelos ou chamadas diretas.

> [!IMPORTANT]
> Este documento é frequentemente atualizado. Por favor, verifique se há atualizações.

Abaixo estão as várias exceções/erros que são surgidos através do Azure Resource Manager.

## <a name="error-bad-request"></a>Erro: Má solicitação

"Solicitação ruim" implica que a solicitação recebida pelo Gerenciador de Recursos falhou na validação.

| Código do erro | SubCódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Solicitação incorreta | 40000 | Subcódigo=40000. O *'nome da propriedade'* não pode ser definido ao criar uma fila porque o *namespace 'namespace namespace name'* está usando o 'Basic' Tier. Esta operação só é suportada no nível 'Padrão' ou 'Premium'. | No Azure Service Bus Basic Tier, as propriedades abaixo não podem ser definidas ou atualizadas - <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> HabilitarExpress </li> <li> ForwardTo </li> <li> Tópicos </li> </ul> | Considere atualizar do nível Básico para Padrão ou Premium para usar essa funcionalidade. |
| Solicitação incorreta | 40000 | Subcódigo=40000. O valor para a propriedade 'requiresDuplicateDetection' de uma fila (ou tópico) existente não pode ser alterado. | A detecção duplicada deve ser ativada/desativada no momento da criação da entidade. O parâmetro de configuração de detecção duplicada não pode ser alterado após a criação. | Para habilitar a detecção duplicada em uma fila/tópico criado anteriormente, você pode criar uma nova fila/tópico com detecção duplicada e, em seguida, encaminhar da fila original para a nova fila/tópico. |
| Solicitação incorreta | 40000 | Subcódigo=40000. O valor especificado 16384 é inválido. A propriedade 'MaxSizeInMegabytes', deve ser um dos seguintes valores: 1024;2048;3072;4096;5120. | O valor MaxSizeInMegabytes é inválido. | Certifique-se de que o MaxSizeInMegabytes é um dos seguintes - 1024, 2048, 3072, 4096, 5120. |
| Solicitação incorreta | 40000 | Subcódigo=40000. Particionamento não pode ser alterado para Fila/Tópico. | Particionamento não pode ser alterado para entidade. | Crie uma nova entidade (fila ou tópico) e habilite partições. | 
| Solicitação incorreta | none | O namespace *'namespace name'* não existe. | O namespace não existe dentro da sua assinatura do Azure. | Para resolver este erro, por favor, tente o abaixo <ul> <li> Certifique-se de que a Assinatura Azure está correta. </li> <li> Certifique-se de que o namespace existe. </li> <li> Verifique se o nome do namespace está correto (sem erros de ortografia ou strings nulas). </li> </ul> | 
| Solicitação incorreta | 40400 | Subcódigo=40400. A entidade de destino de encaminhamento automático não existe. | O destino para a entidade de destino de autoencaminhamento não existe. | A entidade de destino (fila ou tópico), deve existir antes que a fonte seja criada. Tente novamente depois de criar a entidade de destino. |
| Solicitação incorreta | 40000 | Subcódigo=40000. O tempo de bloqueio fornecido excede o máximo permitido de '5' minutos. | O tempo para o qual uma mensagem pode ser bloqueada deve ser entre 1 minuto (mínimo) e 5 minutos (máximo). | Certifique-se de que o tempo de bloqueio fornecido é entre 1 min e 5 min. |
| Solicitação incorreta | 40000 | Subcódigo=40000. Tanto a propriedade DelayedPersistence quanto requiresDuplicateDetection não podem ser ativadas em conjunto. | As entidades com detecção duplicada habilitadas nelas devem ser persistentes, de modo que a persistência não pode ser retardada. | Saiba mais sobre [detecção de duplicatas](duplicate-detection.md) |
| Solicitação incorreta | 40000 | Subcódigo=40000. O valor da propriedade RequiresSession de uma fila existente não pode ser alterado. | O apoio às sessões deve ser habilitado no momento da criação da entidade. Uma vez criado, você não pode ativar/desativar sessões em uma entidade existente (fila ou assinatura) | Exclua e recrie uma nova fila (ou assinatura) com a propriedade "RequiresSession" ativada. |
| Solicitação incorreta | 40000 | Subcódigo=40000. 'URI_PATH' contém caracteres que não são permitidos pelo Service Bus. Os segmentos de entidade podem conter apenas letras, números, períodos(.), hífens(-) e sublinhados(_). | Os segmentos de entidade podem conter apenas letras, números, períodos(.), hífens(-) e sublinhados(_). Qualquer outro caractere faz com que a solicitação falhe. | Certifique-se de que não há caracteres inválidos no Caminho URI. |


## <a name="error-code-429"></a>Código de erro: 429

Assim como em HTTP, "Código de erro 429" indica "muitas solicitações". Isso implica que o recurso específico (namespace) está sendo estrangulado por causa de muitas solicitações (ou devido a operações conflitantes) sobre esse recurso.

| Código do erro | SubCódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 5.0004 | Subcódigo=50004. A solicitação foi encerrada porque o namespace *do seu namespace* está sendo estrangulado. | Esta condição de erro é atingida quando o número de solicitações recebidas excede a limitação do recurso. | Espere alguns segundos e tente de novo. <br/> <br/> Saiba mais sobre as [cotas](service-bus-quotas.md) e os [limites de solicitação do Azure Resource Manager](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcódigo=40901. Outra operação conflitante está em andamento. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde que a operação em andamento seja concluída antes de tentar novamente. |
| 429 | 40900 | Subcódigo=40900. Conflito. Você está solicitando uma operação não permitida no estado atual do recurso. | Essa condição pode ser atingida quando várias solicitações são feitas para executar as operações na mesma entidade (fila, tópico, assinatura ou regra) ao mesmo tempo. | Espere alguns segundos e tente novamente |
| 429 | 40901 | Pedido sobre *'nome da entidade'* conflitante com outro pedido | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde que a operação anterior seja concluída antes de tentar novamente |
| 429 | 40901 | Outra solicitação de atualização está em andamento para o *'nome da entidade' da*entidade. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde que a operação anterior seja concluída antes de tentar novamente |
| 429 | none | Conflito de recursos ocorreu. Outra operação conflitante pode estar em andamento. Se esta é uma tentativa de repetição para a operação fracassada, a limpeza de fundo ainda está pendente. Tente novamente depois. | Esta condição pode ser atingida quando houver uma operação pendente contra a mesma entidade. | Aguarde que a operação anterior seja concluída antes de tentar novamente. |


## <a name="error-code-not-found"></a>Código de erro: não encontrado

Esta classe de erros indica que o recurso não foi encontrado.

| Código do erro | SubCódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não encontrado | none | *O "nome da entidade" da entidade* não foi encontrado. | A entidade contra a qual a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |
| Não encontrado | none | Não encontrado. A Operação não existe. | A operação que você está tentando realizar não existe. | Verifique a operação e tente novamente. |
| Não encontrado | none | A solicitação recebida não é reconhecida como uma solicitação de política de namespace. | O órgão de solicitação de entrada é nulo e, portanto, não pode ser executado como um pedido de pedido. | Verifique o órgão de solicitação para garantir que não seja nulo. | 
| Não encontrado | none | O *"nome da entidade" da* entidade de mensagens não pôde ser encontrado. | A entidade contra a a quem você está tentando executar a operação não pôde ser encontrada. | Por favor, verifique se a entidade existe e tente a operação novamente. |

## <a name="error-code-internal-server-error"></a>Código de erro: Erro do servidor interno

Esta classe de erros indica que houve um erro interno do servidor

| Código do erro | SubCódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | 50000 | Subcódigo=50000. Erro interno do servidor| Pode acontecer por várias razões. Alguns dos sintomas são... <ul> <li> A solicitação/corpo do cliente está corrompida e leva a um erro. </li> <li> O cliente solicita tempo de tempo devido a problemas de processamento no serviço. </li> </ul> | Para resolver isso <ul> <li> Certifique-se de que os parâmetros de solicitação não são nulos ou mal formados. </li> <li> Tente novamente a solicitação. </li> </ul> |

## <a name="error-code-unauthorized"></a>Código de erro: Não autorizado

Esta classe de erros indica a ausência de autorização para executar o comando.

| Código do erro | SubCódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não Autorizado | none | Operação inválida no espaço de nome secundário. O espaço de nome secundário é somente leitura. | A operação foi realizada contra o espaço de nome secundário, que é configurado como um espaço de nome somente leitura. | Tente novamente o comando contra o espaço de nome principal. Saiba mais sobre [o espaço de nome secundário](service-bus-geo-dr.md) |
| Não Autorizado | none | MissingToken: O cabeçalho de autorização não foi encontrado. | Esse erro ocorre quando a autorização tem valores nulos ou incorretos. | Certifique-se de que o valor do token mencionado no cabeçalho de autorização está correto e não é nulo. |