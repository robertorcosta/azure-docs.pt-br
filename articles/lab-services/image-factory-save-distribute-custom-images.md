---
title: Salvar e distribuir imagens no Azure DevTest Labs | Microsoft Docs
description: Este artigo oferece as etapas para salvar imagens personalizadas das máquinas virtuais (VMs) já criadas no Azure DevTest Labs.
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
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759424"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Salvar imagens personalizadas e distribuí-las para vários laboratórios
Este artigo oferece as etapas para salvar imagens personalizadas das máquinas virtuais (VMs) já criadas. Ele também abrange como distribuir essas imagens personalizadas para outros DevTest Labs na organização.

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes itens já devem estar no local:

- Um laboratório para a Fábrica de Imagens em Azure DevTest Labs.
- Um Projeto Azure DevOps que é usado para automatizar a fábrica de imagens.
- Localização de código-fonte contendo os scripts e configuração (em nosso exemplo, no mesmo Projeto DevOps mencionado na etapa anterior).
- Criar definição para orquestrar as tarefas do Azure Powershell.

Se necessário, siga as etapas da [Executar uma fábrica de imagens do Azure DevOps](image-factory-set-up-devops-lab.md) para criar ou configurar esses itens. 

## <a name="save-vms-as-generalized-vhds"></a>Salvar VMs como VHDs generalizados
Salve as VMs existentes como VHDs generalizados.  Há um script PowerShell de exemplo para salvar as VMs existentes como VHDs generalizados. Para usá-lo, primeiro, adicione outra tarefa **do Azure Powershell** à definição de compilação, conforme mostrado na imagem a seguir:

![Adicionar o passo Do Azure PowerShell](./media/save-distribute-custom-images/powershell-step.png)

Depois de ter a nova tarefa na lista, selecione o item para que possamos preencher todos os detalhes mostrados na imagem a seguir: 

![Configurações do PowerShell](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Imagens personalizadas generalizadas vs. especializadas
No [portal Azure,](https://portal.azure.com)ao criar uma imagem personalizada de uma máquina virtual, você pode optar por ter uma imagem personalizada generalizada ou especializada.

- **Imagem personalizada especializada:** Sysprep/Deprovision NÃO foi executado na máquina. Isso significa que a imagem é uma cópia exata do disco do SISTEMA OPERACIONAL na máquina virtual existente (um instantâneo).  Os mesmos arquivos, aplicativos, contas de usuário, nome do computador, e assim por diante, estão todos presentes quando criamos uma nova máquina a partir desta imagem personalizada.
- **Imagem personalizada generalizada:** Sysprep/Deprovision foi executado na máquina.  Quando esse processo é executado, ele remove contas de usuário, remove o nome do computador, tira as colmeias de registro do usuário, etc., com o objetivo de generalizar a imagem para que ela possa ser personalizada ao criar outra máquina virtual.  Quando você generaliza uma máquina virtual (executando o sysprep), o processo destrói a máquina virtual atual – ela não será mais funcional.

O script para tirar imagens personalizadas na Fábrica de Imagens salvará VHDs para quaisquer máquinas virtuais criadas na etapa anterior (identificadas com base em uma tag no recurso no Azure).

## <a name="update-configuration-for-distributing-images"></a>Configuração de atualização para distribuição de imagens
O próximo passo no processo é empurrar as imagens personalizadas do laboratório da fábrica de imagens para outros laboratórios que precisem delas. A parte central deste processo é o arquivo de configuração **labs.json.** Você pode encontrar este arquivo na pasta **Configuração** incluída na fábrica de imagens.

Existem duas coisas-chave listadas no arquivo de configuração labs.json:

- Identificando exclusivamente um laboratório de destino específico usando o ID de assinatura e o nome do laboratório.
- O conjunto específico de imagens que devem ser empurradas para o laboratório como caminhos relativos à raiz de configuração. Você pode especificar pasta inteira (para obter todas as imagens nessa pasta) também.

Aqui está um exemplo labs.json arquivo com dois laboratórios listados. Neste caso, você está distribuindo imagens para dois laboratórios diferentes.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Criar uma tarefa de build
Usando os mesmos passos que você já viu anteriormente neste artigo, adicione uma tarefa adicional de compilação **do Azure Powershell** para construir a definição. Preencha os detalhes conforme mostrado na imagem a seguir: 

![Construir tarefa para distribuir imagens](./media/save-distribute-custom-images/second-build-task-powershell.png)

Os parâmetros são:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Esta tarefa pega todas as imagens personalizadas presentes na fábrica de imagens e as empurra para quaisquer laboratórios definidos no arquivo Labs.json.

## <a name="queue-the-build"></a>Enfileirar a compilação
Uma vez que a tarefa de compilação de distribuição esteja concluída, faça fila para garantir que tudo esteja funcionando. Depois que a compilação for concluída com sucesso, as novas imagens personalizadas aparecerão no laboratório de destino que foi inserido no arquivo de configuração labs.json.

## <a name="next-steps"></a>Próximas etapas
No próximo artigo da série, você atualiza a fábrica de imagens com uma política de retenção e etapas de limpeza: [defina a política de retenção e execute scripts de limpeza](image-factory-set-retention-policy-cleanup.md).
