---
title: incluir arquivo
description: incluir arquivo
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541797"
---
1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com).

1. Atualize seu espaço de trabalho para a edição Enterprise.

    Após a atualização, todos os seus experimentos de interface visual serão convertidos em rascunhos de pipeline no designer.
    
    > [!NOTE]
    > Você não precisa atualizar para a edição Enterprise para converter serviços web de interface visual em pontos finais em tempo real.
    
1. Vá até a seção de designer do espaço de trabalho para ver sua lista de rascunhos de pipeline. 
    
    Os serviços web convertidos podem ser encontrados navegando para **endpoints** > em**tempo real**.

1. Selecione um rascunho de pipeline para abri-lo.

    Se houve um erro durante o processo de conversão, uma mensagem de erro aparecerá com instruções para resolver o problema. 

### <a name="known-issues"></a>Problemas conhecidos

 Abaixo estão conhecidos problemas de migração que precisam ser abordados manualmente:

- **Módulos de dados** de importação ou **de exportação**
        
    Se você tiver um módulo **de dados de importação** ou de **exportação** no experimento, você precisa atualizar a fonte de dados para usar um datastores. Para saber como criar um datastore, consulte [Como acessar dados nos serviços de armazenamento do Azure](../articles/machine-learning/how-to-access-data.md). As informações da sua conta de armazenamento em nuvem foram adicionadas nos comentários do módulo Dados de **Importação** ou **Dados de Exportação** para sua conveniência. 
      