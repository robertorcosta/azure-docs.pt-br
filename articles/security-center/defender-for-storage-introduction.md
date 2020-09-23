---
title: Azure defender para armazenamento-os benefícios e recursos
description: Saiba mais sobre os benefícios e recursos do Azure defender para armazenamento.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 31d1bde1555f98164ccba32d4615ba51837c5ef7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933783"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Introdução ao Azure defender para armazenamento

O **Azure defender para armazenamento** detecta atividade potencialmente prejudicial em suas contas de armazenamento do Azure. Seus dados podem ser protegidos independentemente de serem armazenados como contêineres de BLOB, compartilhamentos de arquivos ou data lagos.

Essa camada de proteção permite que você resolva as ameaças *sem* exigir que você seja um especialista em segurança e ajuda você a gerenciar seus sistemas de monitoramento de segurança.


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Refere|O **Azure defender para armazenamento** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Tipos de armazenamento protegidos|[Armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/)<br>[Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Gov dos EUA<br>![No](./media/icons/no-icon.png) China gov, outros gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Que tipo de alertas é fornecido pelo Azure defender para armazenamento?

Os alertas de segurança são disparados quando há:

- **Atividade suspeita** -por exemplo, a conta de armazenamento foi acessada com êxito de um endereço IP que é conhecido como nó de saída ativo de Tor
- **Comportamento anormal** – por exemplo, alterações no padrão de acesso para uma conta de armazenamento
- **Possível malware carregado** -a análise de reputação de hash indica que um arquivo carregado contém malware

Os alertas incluem detalhes do incidente que os disparou, bem como recomendações sobre como investigar e corrigir ameaças.

> [!TIP]
> Você pode simular alertas de armazenamento seguindo as instruções nesta [postagem no blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).


## <a name="what-is-hash-reputation-analysis-for-malware"></a>O que é a análise de reputação de hash para malware?

Para determinar se um arquivo carregado é suspeito, o Azure defender para armazenamento usa a análise de reputação de hash com suporte da [inteligência de ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684). As ferramentas de proteção contra ameaças não verificam os arquivos carregados, em vez disso examinam os logs de armazenamento e comparam os hashes de arquivos carregados recentemente com aqueles de vírus conhecidos, cavalos de Tróia, spyware e ransomware. 

Quando é suspeito que um arquivo contenha malware, a central de segurança exibe um alerta e pode, opcionalmente, enviar por email o proprietário do armazenamento para aprovação para excluir o arquivo suspeito. Para configurar essa remoção automática de arquivos que a análise de reputação de hash indica conter malware, implante uma [automação de fluxo de trabalho para disparar alertas que contenham "possíveis malwares carregados em uma conta de armazenamento"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).

> [!NOTE]
> Para habilitar os recursos de proteção contra ameaças da central de segurança, você deve habilitar o Azure defender na assinatura que contém as cargas de trabalho aplicáveis.
>
> Você pode habilitar o **Azure defender para armazenamento** no nível de assinatura ou de recurso.



## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o Azure defender para armazenamento.

Para obter material relacionado, consulte os seguintes artigos: 

- Seja um alerta for gerado pela Central de Segurança ou recebido pela Central de Segurança de um produto de segurança diferente, você poderá exportá-lo. Para exportar seus alertas para o Azure Sentinel, qualquer SIEM de terceiros ou qualquer outra ferramenta externa, siga as instruções em [Exportar alertas para um SIEM](continuous-export.md).
- [Como habilitar o defender avançado para armazenamento](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [A lista de alertas do Azure defender para armazenamento](alerts-reference.md#alerts-azurestorage)
- [Recursos de inteligência contra ameaças da Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)