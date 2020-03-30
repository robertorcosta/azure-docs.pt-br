---
title: Proteção de dados no Azure Stream Analytics
description: Este artigo explica como criptografar seus dados privados usados por um trabalho do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299389"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Proteção de dados no Azure Stream Analytics 

O Azure Stream Analytics é uma plataforma totalmente gerenciada como um serviço que permite que você crie pipelines de análise em tempo real. Todo o levantamento pesado, como provisionamento de clusters, nós de dimensionamento para acomodar seu uso e gerenciamento de pontos de verificação internos, é gerenciado nos bastidores.

## <a name="encrypt-your-data"></a>Criptografe seus dados

O Stream Analytics emprega automaticamente os melhores padrões de criptografia em toda a sua infra-estrutura para criptografar e proteger seus dados. Você pode simplesmente confiar no Stream Analytics para armazenar todos os seus dados com segurança para que você não tenha que se preocupar com o gerenciamento da infra-estrutura.

Se você quiser usar as chaves gerenciadas pelo cliente (CMK) para criptografar seus dados, você pode usar sua própria conta de armazenamento (objetivo geral V1 ou V2) para armazenar quaisquer ativos de dados privados que são exigidos pelo tempo de execução do Stream Analytics. Sua conta de armazenamento pode ser criptografada conforme necessário. Nenhum de seus ativos de dados privados é armazenado permanentemente pela infra-estrutura stream analytics. 

Essa configuração deve ser configurada no momento da criação de empregos do Stream Analytics, e não pode ser modificada ao longo do ciclo de vida do trabalho. A modificação ou exclusão do armazenamento que está sendo usado pelo stream analytics não é recomendada. Se você excluir sua conta de armazenamento, você excluirá permanentemente todos os ativos de dados privados, o que fará com que seu trabalho falhe. 

Atualizar ou girar chaves para sua conta de armazenamento não é possível usando o portal Stream Analytics. Você pode atualizar as chaves usando as APIs REST.


## <a name="configure-storage-account-for-private-data"></a>Configurar a conta de armazenamento para dados privados 

Use as seguintes etapas para configurar sua conta de armazenamento para ativos de dados privados. Essa configuração é feita a partir do seu trabalho do Stream Analytics, não da sua conta de armazenamento.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure. 

1. Selecione **o trabalho** > Analytics **Stream Analytics**na lista de resultados. 

1. Preencha a página de trabalho do Stream Analytics com detalhes necessários, como nome, região e escala. 

1. Selecione a caixa de seleção que diz *Proteger todos os ativos de dados privados necessários por este trabalho na minha conta de armazenamento*.

1. Selecione uma conta de armazenamento da sua assinatura. Observe que esta configuração não pode ser modificada ao longo do ciclo de vida do trabalho. 

   ![Configurações de conta de armazenamento de dados privados](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Ativos de dados privados armazenados

Todos os dados privados necessários para serem persistidos pelo Stream Analytics são armazenados em sua conta de armazenamento. Exemplos de ativos de dados privados incluem: 

* Consultas que você escreveu e suas configurações relacionadas  

* Funções definidas pelo usuário 

* Pontos de verificação necessários pelo tempo de execução do Stream Analytics

* Instantâneos de dados de referência 

Os detalhes de conexão de seus recursos, que são usados pelo seu trabalho do Stream Analytics, também são armazenados. Criptografe sua conta de armazenamento para proteger todos os seus dados. 

Para ajudá-lo a cumprir suas obrigações de conformidade em qualquer setor ou ambiente regulamentado, você pode ler mais sobre [as ofertas de conformidade da Microsoft.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>Próximas etapas

* [Criar uma conta de armazenamento do Azure](../storage/common/storage-account-create.md)
* [Entender as entradas para o Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
