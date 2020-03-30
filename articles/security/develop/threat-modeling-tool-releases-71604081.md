---
title: Microsoft Threat Modeling Tool lançamento 4/9/2019
titleSuffix: Azure
description: Documentando as notas sobre a versão da Threat Modeling Tool
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269723"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Atualização da ferramenta de modelagem de ameaças 7.1.60408.1 - 4/9/2019

A versão 7.1.60408.1 da Ferramenta de Modelagem de Ameaças da Microsoft (TMT) foi lançada em 9 de abril de 2019 e contém as seguintes alterações:

- Novos Stencils para Azure Key Vault e Azure Traffic Manager
- O número da versão TMT agora é mostrado na tela inicial
- Os links de suporte foram atualizados
- Correções de bug

## <a name="feature-changes"></a>Alterações de recurso

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Novos Stencils para Azure Key Vault e Azure Traffic Manager

![Estêncil do cofre da chave azure](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Novos estêncil e ameaças para o Azure Key Vault e o Azure Traffic Manager foram adicionados ao conjunto de estêncil Azure. Ao abrir modelos baseados no conjunto de estêncil Azure, os usuários serão solicitados a atualizar o modelo associado ao modelo. A atualização de um modelo baseado no conjunto de estêncil Azure também pode ser iniciada manualmente usando o comando "Aplicar modelo" no menu "Arquivo" e reaplicando o arquivo Azure Cloud Services.tb7 mais recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>O número da versão TMT agora é mostrado na tela inicial

A versão cliente da Ferramenta de Modelagem de Ameaças agora é mostrada na tela inicial do aplicativo para facilitar o acesso.

![Estêncil do cofre da chave azure](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Os links de suporte foram atualizados

Todos os links de suporte dentro da [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) ferramenta foram atualizados para direcionar os usuários ao invés de um fórum MSDN.

## <a name="system-requirements"></a>Requisitos do sistema

- Sistemas operacionais com suporte
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão necessária do .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais
  - Uma conexão de Internet é necessária para receber as atualizações da ferramenta e também os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](threat-modeling-tool.md) e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
