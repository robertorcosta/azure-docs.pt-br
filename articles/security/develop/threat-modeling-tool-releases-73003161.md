---
title: Microsoft Threat Modeling Tool lançamento 22/03/2020 - Azure
description: Documentando as notas sobre a versão da Threat Modeling Tool
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146856"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Lançamento da ferramenta de modelagem de ameaças 7.3.00316.1 - 22/03/2020

A versão 7.3.00316.1 da Ferramenta de Modelagem de Ameaças da Microsoft (TMT) foi lançada em 22 de março de 2020 e contém as seguintes alterações:

- Aprimoramentos de acessibilidade
- Correções de bug
- Novo recurso DiagramReader

## <a name="notable-bug-fixes"></a>Correções notáveis de bugs

### <a name="exporting-the-threat-list-to-csv"></a>Exportando a lista de ameaças para csv

A função de exportação para CSV foi inconsistentemente selecionando quais campos da lista de ameaças seriam exportados. Agora, todos os campos da lista de ameaças serão exportados para o arquivo CSV. 

### <a name="ux-bugs"></a>Bugs UX

- Os menus de ajuda no fluxo de trabalho principal (criar/abrir/analisar) e a experiência do editor de modelos agora têm opções de menu consistentes.
- A barra de pesquisa no painel de estêncil agora tem um cursor padrão e rótulos apropriados foram adicionados.

## <a name="new-features"></a>Novos recursos

### <a name="diagramreader-feature-has-been-added"></a>O recurso DiagramReader foi adicionado

Um novo recurso DiagramReader foi adicionado no menu principal enquanto um modelo está aberto. Esse recurso converterá a representação gráfica do modelo em uma narrativa baseada em texto. 

## <a name="system-requirements"></a>Requisitos do sistema

- Sistemas operacionais com suporte:
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão .NET necessária:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais:
  - Uma conexão com a internet para receber atualizações da ferramenta, bem como modelos

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação da Threat Modeling Tool está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Próximas etapas

Baixe a versão mais recente da [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
