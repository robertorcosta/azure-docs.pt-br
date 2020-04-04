---
title: Limites de recursos no Azure NetApp Files | Microsoft Docs
description: Descreve limites para os recursos do Azure NetApp Files e como solicitar aumento do limite de recursos.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: ac660b20d519e49e832e979603f763fa672757a5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637392"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limites de recursos no Azure NetApp Files

O reconhecimento dos limites de recursos no Azure NetApp Files ajuda você a gerenciar seus volumes.

## <a name="resource-limits"></a>Limites de recursos

A tabela a seguir descreve os limites de recursos para arquivos do Azure NetApp:

|  Recurso  |  Limite padrão  |  Ajustável via solicitação de suporte  |
|----------------|---------------------|--------------------------------------|
|  Número de contas netapp por região do Azure   |  10    |  Sim   |
|  Número de pools de capacidade por conta NetApp   |    25     |   Sim   |
|  Número de volumes por pool de capacidade     |    500   |    Sim     |
|  Número de instantâneos por volume       |    255     |    Não        |
|  Número de sub-redes delegadas aos Arquivos Do Azure NetApp (Microsoft.NetApp/volumes) por Rede Virtual Azure    |   1   |    Não    |
|  Número de IPs usados em um VNet (incluindo VNets imediatamente peered) com Arquivos Azure NetApp   |    1000   |    Não   |
|  Tamanho mínimo de uma única piscina de capacidade   |  4 TiB     |    Não  |
|  Tamanho máximo de um pool de capacidade única    |  500 TiB   |   Não   |
|  Tamanho mínimo de um único volume    |    100 GiB    |    Não    |
|  Tamanho máximo de um único volume     |    100 TiB    |    Não    |
|  Tamanho máximo de um único arquivo     |    16 TiB    |    Não    |    
|  Tamanho máximo de um único diretório      |    320 MB    |    Não    |    
|  Número máximo de arquivos[(maxfiles)](#maxfiles)por volume     |    100 milhões    |    Sim    |    

Para obter mais informações, consulte [PERGUNTAS FREQUENTES sobre gestão de capacidade](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Limites de maxfiles<a name="maxfiles"></a> 

Os volumes do Azure NetApp Files têm um limite chamado *maxfiles*. O limite de arquivos máximos é o número de arquivos que um volume pode conter. O limite máximo de arquivos para um volume de Arquivos Do Azure NetApp é indexado com base no tamanho (cota) do volume. O limite máximo de arquivos para um volume aumenta ou diminui à taxa de 20 milhões de arquivos por TiB de tamanho de volume provisionado. 

O serviço ajusta dinamicamente o limite de arquivos máximos para um volume com base no seu tamanho provisionado. Por exemplo, um volume configurado inicialmente com um tamanho de 1 TiB teria um limite máximo de arquivos de 20 milhões. Alterações subseqüentes no tamanho do volume resultariam em um reajuste automático do limite de arquivos maxfiles com base nas seguintes regras: 

|    Tamanho do volume (cota)     |  Reajuste automático do limite de arquivos máximos    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milhões     |
|    >= 1 TiB mas < 2 TiB    |    40 milhões     |
|    >= 2 TiB mas < 3 TiB    |    60 milhões     |
|    >= 3 TiB mas < 4 TiB    |    80 milhões     |
|    >= 4 TiB                |    100 milhões    |

Para qualquer tamanho de volume, você pode iniciar uma [solicitação de suporte](#limit_increase) para aumentar o limite máximo de arquivos para além de 100 milhões.

## <a name="request-limit-increase"></a>Aumento do limite de solicitação<a name="limit_increase"></a> 

Você pode criar uma solicitação de suporte do Azure para aumentar os limites ajustáveis da tabela acima. 

Do avião de navegação do portal Azure: 

1. Clique **em Ajuda + suporte**.
2. Clique **em + Nova solicitação de suporte**.
3. Na folha Informações Básicas, forneça as seguintes informações: 
    1. Tipo de emissão: Selecione **limites de serviço e assinatura (cotas)**.
    2. Assinaturas: Selecione a assinatura do recurso que você precisa que a cota seja aumentada.
    3. Tipo de cota: Selecione **Armazenamento: Limites de arquivos do Azure NetApp**.
    4. Clique **em Next: Solutions**.
4. Na guia Detalhes:
    1. Na caixa Descrição, forneça as seguintes informações para o tipo de recurso correspondente:

        |  Recurso  |    Recursos dos pais      |    Novos limites solicitados     |    Motivo para aumento de cotas       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Conta |  *ID de assinatura*   |  *Novo número máximo de **conta** solicitado*    |  *Que cenário ou caso de uso motivou a solicitação?*  |
        |  pool    |  *ID de assinatura, URI da conta*  |  *Novo número máximo **de pool** solicitado*   |  *Que cenário ou caso de uso motivou a solicitação?*  |
        |  Volume  |  *ID de assinatura, URI da conta, POOL URI*   |  *Novo número de **volume** máximo solicitado*     |  *Que cenário ou caso de uso motivou a solicitação?*  |
        |  Maxfiles  |  *ID de assinatura, URI da conta, URI do pool, VOLUME URI*   |  *Solicitado novo número máximo **de arquivos maxfiles***     |  *Que cenário ou caso de uso motivou a solicitação?*  |    

    2. Especifique o método de suporte apropriado e forneça as informações do seu contrato.

    3. Clique **em Next: Review + create** para criar a solicitação. 


## <a name="next-steps"></a>Próximas etapas  

- [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modelo de custo para o Azure NetApp Files](azure-netapp-files-cost-model.md)
