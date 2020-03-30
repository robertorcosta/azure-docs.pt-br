---
title: Visão geral do Serviço de Diagnósticos Do Azure Kubernetes (AKS)
description: Saiba mais sobre clusters de autodiagnóstico no Azure Kubernetes Service.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: yunjchoi
ms.openlocfilehash: 03bb7b786280dfcbb64190adac51b8d001d59c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126597"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Visão geral do Azure Kubernetes Service Diagnostics (visualização)

Solução de problemas Os problemas de cluster do Azure Kubernetes Service (AKS) são uma parte importante da manutenção do cluster, especialmente se o cluster estiver executando cargas de trabalho essenciais. A AKS Diagnostics é uma experiência inteligente e autodiagnóstico que ajuda você a identificar e resolver problemas em seu cluster. O AKS Diagnostics é nativo da nuvem, e você pode usá-lo sem configuração ou custo de faturamento extra.

Este recurso está agora em visualização pública.

## <a name="open-aks-diagnostics"></a>Abra a KS Diagnósticos

Para acessar a AKS Diagnostics:

- Navegue até o cluster Kubernetes no [portal Azure](https://portal.azure.com).
- Clique em **Diagnosticar e resolver problemas** na navegação esquerda, que abre a AKS Diagnostics.
- Escolha uma categoria que melhor descreva o problema do seu cluster usando as palavras-chave no bloco da página inicial ou digite uma palavra-chave que melhor descreva seu problema na barra de pesquisa, por _exemplo, Problemas_de nó de cluster .

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de clicar em uma categoria, você pode visualizar um relatório de diagnóstico específico para o seu cluster. O relatório de diagnóstico chama de forma inteligente se houver algum problema em seu cluster com ícones de status. Você pode detalhar cada tópico clicando em **Mais Informações** para ver a descrição detalhada do problema, ações recomendadas, links para documentos úteis, métricas relacionadas e dados de registro. Os relatórios de diagnóstico são gerados de forma inteligente com base no estado atual do seu cluster depois de executar uma variedade de verificações. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar o problema do seu cluster e encontrar os próximos passos para resolver o problema.

![Relatório de diagnóstico](./media/concepts-diagnostics/diagnostic-report.png)

![Relatório de diagnóstico expandido](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Cluster Insights

As seguintes verificações de diagnóstico estão disponíveis no **Cluster Insights**.

### <a name="cluster-node-issues"></a>Problemas de nó de cluster

Cluster Node Problemas verifica se há problemas relacionados ao nó que podem fazer com que o cluster se comporte inesperadamente.

- Problemas de prontidão do nó
- Falhas no nó
- Recursos insuficientes
- Configuração ip ausente do nó
- Falhas na CNI de nó
- Nó não encontrado
- Desligar a energia do nó
- Falha de autenticação do nó
- Nó kube-proxy obsoleto

### <a name="create-read-update--delete-operations"></a>Criar, ler, atualizar & excluir operações

Crud Operations verifica se há operações CRUD que possam causar problemas em seu cluster.

- Erro de operação de exclusão de sub-rede em uso
- Erro de operação de exclusão do grupo de segurança da rede
- Erro de operação de exclusão da tabela de rota em uso
- Erro de provisionamento de recursos referenciado
- Erro de operação de exclusão de endereço IP público
- Falha de implantação devido à cota de implantação
- Erro de operação devido à política de organização
- Registro de assinatura ausente
- Erro de provisionamento de extensão vm
- Capacidade de sub-rede
- Cota excedeu erro

### <a name="identity-and-security-management"></a>Gerenciamento de segurança e identidade

O Gerenciamento de Identidade e Segurança detecta erros de autenticação e autorização que podem impedir a comunicação ao seu cluster.

- Falhas na autorização do nó
- 401 erros
- Erros 403

## <a name="next-steps"></a>Próximas etapas

Coletar registros para ajudá-lo a solucionar ainda mais seus problemas de cluster usando [o Periscope AKS](https://aka.ms/aksperiscope).

Publique suas perguntas ou comentários no [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) adicionando "[Diag]" no título.
