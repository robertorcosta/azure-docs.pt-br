---
title: Criar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Este artigo orienta você pelo processo de criação de um laboratório usando o portal do Azure e Azure DevTest Labs.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 962997bcc66188c66fd9db856fe44e4926f8e70c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019640"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar Laboratórios de Desenvolvimento/Teste do Azure

Um laboratório no Azure DevTest Labs é a infraestrutura que abrange um grupo de recursos, como Máquinas Virtuais (VMs), que permite gerenciar melhor esses recursos especificando limites e cotas. Este artigo explica o processo de criação de um laboratório usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para criar um laboratório, você precisa de:

* Uma assinatura do Azure. Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Você deve ser o proprietário da assinatura para criar o laboratório.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Introdução ao Azure DevTest Labs em minutos

Ao clicar no link a seguir, você será transferido para a página de portal do Azure que permite iniciar a criação de um novo laboratório no Azure DevTest Labs.

[Introdução ao Azure DevTest Labs em minutos](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Preencher as configurações para sua nova conta

Na página **criar um DevTest Labs** , preencha as configurações a seguir.

> [!TIP]
> Na parte inferior de cada página, você encontrará um link que permite **baixar um modelo para automação**.

### <a name="basic-settings"></a>Configurações Básicas

Por padrão, você vê a guia **configurações básicas** . Preencha estes valores:

|Name|Descrição|
|---|---|
|**Assinatura** | Obrigatórios. Selecione a **Assinatura** para associar ao laboratório.|
|**Grupo de recursos**| Obrigatórios. Insira um **nome para o grupo de recursos** do laboratório. Crie um novo se ele não existir.|
|**Nome do laboratório**| Obrigatórios. Insira um **nome** para o laboratório.|
|**Localidade**|Obrigatórios. Selecione um local no qual o laboratório será armazenado.|
|**Ambientes públicos**| Consulte [configurar e usar ambientes públicos](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Configurações de desligamento automático

Alterne para a página de **desligamento automático** para ver suas configurações. O desligamento automático permite que você desligue automaticamente todos os computadores em um laboratório em um horário agendado a cada dia.

Na página, você pode habilitar o **desligamento automático** e definir os parâmetros para o desligamento automático de todas as VMs do laboratório. O recurso de autodesligamento é principalmente um recurso de economia de custos no qual você pode especificar quando deseja que a VM seja desligada automaticamente. Você pode alterar as configurações de autodesligamento após criar o laboratório seguindo as etapas descritas no artigo [Gerenciar todas as políticas de um laboratório no Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Rede

Ao criar um laboratório, uma rede padrão será criada para você.

Alterne para a guia **rede** para alterar/definir a configuração como desejar. Por exemplo, selecione uma rede virtual existente.

### <a name="tags"></a>Marcações

Insira as informações de **NOME** e **VALOR** de **Marcas** se você quiser criar uma marcação personalizada que é adicionada a cada recurso que você criará no laboratório. As marcas são úteis para ajudá-lo a gerenciar e organizar recursos de laboratório por categoria. Para saber mais sobre marcas, incluindo como adicionar marcas depois de criar o ambiente de laboratório, veja [Adicionar marcas a um laboratório](devtest-lab-add-tag.md).

### <a name="review-and-create"></a>Examinar e criar

Depois de terminar, selecione **criar**. Você pode monitorar o status do processo de criação de laboratório observando a área de **notificações** na parte superior direita da página do Portal. 

## <a name="completed-the-creation"></a>A criação foi concluída

Depois de concluído, o botão **ir para recurso** é exibido na parte inferior da página e na janela de notificação. Como alternativa, atualize a página **DevTest Labs** para ver o laboratório recém-criado na lista de laboratórios.  

Pressione **ir para** o botão de recurso e você será levado para a Home Page da sua nova conta do DevTest Labs.

Você também pode procurar **DevTest Labs** na portal do Azure. Selecione sua nova conta na lista e acesse a home page. 

## <a name="next-steps"></a>Próximas etapas

Depois de criar seu laboratório, aqui estão algumas das próximas etapas a serem consideradas:

* [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md)
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md)
* [Criar um modelo de laboratório](devtest-lab-create-template.md)
* [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md)
* [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)

