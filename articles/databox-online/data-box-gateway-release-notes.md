---
title: Notas de lançamento do Azure Data Box Gateway General Availability| Microsoft Docs
description: Descreve problemas e resoluções abertos críticos para o Azure Data Box Gateway em execução da versão geral de disponibilidade.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265396"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notas de lançamento do Gateway de disponibilidade geral da caixa de dados do Azure

## <a name="overview"></a>Visão geral

As notas de versão a seguir identificam os problemas abertos críticos e os problemas resolvidos para a versão GA (General Availability) para o Azure Data Box Edge e o Azure Data Box Gateway.

As notas de versão são continuamente atualizadas e, à medida que são descobertas questões críticas que exijam uma solução alternativa, elas são adicionadas. Antes de implantar o Data Box Edge/Data Box Gateway, revise cuidadosamente as informações contidas nas notas de versão.

A versão GA corresponde às versões do software:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>O que há de novo

- **Novas imagens de disco virtual** - Novos VHDX e VMDK já estão disponíveis no portal Azure. Baixe essas imagens para provisionar, configurar e implantar novos dispositivos GA do Data Box Gateway. Os dispositivos do Data Box Gateway criados em versões prévias anteriores não podem ser atualizados para esta versão. Para saber mais, acesse [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Suporte nfs** - O suporte ao NFS está atualmente em pré-visualização e disponível para clientes v3.0 e v4.1 que acessam os dispositivos Data Box Edge e Data Box Gateway.
- **Resiliência de armazenamento** - O dispositivo Data Box Edge pode suportar a falha de um disco de dados com o recurso de resiliência de armazenamento. Esse recurso está atualmente na visualização. Você pode ativar a resiliência do armazenamento selecionando a opção **Resilient** nas **configurações de armazenamento** na ui da Web local.


## <a name="known-issues-in-ga-release"></a>Problemas conhecidos na versão ga

A tabela a seguir fornece um resumo dos problemas conhecidos para a versão em execução do Data Box Gateway.

| Não. | Recurso | Problema | Solução alternativa/comentários |
| --- | --- | --- | --- |
| **1.** |Tipos de arquivo | Os seguintes tipos de arquivos não são suportados: arquivos de caracteres, arquivos de bloco, soquetes, tubos, links simbólicos.  |A cópia desses arquivos resulta na criação de arquivos de comprimento 0 no compartilhamento NFS. Esses arquivos permanecem no estado de erro e também são relatados no *error.xml*. <br> Links simbólicos para diretórios resultam nos diretórios nunca serem marcados como offline. Como resultado, você não poderá ver a cruz cinza nos diretórios que indica que os diretórios estão offline e todo o conteúdo associado foi completamente carregado no Azure. |
| **2.** |Exclusão | Devido a um bug nesta versão, se um compartilhamento NFS for excluído, o compartilhamento não poderá ser excluído. O status do compartilhamento mostrará *Excluindo*.  |Isso ocorre apenas quando o compartilhamento estiver usando um nome de arquivo sem suporte. |
| **3.** |Cópia | Falha na cópia de dados com Erro: A operação solicitada não pôde ser concluída devido a uma limitação do sistema de arquivos.  |O ADS (Alternate Data Stream, fluxo de dados alternativo) associado ao tamanho do arquivo superior a 128 KB não é suportado.   |


## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Prepare-se para implantar o Azure Data Box Edge](data-box-edge-deploy-prep.md).
