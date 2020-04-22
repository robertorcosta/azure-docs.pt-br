---
title: Abrir e salvar arquivos com pacotes SSIS implantados no Azure
description: Saiba como abrir e salvar arquivos localmente e no Azure quando você migra para o SSIS no Azure, por lift-and-shift, pacotes SSIS que usam sistemas de arquivos locais
ms.date: 06/27/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 630a8ead409ad60b9146838f062210a3301b43b5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760193"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Abrir e salvar arquivos localmente e no Azure com pacotes SSIS implantados no Azure

Este artigo descreve como abrir e salvar arquivos localmente e no Azure quando você migra para o SSIS no Azure, por lift-and-shift, pacotes SSIS que usam sistemas de arquivos locais.

## <a name="save-temporary-files"></a>Salvar arquivos temporários

Se você precisar armazenar e processar arquivos temporários durante a execução de um único pacote, os pacotes poderão usar o diretório de trabalho atual (`.`) ou a pasta temporária (`%TEMP%`) de seus nós do Azure SSIS Integration Runtime.

## <a name="use-on-premises-file-shares"></a>Usar compartilhamentos de arquivos local

Para continuar a usar os **compartilhamentos de arquivos locais** quando você migrar por lift-and-shift pacotes que usem sistemas de arquivos locais no SSIS no Azure, faça o seguinte:

1. Transfira arquivos de sistemas de arquivos locais para compartilhamentos de arquivos locais.

2. Una os compartilhamentos de arquivos locais a uma Rede Virtual do Azure.

3. Una o Azure-SSIS IR à mesma rede virtual. Para obter mais informações, consulte [unir um runtime de integração do Azure-SSIS a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Conecte o Azure-SSIS IR aos compartilhamentos de arquivos locais dentro da mesma rede virtual, configurando credenciais de acesso que usam a Autenticação do Windows. Para obter mais informações, confira [Conectar-se a dados e a compartilhamentos de arquivos com a Autenticação do Windows](ssis-azure-connect-with-windows-auth.md).

5. Atualize caminhos de arquivos locais em seus pacotes para caminhos UNC apontando para compartilhamentos de arquivos locais. Por exemplo, atualize `C:\abc.txt` para `\\<on-prem-server-name>\<share-name>\abc.txt`.

## <a name="use-azure-file-shares"></a>Usar compartilhamentos de arquivos do Azure

Para usar **Arquivos do Azure** quando você migrar por lift-and-shift pacotes que usem sistemas de arquivos locais no SSIS no Azure, faça o seguinte:

1. Transfira arquivos de sistemas de arquivos locais para Arquivos do Azure. Para obter mais informações, consulte [Arquivos do Azure](https://azure.microsoft.com/services/storage/files/).

2. Conecte o Azure-SSIS IR aos Arquivos do Azure, configurando credenciais de acesso que usam a autenticação do Windows. Para obter mais informações, confira [Conectar-se a dados e a compartilhamentos de arquivos com a Autenticação do Windows](ssis-azure-connect-with-windows-auth.md).

3. Atualize caminhos de arquivos locais em seus pacotes para caminhos UNC apontando para Arquivos do Azure. Por exemplo, atualize `C:\abc.txt` para `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`.

## <a name="next-steps"></a>Próximas etapas

- Implante seus pacotes. Confira mais informações em [Implantar um projeto do SSIS no Azure com o SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Execute os pacotes. Confira mais informações em [Executar pacotes SSIS no Azure com o SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Agende seus pacotes. Para obter mais informações, consulte [Agendar pacotes SSIS no Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
