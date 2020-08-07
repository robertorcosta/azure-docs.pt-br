---
title: Exibir status de trabalhos de Importação/Exportação do Azure | Microsoft Docs
description: Saiba como exibir o status dos trabalhos de importação/exportação do Azure e as unidades usadas. Entenda os fatores que afetam o tempo necessário para processar um trabalho.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: bf44b24b9222072bf5f1493b36b0dfcca24b5389
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923604"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Exibir o status de trabalhos de Importação/Exportação do Azure

Este artigo fornece informações sobre como exibir o status da unidade e do trabalho para trabalhos de Importação/Exportação do Azure. O serviço de importação/exportação do Azure é usado para transferir grandes quantidades de dados com segurança para Blobs do Azure e Arquivos do Azure. O serviço também é usado para exportar dados de armazenamento de Blobs do Azure.  

## <a name="view-job-and-drive-status"></a>Exibir o status do trabalho e da unidade
Você pode acompanhar o status dos seus trabalhos de importação ou exportação no Portal do Azure. Clique na guia **importar/exportar** . Uma lista de seus trabalhos é exibida na página.

![Exibir o estado do trabalho](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Exibir status do trabalho

Você verá um dos seguintes status de trabalho, dependendo de onde a unidade está no processo.

| Status do Trabalho | Descrição |
|:--- |:--- |
| Criando | Após a criação de um trabalho, seu estado será definido como **Criando**. Enquanto o trabalho está no estado **Criando**, o serviço de Importação/Exportação considera que as unidades não foram enviadas para o datacenter. Um trabalho pode permanecer neste estado por até duas semanas, depois disso é excluído automaticamente pelo serviço. |
| Remessa | Depois que você enviar seu pacote, atualize as informações de rastreamento no Portal do Azure.  Isso ativa o trabalho no estado **Enviando**. O trabalho permanece no estado **Enviando** por até duas semanas. 
| Recebido | Depois que todas as unidades forem recebidas no data center, o estado do trabalho será definido como **Recebido**. |
| Transferindo | Após pelo menos uma unidade começar o processamento, o estado do trabalho será definido como **Transferindo**. Para obter mais informações, consulte [Estados de unidade](#view-drive-status). |
| Empacotamento | Depois que todas as unidades tiverem concluído o processamento, o trabalho será colocado no estado **Empacotamento** até que as unidades sejam enviadas de volta para você. |
| Concluído | Depois que todas as unidades forem enviadas de volta para você, se o trabalho tiver sido concluído sem erros, ele será definido como **Concluído**. O trabalho será excluído automaticamente após 90 dias no estado **Concluído**. |
| Fechadas | Depois que todas as unidades são enviadas de volta para você, se erros tiverem ocorrido durante o processamento do trabalho, o trabalho será definido como **Fechado**. O trabalho é excluído automaticamente após 90 dias no estado **Fechado**. |

## <a name="view-drive-status"></a>Exibir status da unidade

A tabela a seguir descreve o ciclo de vida de uma unidade individual conforme ela passa por um trabalho de importação ou exportação. O estado atual de cada unidade em um trabalho fica visível no Portal do Azure.

A tabela a seguir descreve cada estado pelo qual cada unidade em um trabalho pode passar.

| Estado da unidade | Descrição |
|:--- |:--- |
| Especificado | Para um trabalho de importação, quando o trabalho é criado no Portal do Azure, o estado inicial de uma unidade é **Especificado**. Para um trabalho de exportação, como nenhuma unidade é especificada quando o trabalho é criado, o estado inicial da unidade é **Recebido**. |
| Recebido | A unidade passa para o estado **Recebido** quando o serviço de Importação/Exportação tiver processado as unidades que foram recebidas da empresa transportadora para um trabalho de importação. Para um trabalho de exportação, o estado inicial da unidade é o estado **Recebido**. |
| Nunca recebido | A unidade passará para o estado **Nunca recebido** quando o pacote de um trabalho chegar, mas não contiver a unidade. Uma unidade também passará para esse estado se duas semanas tiverem se passado desde que o serviço recebeu as informações de envio, mas o pacote ainda não foi entregue no data center. |
| Transferindo | Uma unidade passará para o estado **Transferindo** quando o serviço começar a transferir dados da unidade para o Armazenamento do Microsoft Azure. |
| Concluído | Uma unidade passará para o estado **Concluído** quando o serviço tiver transferido com êxito todos os dados sem erros.
| Concluído mais informações | Uma unidade passará para o estado **Concluído mais informações** quando o serviço encontrar alguns problemas ao copiar dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre a substituição de blobs.
| Enviado de volta | Uma unidade passará para o estado **Enviado de volta** quando for enviada do data center para o endereço de retorno. |

Essa imagem no Portal do Azure exibe o estado da unidade de um trabalho de exemplo:

![Exibir estado da unidade](./media/storage-import-export-service/drivestate.png)

A tabela a seguir descreve os estados de falha de unidade e as ações executadas para cada estado.

| Estado da unidade | Evento | Resolução/Próxima etapa |
|:--- |:--- |:--- |
| Nunca recebido | Uma unidade que está marcada como **Nunca recebido** (porque não foi recebida como parte da remessa do trabalho) chega em outra remessa. | A equipe de operações move a unidade para **Recebido**. |
| N/D | Uma unidade que não é parte de qualquer trabalho chega no data center como parte de outro trabalho. | A unidade será marcada como uma unidade adicional e será retornada a você quando o trabalho associado ao pacote original for concluído. |

## <a name="time-to-process-job"></a>Tempo para processar o trabalho
A quantidade de tempo que leva para processar um trabalho de importação/exportação varia de acordo com uma série de fatores, como:

-  Tempo de envio
-  Carregar no datacenter
-  Tipo de trabalho e o tamanho dos dados sendo copiados
-  Número de discos em um trabalho. 

O serviço de Importação/Exportação não tem um SLA, mas o serviço se esforçará para concluir a cópia no prazo de 7 a 10 dias após o recebimento dos discos. Além do status Postado em portal do Azure, as APIs REST podem ser usadas para acompanhar o progresso do trabalho. O parâmetro de porcentagem concluída na chamada da API da operação [listar trabalhos](/previous-versions/azure/dn529083(v=azure.100)) fornece o progresso da cópia percentual.


## <a name="next-steps"></a>Próximas etapas

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Exemplo de API REST de importação e exportação do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
