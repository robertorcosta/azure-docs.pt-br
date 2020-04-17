---
title: Guia de onboarding de análise de código de segurança da Microsoft
description: Este artigo descreve a instalação da extensão Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460198"
---
# <a name="onboarding-and-installing"></a>Onboarding e instalação

Pré-requisitos para começar com a Análise de Código de Segurança da Microsoft:

- Uma oferta de suporte unificado da Microsoft elegível, conforme detalhado na seção a seguir.
- Uma organização do Azure DevOps.
- Permissão para instalar extensões na organização Azure DevOps.
- Código-fonte que pode ser sincronizado com um pipeline Azure DevOps hospedado na nuvem.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Onboarding da extensão Microsoft Security Code Analysis

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Interessado em comprar a extensão Microsoft Security Code Analysis?

Se você tiver uma das seguintes ofertas de suporte, entre em contato com o gerente de conta técnica para comprar ou trocar as horas existentes para ter acesso à extensão:

- Nível avançado de suporte unificado
- Nível de desempenho de suporte unificado
- Suporte premier para desenvolvedores
- Suporte Premier para Parceiros
- Suporte Premier para Empresas

Se você não tiver um dos acordos de suporte mencionados acima, você pode comprar a extensão de um de nossos Parceiros.

**Próximos passos:**

Entre em contato com um parceiro da lista abaixo e peça para comprar a extensão microsoft Security Code Analysis.

>**Parceiros:**

- Zonas - Detalhes de contato:cloudsupport@zones.com
- Wortell – Detalhes de contato:info@wortell.nl

### <a name="become-a-partner"></a>Torne-se um parceiro

A equipe de análise de código de segurança da Microsoft está procurando parceiros a bordo com um acordo de Suporte Premier para Parceiros. Os parceiros ajudarão a capacitar os clientes do Azure DevOps a desenvolver de forma mais segura, vendendo a extensão para clientes que desejam comprá-la, mas não têm um acordo de suporte corporativo com a Microsoft. Os parceiros interessados podem se inscrever [aqui.](http://www.microsoftpartnersupport.com/msrd/opin)

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Instalando a extensão microsoft security code analysis

1. Depois que a extensão for compartilhada com sua organização Azure DevOps, vá para a página da organização Azure DevOps. Um exemplo de URL `https://dev.azure.com/contoso`para tal página é .
1. Selecione o ícone do saco de compras no canto superior direito ao lado do seu nome e selecione **Gerenciar extensões**.
1. Selecione **Compartilhado**.
1. Selecione a extensão Microsoft Security Code Analysis, selecione **instalar**.
1. Na lista de paradas, escolha a organização Azure DevOps para instalar a extensão.
1. Selecione **Instalar**. Depois que a instalação estiver concluída, você pode começar a usar a extensão.

>[!NOTE]
> Mesmo que você não tenha acesso para instalar a extensão, continue com as etapas de instalação. Você pode solicitar acesso ao administrador da organização Azure DevOps durante o processo de instalação.

Depois de instalar a extensão, as tarefas de compilação de desenvolvimento seguro são visíveis e disponíveis para adicionar aos seus Pipelines Azure.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Adicionando tarefas específicas de compilação ao seu pipeline Azure DevOps

1. A partir de sua organização Azure DevOps, abra seu projeto de equipe.
1. Selecione **builds** > **de pipelines**.
1. Selecione o pipeline no qual deseja adicionar as tarefas de compilação de extensão:
   - Novo pipeline: Selecione **Novo** e siga as etapas detalhadas para criar um novo pipeline.
   - Editar pipeline: Selecione um pipeline existente e selecione **Editar** para começar a editar o pipeline.
1. Selecione **+** e vá para o painel **Adicionar tarefas.**
1. A partir da lista ou usando a caixa de pesquisa, encontre a tarefa de compilação que deseja adicionar. Selecione **Adicionar**.
1. Especifique os parâmetros necessários para a tarefa.
1. Fila de uma nova compilação.
   >[!NOTE]
   >Os caminhos de arquivo e pasta são relativos à raiz do repositório de origem. Se você especificar os arquivos de saída e pastas como parâmetros, eles serão substituídos pelo local comum que definimos no agente de compilação.

> [!TIP]
>
> - Para executar uma análise após a sua compilação, coloque as tarefas de compilação do Código de Segurança da Microsoft após a etapa Publicar artefatos de compilação da sua compilação. Dessa forma, sua compilação pode terminar e postar resultados antes de executar ferramentas de análise estática.
> - Sempre **selecione Continuar com erro** para tarefas de compilação de desenvolvimento seguro. Mesmo que uma ferramenta falhe, as outras podem ser executadas. Não há interdependências entre as ferramentas.
> - As tarefas de compilação do Microsoft Security Code Analysis falham apenas se uma ferramenta não for executada com sucesso. Mas eles têm sucesso mesmo se uma ferramenta identificar problemas no código. Ao usar a tarefa de compilação pós-análise, você pode configurar sua compilação para falhar quando uma ferramenta identifica problemas no código.
> - Algumas tarefas de compilação do Azure DevOps não são suportadas quando executadas através de um pipeline de liberação. Mais especificamente, o Azure DevOps não suporta tarefas que publicam artefatos de dentro de um pipeline de liberação.
> - Para obter uma lista de variáveis predefinidas no Azure DevOps Team Build que você pode especificar como parâmetros, consulte [Variáveis de compilação do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a configuração das tarefas de [compilação,](security-code-analysis-customize.md) consulte nosso guia de configuração ou [guia de configuração YAML](yaml-configuration.md).

Se você tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
