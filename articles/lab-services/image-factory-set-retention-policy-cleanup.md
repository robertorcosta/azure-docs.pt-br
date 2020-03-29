---
title: Configurar a política de retenção no Azure DevTest Labs | Microsoft Docs
description: Aprenda a configurar uma política de retenção, limpe a fábrica e retire imagens antigas do DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759407"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Configure a política de retenção no Azure DevTest Labs
Este artigo abrange a definição de uma política de retenção, a limpeza da fábrica e a retirada de imagens antigas de todos os outros Laboratórios DevTest da organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que você seguiu estes artigos antes de prosseguir:

- [Criar uma fábrica de imagem](image-factory-create.md)
- [Executar uma fábrica de imagem do Azure DevOps](image-factory-set-up-devops-lab.md)
- [Salve imagens personalizadas e distribua em vários laboratórios](image-factory-save-distribute-custom-images.md)

Os seguintes itens já devem estar no local:

- Um laboratório para a fábrica de imagens em Azure DevTest Labs
- Um ou mais alvos Azure DevTest Labs onde a fábrica distribuirá imagens de ouro
- Um Projeto Azure DevOps usado para automatizar a fábrica de imagens.
- Localização de código-fonte contendo os scripts e configuração (em nosso exemplo, no mesmo Projeto DevOps usado acima)
- Uma definição de construção para orquestrar as tarefas do Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Definindo a política de retenção
Antes de configurar as etapas de limpeza, defina quantas imagens históricas deseja reter no DevTest Labs. Quando você seguiu a executar uma fábrica de imagens do artigo [Azure DevOps,](image-factory-set-up-devops-lab.md) você configurou várias variáveis de compilação. Uma delas era **a Retenção de Imagens.** Você define esta `1`variável como , o que significa que o DevTest Labs não manterá um histórico de imagens personalizadas. Apenas as últimas imagens distribuídas estarão disponíveis. Se você alterar `2`esta variável para , a imagem distribuída mais as anteriores serão mantidas. Você pode definir esse valor para definir o número de imagens históricas que deseja manter em seus Laboratórios DevTest.

## <a name="cleaning-up-the-factory"></a>Limpando a fábrica
O primeiro passo para limpar a fábrica é remover as VMs de imagem douradas da fábrica de imagens. Há um script para fazer essa tarefa, assim como nossos scripts anteriores. O primeiro passo é adicionar outra tarefa **do Azure Powershell** à definição de compilação, conforme mostrado na imagem a seguir:

![Etapa do PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Depois de ter a nova tarefa na lista, selecione o item e preencha todos os detalhes mostrados na imagem a seguir:

![Limpar imagens antigas Tarefa PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros `-DevTestLabName $(devTestLabName)`do script são: .

## <a name="retire-old-images"></a>Retire imagens antigas 
Essa tarefa remove quaisquer imagens antigas, mantendo apenas um histórico correspondente à variável **de compilação ImageRetention.** Adicione uma tarefa adicional de compilação **do Azure Powershell** à nossa definição de compilação. Uma vez adicionado, selecione a tarefa e preencha os detalhes conforme mostrado na imagem a seguir: 

![Retire imagens antigas tarefa PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros do script são:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Enfileirar a compilação
Agora que você completou a definição de compilação, faça fila para garantir que tudo esteja funcionando. Depois que a compilação for concluída com sucesso, as novas imagens personalizadas aparecem no laboratório de destino e se você verificar o laboratório de fábrica de imagens, você não verá VMs provisionados. Além disso, se você fizer fila para construir mais, verá as tarefas de limpeza reaposentando imagens personalizadas antigas dos DevTest Labs de acordo com o valor de retenção definido nas variáveis de compilação.

> [!NOTE]
> Se você executou o pipeline de compilação no final do último artigo da série, exclua manualmente as máquinas virtuais que foram criadas no laboratório de fábrica de imagens antes de enfileirar uma nova compilação.  A etapa de limpeza manual só é necessária enquanto configuramos tudo e verificamos se funciona.



## <a name="summary"></a>Resumo
Agora você tem uma fábrica de imagens em execução que pode gerar e distribuir imagens personalizadas para seus laboratórios demanda. Neste momento, é apenas uma questão de configurar suas imagens corretamente e identificar os laboratórios alvo. Como mencionado no artigo anterior, o arquivo **Labs.json** localizado em sua pasta **Configuração** especifica quais imagens devem ser disponibilizadas em cada um dos laboratórios de destino. À medida que você adiciona outros DevTest Labs à sua organização, você simplesmente precisa adicionar uma entrada no Labs.json para o novo laboratório.

Adicionar uma nova imagem à sua fábrica também é simples. Quando você quiser incluir uma nova imagem em sua fábrica, você abre o [portal Azure,](https://portal.azure.com)navega até o derestatização de fábrica, seleciona o botão para adicionar uma VM e escolhe a imagem e artefatos desejados do mercado. Em vez de selecionar o botão **Criar** para fazer a nova VM, selecione **Exibir modelo do Gerenciador de recursos do Azure**" e salve o modelo como um arquivo .json em algum lugar dentro da pasta **GoldenImages** em seu repositório. Da próxima vez que você executar sua fábrica de imagens, ele criará sua imagem personalizada.


## <a name="next-steps"></a>Próximas etapas
1. [Agende sua compilação/liberação](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) para executar a fábrica de imagens periodicamente. Ele atualiza suas imagens geradas de fábrica regularmente.
2. Faça mais imagens douradas para sua fábrica. Você também pode considerar [a criação de artefatos](devtest-lab-artifact-author.md) para roteirizar peças adicionais de suas tarefas de configuração de VM e incluir os artefatos em suas imagens de fábrica.
4. Crie uma [compilação/versão separada](/azure/devops/pipelines/overview?view=azure-devops-2019) para executar o script **DistributeImages** separadamente. Você pode executar este script quando fizer alterações no Labs.json e obter imagens copiadas para laboratórios de destino sem ter que recriar todas as imagens novamente.

