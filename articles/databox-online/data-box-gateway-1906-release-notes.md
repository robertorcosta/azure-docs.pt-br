---
title: Azure Data Box Gateway & notas de lançamento do Azure Data Box Edge 1906| Microsoft Docs
description: Descreve problemas e resoluções abertas críticas para o Gateway da Caixa de Dados Azure e o Azure Data Box Edge em execução em 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099480"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge e Azure Data Box Gateway 1906

As notas de versão a seguir identificam os problemas abertos críticos e os problemas resolvidos para a versão de 1906 para o Azure Data Box Edge e o Azure Data Box Gateway.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o Data Box Edge/Data Box Gateway, revise cuidadosamente as informações contidas nas notas de versão.

Esta versão corresponde às versões do software:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> A atualização 1906 pode ser aplicada apenas aos dispositivos Data Box Edge que estão executando a disponibilidade geral (GA) ou a versão 1905 do software.

## <a name="whats-new"></a>O que há de novo

- **Correção de bugs no fluxo de trabalho de gerenciamento da chave de recuperação** - Na versão anterior, havia um bug devido ao qual a chave de recuperação não estava sendo aplicada. Esse bug foi corrigido nesta versão. Recomendamos fortemente que você aplique esta atualização, pois a chave de recuperação permite que você recupere os dados no dispositivo, caso o dispositivo não seja inicializado. Para obter mais informações, veja como [salvar a chave de recuperação ao implantar o Data Box Edge ou o Data Box Gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Melhorias de registro do Field Programmable Gate Array (FPGA)** - A partir de 1905 foram feitas melhorias de registro, registro e alerta relacionadas ao FPGA. Esta continua a ser uma atualização necessária para o Data Box Edge se você estiver usando o recurso de computação Edge com o FPGA. Para obter mais informações, veja como [transformar dados com o Edge compute no data box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão ga

Nenhum novo problema é divulgado para esta versão. Todos os problemas de lançamento notáveis foram realizados a partir das versões anteriores. Para ver uma lista de problemas conhecidos, vá para [Problemas conhecidos na versão GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Próximas etapas

- [Prepare-se para implantar o Gateway da Caixa de Dados Do Azure](data-box-gateway-deploy-prep.md)
- [Preparar para implantar o Azure Data Box Edge](data-box-edge-deploy-prep.md)
