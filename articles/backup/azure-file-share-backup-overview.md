---
title: Sobre o backup de compartilhamento de arquivos do Azure
description: Saiba como fazer backup das ações de arquivos do Azure no cofre serviços de recuperação
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396276"
---
# <a name="about-azure-file-share-backup"></a>Sobre o backup de compartilhamento de arquivos do Azure

O backup de compartilhamento de arquivos do Azure é uma solução de backup nativa baseada em nuvem que protege seus dados na nuvem e elimina despesas adicionais de manutenção envolvidas em soluções de backup no local. O serviço de backup do Azure integra-se suavemente à sincronização de arquivos do Azure e permite centralizar os dados de compartilhamento de arquivos, bem como seus backups. Esta solução simples, confiável e segura permite configurar a proteção para os compartilhamentos de arquivos corporativos em poucas etapas simples com a garantia de que você pode recuperar seus dados em caso de qualquer cenário de desastre.

## <a name="key-benefits-of-azure-file-share-backup"></a>Principais benefícios do backup de compartilhamento de arquivos do Azure

* Infra-estrutura zero: nenhuma implantação é necessária para configurar a proteção para seus compartilhamentos de arquivos.
* Recursos de gerenciamento incorporados: Você pode agendar backups e especificar o período de retenção desejado sem a sobrecarga adicional de poda de dados.
* Restauração instantânea: o backup do compartilhamento de arquivos do Azure usa instantâneos de compartilhamento de arquivos, para que você possa selecionar apenas os arquivos que deseja restaurar instantaneamente.
* Alerta e emissão de relatórios: Você pode configurar alertas para falhas de backup e restauração e usar a solução de emissão de relatórios fornecida pelo Azure Backup para obter informações sobre backups em seus compartilhamentos de arquivos.

## <a name="architecture"></a>Arquitetura

![Arquitetura de backup de compartilhamento de arquivos do Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

1. O primeiro passo para configurar o backup para compartilhamentos do Azure File é criar um cofre de serviços de recuperação. O cofre oferece uma visão consolidada dos backups configurados em diferentes cargas de trabalho.

2. Depois de criar um cofre, o serviço de backup do Azure descobre as contas de armazenamento que podem ser registradas no cofre. Você pode selecionar a conta de armazenamento que hospeda os compartilhamentos de arquivos que deseja proteger.

3. Depois de selecionar a conta de armazenamento, o serviço de backup do Azure lista o conjunto de compartilhamentos de arquivos presentes na conta de armazenamento e armazena seus nomes no catálogo da camada de gerenciamento.

4. Em seguida, configure a política de backup (agendamento e retenção) de acordo com seus requisitos e selecione as partes de arquivo para fazer backup. O serviço de backup do Azure registra os horários no avião de controle para fazer backups programados.

5. Com base na política especificada, o agendador de backup do Azure aciona backups no horário programado. Como parte desse trabalho, o instantâneo de compartilhamento de arquivos é criado usando a API de compartilhamento de arquivos. Apenas a URL de instantâneo é armazenada no armazenamento de metadados.

    >[!NOTE]
    >Os dados de compartilhamento de arquivos não são transferidos para o serviço de backup, uma vez que o serviço de backup cria e gerencia instantâneos que fazem parte da sua conta de armazenamento.

6. Você pode restaurar o conteúdo de compartilhamento de arquivos do Azure (arquivos individuais ou o compartilhamento completo) a partir de instantâneos disponíveis no compartilhamento de arquivos de origem. Uma vez que a operação é acionada, a URL de instantâneo é recuperada do armazenamento de metadados e os dados são listados e transferidos do snapshot de origem para o compartilhamento de arquivo de destino de sua escolha.

7. Os dados de monitoramento de trabalho de backup e restauração são empurrados para o serviço de monitoramento de backup do Azure. Isso permite monitorar backups na nuvem para seus compartilhamentos de arquivos em um único painel. Além disso, você também pode configurar alertas ou notificações de e-mail quando a saúde de backup é afetada. Os e-mails são enviados através do serviço de e-mail do Azure.

## <a name="backup-costs"></a>Custos de backup

O backup do compartilhamento de arquivos do Azure é uma solução baseada em instantâneos e as taxas de armazenamento incorridas para snapshots são cobradas juntamente com o uso de arquivos do Azure, de acordo com os detalhes de preços mencionados [aqui](https://azure.microsoft.com/pricing/details/storage/files/).

No entanto, a taxa de instância protegida para alavancar a solução de backup está de acordo com o modelo de preços descrito na seção [Backup for Azure Files.](https://azure.microsoft.com/pricing/details/backup/) Atualmente, o preço real foi atualizado apenas para o Centro-Oeste dos EUA. Para outras regiões, os preços exatos serão atualizados em breve com algumas variações regionais, mas utilizando o mesmo modelo de preços.

>[!NOTE]
>Durante a pré-visualização, não há "Taxa de Instância Protegida" e você será cobrado apenas por instantâneos de acordo com os preços mencionados [aqui](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [fazer backup das ações de arquivos do Azure](backup-afs.md)
* Encontre respostas para [perguntas sobre backup de arquivos Do Zure](backup-azure-files-faq.md)
