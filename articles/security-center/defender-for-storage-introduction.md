---
title: Azure Defender para Armazenamento – Benefícios e recursos
description: Saiba mais sobre os benefícios e os recursos do Azure Defender para Armazenamento.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 4677426337a48d4fde74f61b8a4ad6fcb695f420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577808"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure Defender para Armazenamento

**O Azure Defender para Armazenamento** detecta atividades potencialmente prejudiciais nas contas de Armazenamento do Azure. Os dados podem ser protegidos independentemente de estarem armazenados como contêineres de blob, compartilhamentos de arquivos ou data lakes.

Essa camada de proteção permite que você resolva as ameaças *sem* que precise ser um especialista em segurança e ajuda você a gerenciar os sistemas de monitoramento de segurança.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|O **Azure Defender para Armazenamento** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos:|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![Não](./media/icons/no-icon.png) Governo da China e outros governos|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Quais tipos de alertas são fornecidos pelo Azure Defender para Armazenamento?

Os alertas de segurança são disparados quando há:

- **Atividades suspeitas** – Por exemplo, a conta de armazenamento foi acessada com sucesso de um endereço IP que é conhecido como um nó de saída ativo do Tor
- **Comportamento anormal** – Por exemplo, alterações no padrão de acesso para uma conta de armazenamento
- **Possível upload de malware** – Análise de reputação de hash indica que um arquivo carregado contém malware

Um alerta inclui detalhes sobre o incidente que o disparou, bem como recomendações sobre como investigar e corrigir as ameaças.

> [!TIP]
> Você pode simular alertas de armazenamento seguindo as instruções [desta postagem no blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


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
- [Como habilitar o Defender Avançado para Armazenamento](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [A lista de alertas do Azure Defender para Armazenamento](alerts-reference.md#alerts-azurestorage)
- [Funcionalidades da Inteligência contra Ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)