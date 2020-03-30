---
title: Mova uma configuração de manutenção para outra região do Azure
description: Saiba como mover uma configuração de manutenção de VM para outra região do Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304453"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Mova uma configuração de controle de manutenção para outra região

Siga este artigo para mover uma configuração de controle de manutenção para uma região diferente do Azure. Você pode querer mover uma configuração por uma série de razões. Por exemplo, aproveitar uma nova região, implantar recursos ou serviços disponíveis em uma região específica, atender aos requisitos internos de política e governança ou em resposta ao planejamento de capacidade.

O controle de manutenção, com configurações de manutenção personalizadas, permite controlar como as atualizações da plataforma são aplicadas às VMs [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) e [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) e aos Hosts Dedicados do Azure. Existem alguns cenários para mover o controle de manutenção entre as regiões:

- Para mover sua configuração de controle de manutenção, mas não os recursos associados à configuração, siga as instruções deste artigo.
- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga [estas instruções](move-region-maintenance-configuration-resources.md).
- Para mover tanto a configuração de manutenção quanto os recursos associados a ela, primeiro siga as instruções deste artigo. Em seguida, siga [estas instruções.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover uma configuração de controle de manutenção:

- As configurações de manutenção estão associadas a VMs azure ou hosts dedicados do Azure. Certifique-se de que os recursos de VM/host existam na nova região antes de começar.
- Identify: 
    - Configurações de controle de manutenção existentes.
    - Os grupos de recursos nos quais as configurações existentes residem atualmente. 
    - Os grupos de recursos aos quais as configurações serão adicionadas após a mudança para a nova região. 
    - Os recursos associados à configuração de manutenção que você deseja mover.
    - Verifique se os recursos na nova região são os mesmos associados às configurações de manutenção atuais. As configurações podem ter os mesmos nomes na nova região como na antiga, mas isso não é necessário.

## <a name="prepare-and-move"></a>Prepare-se e mova-se 

1. Recupere todas as configurações de manutenção em cada assinatura. Execute o comando de [lista de configuração de manutenção](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) CLI az para fazer isso, substituindo $subId por seu ID de assinatura.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Revise a lista de registros de configuração retornados dentro da assinatura. Veja um exemplo. Sua lista conterá valores para seu ambiente específico.

    **Nome** | **Local** | **Grupo de recursos**
    --- | --- | ---
    Pular manutenção | eastus2 | grupo de recursos de configuração
    IgniçãoDemoConfig | eastus2 | grupo de recursos de configuração
    padrãoManutençãoConfiguração-eastus | eastus | configuração de teste
    

3. Guarde sua lista para referência. À medida que você move as configurações, ele ajuda você a verificar se tudo foi movido.
4. Como referência, mapeie cada configuração/grupo de recursos para o novo grupo de recursos na nova região.
5. Crie novas configurações de manutenção na nova região usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)ou [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associe as configurações com os recursos da nova região, usando [powershell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)ou [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verifique o movimento

Depois de mover as configurações, compare configurações e recursos na nova região com a lista de tabelas que você preparou.


## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Após a mudança, considere excluir as configurações de manutenção movida na região de origem, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)ou [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Próximas etapas

Siga [estas instruções](move-region-maintenance-configuration-resources.md) se você precisar mover recursos associados às configurações de manutenção. 
