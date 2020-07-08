---
title: Proteção de dados no Azure Stream Analytics
description: Este artigo explica como criptografar seus dados privados usados por um trabalho de Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/05/2020
ms.openlocfilehash: 637ac97d1e054599ec297344ff0c5fff600c8487
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045341"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Proteção de dados no Azure Stream Analytics 

Azure Stream Analytics é uma plataforma como um serviço totalmente gerenciada que permite criar pipelines de análise em tempo real. Todo o trabalho pesado, como provisionamento de cluster, dimensionamento de nós para acomodar seu uso e gerenciamento de pontos de verificação internos, é gerenciado em segundo plano.

## <a name="encrypt-your-data"></a>Criptografar seus dados

Stream Analytics emprega automaticamente os melhores padrões de criptografia em toda a sua infraestrutura para criptografar e proteger seus dados. Você pode simplesmente confiar Stream Analytics para armazenar com segurança todos os seus dados para que você não precise se preocupar com o gerenciamento da infraestrutura.

Se você quiser usar chaves gerenciadas pelo cliente (CMK) para criptografar seus dados, poderá usar sua própria conta de armazenamento (uso geral v1 ou v2) para armazenar quaisquer ativos de dados privados que são necessários para o tempo de execução de Stream Analytics. Sua conta de armazenamento pode ser criptografada conforme necessário. Nenhum dos seus ativos de dados privados são armazenados permanentemente pela infraestrutura de Stream Analytics. 

Essa configuração deve ser definida no momento da criação do trabalho de Stream Analytics e não pode ser modificada durante o ciclo de vida do trabalho. A modificação ou exclusão de armazenamento que está sendo usada pelo seu Stream Analytics não é recomendada. Se você excluir sua conta de armazenamento, excluirá permanentemente todos os ativos de dados privados, o que causará falha no trabalho. 

Não é possível atualizar ou alternar chaves para sua conta de armazenamento usando o portal de Stream Analytics. Você pode atualizar as chaves usando as APIs REST.


## <a name="configure-storage-account-for-private-data"></a>Configurar a conta de armazenamento para dados privados 

Use as etapas a seguir para configurar sua conta de armazenamento para ativos de dados privados. Essa configuração é feita de seu trabalho de Stream Analytics, não da sua conta de armazenamento.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure. 

1. Selecione **análise**   >  **Stream Analytics trabalho**   na lista de resultados. 

1. Preencha a página Stream Analytics trabalho com os detalhes necessários, como nome, região e escala. 

1. Marque a caixa de seleção que diz *proteger todos os ativos de dados privados necessários para esse trabalho em minha conta de armazenamento*.

1. Selecione uma conta de armazenamento da sua assinatura. Observe que essa configuração não pode ser modificada durante o ciclo de vida do trabalho. 

   ![Configurações de conta de armazenamento de dados particulares](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Ativos de dados privados que são armazenados

Todos os dados privados que precisam ser persistidos pelo Stream Analytics são armazenados em sua conta de armazenamento. Exemplos de ativos de dados privados incluem: 

* Consultas que você criou e suas configurações relacionadas  

* Funções definidas pelo usuário 

* Pontos de verificação necessários para o tempo de execução de Stream Analytics

* Instantâneos de dados de referência 

Os detalhes de conexão de seus recursos, que são usados pelo seu trabalho de Stream Analytics, também são armazenados. Criptografe sua conta de armazenamento para proteger todos os seus dados. 

Para ajudá-lo a atender suas obrigações de conformidade em qualquer ambiente ou setor regulamentado, você pode ler mais sobre as [ofertas de conformidade da Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="known-issues"></a>Problemas conhecidos
Há um problema conhecido em que um trabalho que usa a chave gerenciada pelo cliente é executado em falhas ao usar a identidade gerenciada para se autenticar em qualquer entrada ou saída. Uma correção para esse problema está sendo realizada e ficará disponível em breve. 

## <a name="next-steps"></a>Próximas etapas

* [Crie uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md)
* [Entender as entradas para o Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
