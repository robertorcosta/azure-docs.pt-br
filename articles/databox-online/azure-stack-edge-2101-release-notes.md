---
title: Notas de versão do Azure Stack Edge pro com FPGA 2101 | Microsoft Docs
description: Descreve problemas e resoluções críticos em aberto para o Azure Stack versão 2101 do Edge.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 025694dc020bb18ce66574bac476f34034353721
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072536"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Notas de versão do Azure Stack Edge pro com FPGA 2101

As notas de versão a seguir identificam os problemas críticos abertos e os problemas resolvidos para a versão 2101 do Azure Stack Edge pro com com uma FPGA (matriz de porta programável) de campo interno.

As notas de versão são atualizadas continuamente. Como problemas críticos que exigem uma solução alternativa são descobertos, eles são adicionados. Antes de implantar o dispositivo Azure Stack Edge, revise cuidadosamente as informações nas notas de versão.  

Esta versão corresponde à versão do software:

- **Azure Stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> A atualização 2101 pode ser aplicada somente a dispositivos que executam versões de GA (disponibilidade geral) do software ou posterior.

## <a name="whats-new"></a>Novidades

Esta versão contém a correção de bug a seguir:

- **Problema de upload** – esta versão corrige um problema de carregamento, onde as reinicializações de carregamento causadas por uma falha podem diminuir a taxa de conclusão do carregamento. Esse problema pode ocorrer ao carregar um conjunto de um DataSet que consiste principalmente em arquivos que são grandes em relação à largura de banda disponível, particularmente, mas não se limita ao, quando a limitação da largura de banda está ativa. Essa alteração garante uma oportunidade suficiente para o carregamento da conclusão antes de reiniciar o carregamento para um determinado arquivo.

Esta versão também contém as seguintes atualizações:

- Todas as atualizações cumulativas do Windows e do .NET Framework lançadas até outubro de 2020.
- A versão do firmware do controlador BMC (Baseboard Management Controller) é atualizada de 3.32.32.32 para 3.36.36.36 durante a instalação de fábrica para resolver a incompatibilidade com as unidades mais recentes da fonte de energia da Dell.
- O endereço IP estático para Gateway do Azure Data Box é mantido entre as atualizações de software.
- Esta versão dá suporte a IoT Edge 1.0.9.3 em dispositivos Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão

Não foram observados novos problemas para esta versão. Toda a versão observou que os problemas foram transferidos das versões anteriores. Para ver uma lista de problemas conhecidos, vá para [problemas conhecidos na versão GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Azure Stack borda](../databox-online/azure-stack-edge-deploy-prep.md)
