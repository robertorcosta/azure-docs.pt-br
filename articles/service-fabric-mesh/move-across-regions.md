---
title: Mova um aplicativo de malha de malha de malha de malha de serviço para outra região
description: Você pode mover os recursos da Malha de Malha de Malha de Malha de Malha de Serviço, implantando uma cópia do seu modelo atual para uma nova região do Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908157"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Mova um aplicativo de malha de malha de malha de malha de malha de serviço para outra região do Azure

Este artigo descreve como mover seu aplicativo de malha de malha de malha de malha de malha de serviço e seus recursos para uma região diferente do Azure. Você pode transferir seus recursos para outra região por uma série de razões. Por exemplo, em resposta a paralisações, para obter recursos ou serviços disponíveis apenas em regiões específicas, para atender aos requisitos internos de política e governança, ou em resposta aos requisitos de planejamento de capacidade.

 [A Malha de Malha de Malha de Serviço não suporta](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) a capacidade de mover diretamente recursos em regiões do Azure. No entanto, você pode mover recursos indiretamente implantando uma cópia do modelo atual do Azure Resource Manager para a nova região de destino e, em seguida, redirecionando tráfego e dependências de entrada para o aplicativo de malha de malha de malha de malha de malha de malha de malha de malha de malha de serviço recém-criado.

## <a name="prerequisites"></a>Pré-requisitos

* O controlador ingress (como [o Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)para servir como um intermediário para roteamento de tráfego entre clientes e seu aplicativo de malha de malha de malha de malha de malha de malha de malha de malha de serviço
* Disponibilidade de malha de malha de serviço (Preview) na região azure alvo (`westus`, `eastus`ou `westeurope`)

## <a name="prepare"></a>Preparar

1. Faça um "instantâneo" do estado atual do aplicativo Malha de malha de malha de malha de malha de malha de malha de malha de malha de malha de malha de serviço exportando o modelo e parâmetros do Azure Resource Manager da implantação mais recente. Para isso, siga as etapas do [modelo Exportar após a implantação](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) usando o portal Azure. Você também pode usar [a Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. Se for o caso, [exporte outros recursos no mesmo grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) para reimplantação na região-alvo.

3. Revise (e edite, se necessário) o modelo exportado para garantir que os valores de propriedade existentes sejam os que você deseja usar na região de destino. A `location` nova (região azure) é um parâmetro que você fornecerá durante a reimplantação.

## <a name="move"></a>Mover

1. Crie um novo grupo de recursos (ou use um existente) na região de destino.

2. Com seu modelo exportado, siga as etapas em [Implantar recursos a partir de modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) usando o portal Azure. Você também pode usar [a Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)ou [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Para obter orientação sobre a movimentação de recursos relacionados, como [contas de armazenamento azure,](../storage/common/storage-account-move.md)consulte orientação para serviços individuais listados o tópico [Movimentando recursos do Azure em todas as regiões](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verificar

1. Quando a implantação estiver concluída, teste os pontos finais do aplicativo para verificar a funcionalidade do seu aplicativo.

2. Você também pode verificar o status do seu aplicativo verificando o status do aplicativo[(az mesh app show)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)e revisando os logs de aplicativos e os comandos[(az mesh code-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)usando o [Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Commit

Depois de confirmar a funcionalidade equivalente do aplicativo Malha de malha de malha de malha de serviço na região de destino, configure o controlador de entrada (por exemplo, [O Gateway do Aplicativo)](../application-gateway/redirect-overview.md)para redirecionar o tráfego para o novo aplicativo.

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Para concluir a movimentação do aplicativo Malha de malha de malha de malha de malha de serviço, [exclua o aplicativo de origem e/ou o grupo de recursos pai](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Próximas etapas

* [Mova os recursos do Azure em todas as regiões](../azure-resource-manager/management/move-region.md)
* [Suporte para movimentar recursos do Azure em todas as regiões](../azure-resource-manager/management/region-move-support.md)
* [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mover o suporte de operação para recursos](../azure-resource-manager/management/move-support-resources.md
)
