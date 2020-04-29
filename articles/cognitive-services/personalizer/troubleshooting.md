---
title: Solução de problemas-personalizador
description: Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80336026"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.

## <a name="transaction-errors"></a>Erros de transação

<details>
<summary><b>Recebo uma resposta HTTP 429 (número excessivo de solicitações) do serviço. O que posso fazer?</b></summary>

**Resposta**: se você escolheu uma camada de preço livre quando criou a instância de personalizador, há um limite de cota no número de solicitações de classificação permitidas. Examine sua taxa de chamada de API para a API de classificação (no painel métricas no portal do Azure para o recurso personalizado) e ajuste o tipo de preço (no painel tipo de preço) se o volume de chamada deve aumentar além do limite para o tipo de preço escolhido.

</details>

<details>
<summary><b>Estou recebendo um erro 5xx em APIs de classificação ou recompensa. O que devo fazer?</b></summary>

**Resposta**: esses problemas devem ser transparentes. Se eles continuarem, entre em contato com o suporte selecionando **nova solicitação de suporte** na seção **suporte + solução de problemas** , na portal do Azure para o recurso personalizador.

</details>

## <a name="learning-loop"></a>Loop de aprendizagem

<details>
<summary>
<b>O loop de aprendizagem não atinge uma correspondência de 100% para o sistema sem personalizar. Como fazer corrigir isso?</b></summary>

**Resposta**: os motivos pelos quais você não atinge seu objetivo com o loop de aprendizagem:
* Não há recursos suficientes enviados com a chamada à API de classificação
* Bugs nos recursos enviados, como o envio de dados de recursos não agregados, como carimbos de data/hora para a API de classificação
* Bugs com processamento de loop-como não enviar dados de recompensa para recompensar a API para eventos

Para corrigir, você precisa alterar o processamento alterando os recursos enviados para o loop ou certificar-se de que a recompensa é uma avaliação correta da qualidade da resposta da classificação.

</details>

<details>
<summary>
<b>O loop de aprendizagem parece não aprender. Como fazer corrigir isso?</b></summary>

**Resposta**: o loop de aprendizagem precisa de alguns mil pontos de recompensa para que as chamadas de classificação sejam priorizadas com eficiência.

Se você não tiver certeza sobre como seu loop de aprendizado está sendo usado no momento, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida.

</details>

<details>
<summary><b>Continuo obtendo resultados de classificação com todas as mesmas probabilidades para todos os itens. Como fazer sabe que o personalizador está aprendendo?</b></summary>

**Resposta**: o personalizador retorna as mesmas probabilidades em um resultado de API de classificação quando ele acabou de ser iniciado e tem um modelo _vazio_ , ou quando você redefine o loop personalizador, e seu modelo ainda está dentro do período de **frequência de atualização do modelo** .

Quando o novo período de atualização começar, o modelo atualizado será usado e você verá as probabilidades de alteração.

</details>

<details>
<summary><b>O loop de aprendizagem estava aprendendo, mas parece não saber mais, e a qualidade dos resultados da classificação não é boa. O que devo fazer?</b></summary>

**Resposta**:
* Verifique se você concluiu e aplicou uma avaliação no portal do Azure para esse recurso personalizado (loop de aprendizagem).
* Verifique se todas as recompensas foram enviadas, por meio da API de recompensa e processadas.

</details>


<details>
<summary><b>Como fazer saber que o loop de aprendizagem está sendo atualizado regularmente e é usado para pontuar meus dados?</b></summary>

**Resposta**: você pode encontrar a hora em que o modelo foi atualizado pela última vez na página de **configurações modelo e aprendizado** da portal do Azure. Se você vir um carimbo de data/hora antigo, é provável que você não esteja enviando as chamadas de classificação e recompensa. Se o serviço não tiver dados de entrada, ele não atualizará o aprendizado. Se você vir que o loop de aprendizagem não está atualizando com frequência suficiente, você poderá editar a **frequência de atualização do modelo**do loop.

</details>

## <a name="offline-evaluations"></a>Avaliações offline

<details>
<summary><b>A importância de um recurso de uma avaliação offline retorna uma longa lista com centenas ou milhares de itens. O que aconteceu?</b></summary>

**Resposta**: isso normalmente ocorre devido a carimbos de data/hora, IDs de usuário ou alguns outros recursos refinados enviados no.

</details>

<details>
<summary><b>Criei uma avaliação offline e ela foi bem-sucedida quase instantaneamente. Por quê? Não vejo nenhum resultado?</b></summary>

**Resposta**: a avaliação offline usa os dados de modelo treinados dos eventos nesse período de tempo. Se você não enviou dados no período de tempo entre a hora de início e de término da avaliação, ele será concluído sem nenhum resultado. Envie uma nova avaliação offline selecionando um intervalo de tempo com eventos que você conhece que foram enviados ao personalizador.

</details>


## <a name="learning-policy"></a>Política de aprendizagem

<details>
<summary><b>Como fazer importar uma política de aprendizado?</b></summary>

**Resposta**: Saiba mais sobre os [conceitos de política de aprendizado](concept-active-learning.md#understand-learning-policy-settings) e [como aplicar](how-to-manage-model.md) uma nova política de aprendizado. Se você não quiser selecionar uma política de aprendizado, poderá usar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizado com base em seus eventos atuais.

</details>

## <a name="security"></a>Segurança

<details>
<summary><b>A chave de API para meu loop foi comprometida. O que posso fazer?</b></summary>

**Resposta**: você pode regenerar uma chave depois de trocar seus clientes para usar a outra chave. Ter duas chaves permite propagar a chave de maneira lenta sem ter que ter qualquer tempo de inatividade. É recomendável fazer isso em um ciclo regular como uma medida de segurança.

</details>

## <a name="next-steps"></a>Próximas etapas

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)