---
title: Solução de problemas - Personalizador
description: Este artigo contém respostas para perguntas freqüentes sobre o Personalr.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336026"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas freqüentes sobre o Personalr.

## <a name="transaction-errors"></a>Erros de transação

<details>
<summary><b>Recebo uma resposta HTTP 429 (muitas solicitações) do serviço. O que é que eu posso fazer?</b></summary>

**Resposta**: Se você escolheu um nível de preço gratuito quando criou a instância Personalizador, há um limite de cota no número de solicitações de classificação que são permitidas. Revise sua taxa de chamada de API para a API rank (no painel Métricas no portal Azure para o seu recurso Personalizador) e ajuste o nível de preços (no painel Nível de preços) se o volume de chamada deverá aumentar além do limite para o nível de preço escolhido.

</details>

<details>
<summary><b>Estou recebendo um erro de 5xx em APIs rank ou reward. O que eu devo fazer?</b></summary>

**Resposta**: Essas questões devem ser transparentes. Se eles continuarem, entre em contato com o suporte selecionando **nova solicitação de suporte** na seção Suporte + solução de **problemas,** no portal Azure para o recurso Personalizador.

</details>

## <a name="learning-loop"></a>Ciclo de aprendizado

<details>
<summary>
<b>O ciclo de aprendizagem não atinge 100% de correspondência com o sistema sem o Personalr. Como faço para consertar isso?</b></summary>

**Resposta**: As razões pelas quais você não atinge seu objetivo com o ciclo de aprendizado:
* Não há recursos suficientes enviados com chamada de API rank
* Bugs nos recursos enviados - como o envio de dados de recursos não agregados, como carimbos de tempo para a API de classificação
* Bugs com processamento de loop - como não enviar dados de recompensa para a API de recompensa para eventos

Para corrigir, você precisa alterar o processamento alterando os recursos enviados para o loop ou certificando-se de que a recompensa é uma avaliação correta da qualidade da resposta do Rank.

</details>

<details>
<summary>
<b>O ciclo de aprendizado parece não aprender. Como faço para consertar isso?</b></summary>

**Resposta**: O ciclo de aprendizado precisa de algumas milhares de chamadas de recompensa antes que as chamadas do Rank priorizem efetivamente.

Se você não tiver certeza sobre como seu ciclo de aprendizagem está se comportando atualmente, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida.

</details>

<details>
<summary><b>Eu continuo obtendo resultados de classificação com todas as mesmas probabilidades para todos os itens. Como sei que o Personalr está aprendendo?</b></summary>

**Resposta**: O personalizador retorna as mesmas probabilidades em um resultado de API de classificação quando ele acabou de começar e tem um modelo _vazio,_ ou quando você redefine o Loop personalizador, e seu modelo ainda está dentro do período **de freqüência de atualização do modelo.**

Quando o novo período de atualização começa, o modelo atualizado é usado e você verá as probabilidades mudarem.

</details>

<details>
<summary><b>O ciclo de aprendizado foi aprender, mas parece não aprender mais, e a qualidade dos resultados do Rank não é tão boa. O que eu devo fazer?</b></summary>

**Resposta:**
* Certifique-se de ter concluído e aplicado uma avaliação no portal Azure para que o recurso Personalizer (loop de aprendizagem).
* Certifique-se de que todas as recompensas sejam enviadas, através da API de recompensa, e processadas.

</details>


<details>
<summary><b>Como eu sei que o ciclo de aprendizagem está sendo atualizado regularmente e é usado para marcar meus dados?</b></summary>

**Resposta**: Você pode encontrar a hora em que o modelo foi atualizado pela última vez na página **Modelo e Configurações** de Aprendizagem do portal Azure. Se você vir um carimbo de tempo antigo, é provável porque você não está enviando as chamadas Rank and Reward. Se o serviço não tiver dados de entrada, ele não atualiza o aprendizado. Se você ver que o ciclo de aprendizado não está atualizando com freqüência suficiente, você pode editar a **freqüência**de atualização de modelo do loop .

</details>

## <a name="offline-evaluations"></a>Avaliações offline

<details>
<summary><b>A importância do recurso de uma avaliação offline retorna uma longa lista com centenas ou milhares de itens. O que aconteceu?</b></summary>

**Resposta**: Isso é normalmente devido a carimbos de tempo, IDs de usuário ou alguns outros recursos finos de grãos enviados.

</details>

<details>
<summary><b>Criei uma avaliação offline e ela conseguiu quase instantaneamente. Por que isso? Não vejo nenhum resultado?</b></summary>

**Resposta**: A avaliação offline utiliza os dados do modelo treinado dos eventos nesse período de tempo. Se você não enviou nenhum dado no período de tempo entre o início e o tempo final da avaliação, ele será concluído sem nenhum resultado. Envie uma nova avaliação offline selecionando um intervalo de tempo com eventos que você sabe que foram enviados para o Personalr.

</details>


## <a name="learning-policy"></a>Política de aprendizagem

<details>
<summary><b>Como importar uma política de aprendizagem?</b></summary>

**Resposta**: Saiba mais sobre [conceitos de políticas de aprendizagem](concept-active-learning.md#understand-learning-policy-settings) e como [aplicar](how-to-manage-model.md) uma nova política de aprendizagem. Se você não quiser selecionar uma política de aprendizagem, você pode usar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizado, com base em seus eventos atuais.

</details>

## <a name="security"></a>Segurança

<details>
<summary><b>A chave da API para o meu loop foi comprometida. O que é que eu posso fazer?</b></summary>

**Resposta**: Você pode regenerar uma chave depois de trocar seus clientes para usar a outra chave. Ter duas teclas permite que você propague a chave de forma preguiçosa sem ter que ter qualquer tempo de inatividade. Recomendamos fazer isso em um ciclo regular como medida de segurança.

</details>

## <a name="next-steps"></a>Próximas etapas

[Configure a freqüência de atualização do modelo](how-to-settings.md#model-update-frequency)