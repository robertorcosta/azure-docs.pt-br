---
title: Hub de gerenciamento
description: Gerenciar suas conexões, configuração de controle do código-fonte e propriedades de criação global no Hub de gerenciamento de Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.date: 06/02/2020
ms.openlocfilehash: df5a13b3d7c3fea98cd571a241b9d25e36ffeea6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570623"
---
# <a name="management-hub-in-azure-data-factory"></a>Hub de gerenciamento no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O Hub de gerenciamento, acessado pela guia *gerenciar* no Azure data Factory UX, é um portal que hospeda ações de gerenciamento global para seu data Factory. Aqui, você pode gerenciar suas conexões com armazenamentos de dados e computações externas, configuração de controle do código-fonte e configurações de gatilho.

## <a name="manage-connections"></a>Gerenciar conexões

### <a name="linked-services"></a>Serviços vinculados

Os serviços vinculados definem as informações de conexão para Azure Data Factory se conectar a armazenamentos de dados externos e ambientes de computação. Para obter mais informações, consulte [conceitos de serviços vinculados](concepts-linked-services.md). A criação, edição e exclusão do serviço vinculado são feitas no Hub de gerenciamento.

![Gerenciar serviços vinculados](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Tempos de execução de integração

Um tempo de execução de integração é uma infraestrutura de computação usada pelo Azure Data Factory para fornecer recursos de integração de dados em diferentes ambientes de rede. Para obter mais informações, saiba mais sobre os [conceitos do Integration Runtime](concepts-integration-runtime.md). No Hub de gerenciamento, você pode criar, excluir e monitorar seus tempos de execução de integração.

![Gerenciar tempos de execução de integração](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Gerenciar controle do código-fonte

### <a name="git-configuration"></a>Configuração do git

Exiba e edite suas configurações de repositório git configuradas no Hub de gerenciamento. Para obter mais informações, saiba mais sobre o [controle do código-fonte no Azure data Factory](source-control.md).

![Gerenciar repositório git](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Modelo de parametrização

Para substituir os parâmetros de modelo do Resource Manager gerados ao publicar a partir da ramificação de colaboração, você pode gerar ou editar um arquivo de parâmetros personalizado. Para obter mais informações, saiba como [usar parâmetros personalizados no modelo do Resource Manager](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template). O modelo de parametrização só está disponível ao trabalhar em um repositório git. Se o *arm-template-parameters-definition.jsno* arquivo não existir no Branch de trabalho, a edição do modelo padrão irá gerá-lo.

![Gerenciar parâmetros personalizados](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Gerenciar a criação

### <a name="triggers"></a>Gatilhos

Os gatilhos determinam quando uma execução de pipeline deve ser inicializada. Atualmente, os gatilhos podem estar em uma agenda de relógio de parede, operar em um intervalo periódico ou depender de um evento. Para obter mais informações, saiba mais sobre a [execução do gatilho](concepts-pipeline-execution-triggers.md#trigger-execution). No Hub de gerenciamento, você pode criar, editar, excluir ou exibir o estado atual de um gatilho.

![Captura de tela que mostra onde criar, editar, excluir ou exibir o estado atual de um gatilho.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Parâmetros globais

Os parâmetros globais são constantes em um data factory que podem ser consumidos por um pipeline em qualquer expressão. Para obter mais informações, saiba mais sobre [parâmetros globais](author-global-parameters.md).

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como [configurar um repositório git](source-control.md) para o ADF


