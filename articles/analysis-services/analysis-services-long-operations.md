---
title: Melhores práticas para operações de longa duração nos Serviços de Análise do Azure | Microsoft Docs
description: Este artigo descreve as melhores práticas para operações de longa duração.
author: minewiskan
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 3f6b2194cc422a827bbc7a15c012173b3f814b52
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428102"
---
# <a name="best-practices-for-long-running-operations"></a>Melhores práticas para operações de longa duração

Nos Serviços de Análise do Azure, um *nó* representa uma máquina virtual host onde um recurso de servidor está sendo executado. Algumas operações, como consultas de longa duração, operações de atualização e sincronização de saída de escala de consulta podem falhar se um recurso de servidor se mover para um nó diferente. Mensagens de erro comuns neste cenário incluem:

- "Ocorreu um erro ao tentar localizar uma solicitação XMLA de longa duração. A solicitação pode ter sido interrompida por atualização de serviço ou reinicialização do servidor."
- "Job with ID<guid>'<database>for model ' foi cancelado devido a erro de serviço (inatividade) com a mensagem 'Cancelando a solicitação de atualização, uma vez que estava preso sem atualizações. Este é um problema de serviço interno. Por favor, reenvie o trabalho ou faça um boleto para obter ajuda se esse problema acontecer repetidamente."

Há muitas razões pelas quais as operações de longa duração podem ser interrompidas. Por exemplo, atualizações no Azure, tais como: 
- Patches do sistema operacional 
- Atualizações de segurança
- Atualizações do serviço de serviços de análise do Azure
- Atualizações do Service Fabric. Service Fabric é um componente de plataforma usado por uma série de serviços de nuvem da Microsoft, incluindo o Azure Analysis Services.

Além das atualizações que ocorrem no serviço, há uma movimentação natural de serviços entre os latos devido ao balanceamento de carga. Movimentos de nós são parte esperada de um serviço de nuvem. O Azure Analysis Services tenta minimizar os impactos dos movimentos dos nódulos, mas é impossível eliminá-los completamente. 

Além dos movimentos dos nós, há outras falhas que podem ocorrer. Por exemplo, um sistema de banco de dados de origem de dados pode estar offline ou a conectividade de rede é perdida. Se durante a atualização, uma partição tiver 10 milhões de linhas e ocorrer uma falha na 9 milionésima linha, não há como reiniciar a atualização no ponto de falha. O serviço tem que começar de novo desde o início. 

## <a name="refresh-rest-api"></a>Atualizar a API REST

Interrupções de serviço podem ser desafiadoras para operações de longa duração, como a atualização de dados. Os Serviços de Análise do Azure incluem uma API REST para ajudar a mitigar os impactos negativos das interrupções dos serviços. Para saber mais, consulte [Atualização assíncrona com a API REST](analysis-services-async-refresh.md).
 
Além da API REST, existem outras abordagens que você pode usar para minimizar possíveis problemas durante operações de atualização de longo prazo. O objetivo principal é evitar ter que reiniciar a operação de atualização desde o início e, em vez disso, realizar atualizações em lotes menores que podem ser cometidos em etapas. 
 
A API REST permite tal reinicialização, mas não permite flexibilidade total da criação e exclusão de partições. Se um cenário requer operações complexas de gerenciamento de dados, sua solução deve incluir alguma forma de loteamento em sua lógica. Por exemplo, usando transações para processar dados em lotes múltiplos, lotes separados permitem que uma falha não exija reinicialização desde o início, mas sim de um ponto de verificação intermediário. 
 
## <a name="scale-out-query-replicas"></a>Réplicas de consulta de escala

Seja usando REST ou lógica personalizada, as consultas de aplicativos do cliente ainda podem retornar resultados inconsistentes ou intermediários enquanto os lotes estão sendo processados. Se os dados consistentes retornados pelas consultas de aplicativos do cliente forem necessários durante o processamento e os dados do modelo estiverem em um estado intermediário, você poderá usar [a escala](analysis-services-scale-out.md) com réplicas de consulta somente leitura.

Ao usar réplicas de consulta somente leitura, enquanto as atualizações estão sendo realizadas em lotes, os usuários do aplicativo cliente podem continuar consultando o instantâneo antigo de dados nas réplicas somente leitura. Uma vez que as atualizações sejam concluídas, uma operação Synch pode ser executada para atualizar as réplicas somente de leitura.


## <a name="next-steps"></a>Próximas etapas

[Atualização assíncrona com a API REST](analysis-services-async-refresh.md)  
[Escala horizontal do Azure Analysis Services](analysis-services-scale-out.md)  
[Alta disponibilidade do Analysis Services](analysis-services-bcdr.md)  
[Orientação de nova tentativa para os serviços do Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)   

