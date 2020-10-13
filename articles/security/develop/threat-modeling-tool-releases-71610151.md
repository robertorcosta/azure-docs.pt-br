---
title: Versão Microsoft Threat Modeling Tool 10/16/2019 – Azure
description: Documentando as notas de versão da versão 7.1.61015.1 da ferramenta de modelagem de ameaças.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: e45d1347993611130d1bb5036ad4638210c15517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317932"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Versão 7.1.61015.1 de atualização do Threat Modeling Tool – 16/10/2019

A versão 7.1.61015.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em outubro de 16 2019 e contém as seguintes alterações:

- Aprimoramentos na acessibilidade
- Correções de bug
- Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure

## <a name="notable-bug-fixes"></a>Correções de bugs notáveis

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Compatibilidade com versões anteriores aprimorada com arquivos criados em "Threat Modeling Tool 2016"

Vários bugs relacionados à abertura ou exibição de arquivos de modelo de ameaça criados no "Threat Modeling Tool 2016" foram corrigidos.

## <a name="feature-enhancements"></a>Aprimoramentos de recursos

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure

Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure foram adicionados ao estêncil do Azure junto com suas ameaças e mitigações associadas.

![Aplicativos lógicos do Azure e estênceis do Azure Data Explorer](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados a valores de prioridade fora dos intervalos esperados

Alguns clientes relataram o recebimento da seguinte mensagem de erro ao abrir arquivos criados no "Threat Modeling Tool 2016" ou modelos personalizados:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Esse problema está em investigação

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operacionais com suporte
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão necessária do .NET
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
