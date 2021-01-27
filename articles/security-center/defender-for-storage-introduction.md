---
title: Azure Defender para Armazenamento – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Armazenamento.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 42e8a1f4ff06f6ca6af4afd428008ca174823c5f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916414"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure Defender para Armazenamento


O **Azure Defender para Armazenamento** é uma camada nativa do Azure de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar suas contas de armazenamento. Ele utiliza funcionalidades avançadas da IA de segurança e [Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684) para fornecer recomendações e alertas de segurança contextuais.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à Central de Segurança do Azure e são enviados por email para administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (Disponibilidade Geral)|
|Preço:|O **Azure Defender para Armazenamento** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos:|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Arquivos do Azure](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos|
|||


## <a name="what-are-the-benefits-of-azure-defender-for-storage"></a>Quais são os benefícios do Azure Defender para Armazenamento?

O Azure Defender para Armazenamento oferece:

- **Segurança nativa do Azure** – com a habilitação de um clique, o Defender para Armazenamento protege os dados armazenados no blob do Azure, nos Arquivos do Azure e nos Data Lakes. Como um serviço nativo do Azure, o Defender para Armazenamento proporciona segurança centralizada em todos os ativos de dados gerenciados pelo Azure e é integrado a outros serviços de segurança do Azure, como o Azure Sentinel.
- **Pacote de detecção avançada** – Da plataforma de inteligência contra ameaças da Microsoft, as detecções no Defender para Armazenamento abrangem as principais ameaças de armazenamento, como acesso anônimo, credenciais comprometidas, engenharia social, abuso de privilégio e conteúdo mal-intencionado.
- **Resposta em escala** – As ferramentas de automação da Central de Segurança facilitam a prevenção e a resposta a ameaças identificadas. Saiba mais em [Automatizar respostas para os gatilhos da Central de Segurança](workflow-automation.md).

:::image type="content" source="media/defender-for-storage-introduction/defender-for-storage-high-level-overview.png" alt-text="Visão geral de alto nível dos recursos do Azure Defender para Armazenamento":::


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quais tipos de alertas são fornecidos pelo Azure Defender para Armazenamento?

Os alertas de segurança são disparados quando há:

- **Padrões de acesso suspeitos** – como acesso bem-sucedido de um nó de saída do Tor ou de um IP considerado suspeito pela Inteligência contra Ameaças da Microsoft
- **Atividades suspeitas** – como extração de dados anormais ou alteração incomum de permissões de acesso
- **Upload de conteúdo mal-intencionado** – como arquivos de malware em potencial (com base na análise de reputação de hash) ou hospedagem de conteúdo de phishing

Um alerta inclui detalhes sobre o incidente que o disparou, bem como recomendações sobre como investigar e corrigir as ameaças. Os alertas podem ser exportados para o Azure Sentinel ou qualquer outro SIEM de terceiros ou qualquer outra ferramenta externa.

> [!TIP]
> É uma melhor prática [configurar o Azure Defender para Armazenamento](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center) no nível da assinatura, mas você também pode [configurá-lo em contas de armazenamento individuais](../storage/common/azure-defender-storage-configure.md?tabs=azure-portal).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de hash para malware?

Para determinar se um arquivo carregado é suspeito, o Azure defender para Armazenamento usa a análise de reputação de hash compatível com a [Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não verificam os arquivos carregados. Em vez disso, examinam os logs de armazenamento e comparam os hashes dos arquivos carregados recentemente com aqueles de vírus conhecidos, cavalos de Tróia, spywares e ransomwares. 

Quando há a suspeita de que um arquivo contém malware, a Central de Segurança exibe um alerta e pode, opcionalmente, enviar um email ao proprietário do armazenamento solicitando a aprovação para excluir o arquivo suspeito. Para configurar essa remoção automática de arquivos indicados pela análise de reputação de hash como possíveis portadores de malware, implante uma [automação de fluxo de trabalho para disparar com alertas que contenham "Possível upload de malware em uma conta de armazenamento"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Para habilitar as funcionalidades de proteção contra ameaças da Central de Segurança, você precisará habilitar o Azure Defender na assinatura que contém as cargas de trabalho aplicáveis.
>
> Você pode habilitar o **Azure Defender para Armazenamento** no nível de assinatura ou no nível de recurso.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure Defender para Armazenamento.

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).
- [Como habilitar o Defender Avançado para Armazenamento](../storage/common/azure-defender-storage-configure.md)
- [A lista de alertas do Azure Defender para Armazenamento](alerts-reference.md#alerts-azurestorage)
- [Funcionalidades da Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)