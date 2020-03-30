---
title: Microsoft Threat Modeling Tool lançamento 16/10/2019 - Azure
description: Documentando as notas sobre a versão da Threat Modeling Tool
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552042"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Versão 7.1.61015.1 de atualização do Threat Modeling Tool – 16/10/2019

A versão 7.1.61015.1 da Ferramenta de Modelagem de Ameaças da Microsoft (TMT) foi lançada em 16 de outubro de 2019 e contém as seguintes alterações:

- Aprimoramentos de acessibilidade
- Correções de bug
- Novos estêncil para apps azure logic e azure data explorer

## <a name="notable-bug-fixes"></a>Correções notáveis de bugs

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Compatibilidade retrógrada melhorada com arquivos criados em "Threat Modeling Tool 2016"

Vários bugs relacionados à abertura ou exibição de arquivos de modelo de ameaça criados em "Threat Modeling Tool 2016" foram corrigidos.

## <a name="feature-enhancements"></a>Aprimoramentos de recursos

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Novos estêncil para apps azure logic e azure data explorer

Novos estêncil para a Azure Logic Apps e o Azure Data Explorer foram adicionados ao Azure Stencil, juntamente com suas ameaças e mitigações associadas.

![Azure Logic Apps e Azure Data Explorer Stencils](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados a valores prioritários fora das faixas esperadas

Alguns clientes relataram ter recebido a seguinte mensagem de erro ao abrir arquivos criados na "Ferramenta de Modelagem de Ameaças 2016" ou modelos personalizados:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Esta questão está investigação.

## <a name="system-requirements"></a>Requisitos do sistema

- Sistemas operacionais com suporte
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão necessária do .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
