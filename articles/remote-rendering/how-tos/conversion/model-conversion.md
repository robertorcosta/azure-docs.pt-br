---
title: Conversão de modelo
description: Descreve o processo de conversão de um modelo para renderização
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681487"
---
# <a name="convert-models"></a>Converter modelos

A renderização remota do Azure permite que você produza modelos muito complexos. Para atingir o máximo desempenho, os dados devem ser pré-processados para estarem em um formato ideal. Dependendo da quantidade de dados, esse passo pode demorar um pouco. Seria impraticável, se esse tempo fosse gasto durante o carregamento do modelo. Além disso, seria um desperdício repetir esse processo por várias sessões. Por essas razões, o serviço ARR fornece um serviço de *conversão*dedicado, que você pode executar com antecedência.
Uma vez convertido, um modelo pode ser carregado a partir de uma conta de armazenamento do Azure.

## <a name="supported-source-formats"></a>Formatos de origem suportados

O serviço de conversão suporta esses formatos:

- **FBX** (versão 2011 e acima)
- **GLTF** (versão 2.x)
- **GLB** (versão 2.x)

Há pequenas diferenças entre os formatos em relação à conversão de propriedade material, conforme listado no [mapeamento do material capítulo para formatos de modelo](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>O processo de conversão

1. [Prepare dois recipientes de armazenamento Azure Blob](blob-storage.md): um para entrada, um para saída
1. Carregue seu modelo para o recipiente de entrada (opcionalmente sob um subcaminho)
1. Desencadear o processo de conversão através [da API REST de conversão do modelo](conversion-rest-api.md)
1. Pesquise o serviço para o progresso da conversão
1. Uma vez terminado, carregue um modelo
    - a partir de uma conta de armazenamento vinculada (consulte as etapas "Contas de armazenamento de link" em [Criar uma conta](../create-an-account.md#link-storage-accounts) para vincular sua conta de armazenamento)
    - ou fornecendo uma *Assinatura de Acesso Compartilhado (SAS)*.

Todos os dados do modelo (entrada e saída) são armazenados no armazenamento blob do Azure fornecido pelo usuário. A renderização remota do Azure lhe dá controle total sobre o gerenciamento de ativos.

## <a name="conversion-parameters"></a>Parâmetros de conversão

Para obter as várias opções de conversão, consulte [este capítulo](configure-model-conversion.md).

## <a name="examples"></a>Exemplos

- [Quickstart: Converter um modelo de renderização](../../quickstarts/convert-model.md) é uma introdução passo a passo de como converter um modelo.
- [Exemplo De scripts PowerShell](../../samples/powershell-example-scripts.md), que demonstram o uso do serviço de conversão, podem ser encontrados no [repositório de amostras ARR](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts.*

## <a name="next-steps"></a>Próximas etapas

- [Use o Armazenamento Azure Blob para conversão de modelos](blob-storage.md)
- [A API de conversão do modelo REST](conversion-rest-api.md)
- [Configuração da conversão do modelo](configure-model-conversion.md)
- [Mapeamento de materiais para formatos de modelos](../../reference/material-mapping.md)
