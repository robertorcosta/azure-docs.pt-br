---
title: Comece com o TmaxSoft OpenFrame em Máquinas Virtuais Azure
description: Rehospede suas cargas de trabalho de mainframe ibm z/OS usando o ambiente TmaxSoft OpenFrame em VMs (Azure Virtual Machines).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485307"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Comece com o TmaxSoft OpenFrame no Azure

Pegue seus ativos de mainframe existentes e mova-os para o Microsoft Azure usando o TmaxSoft OpenFrame. Esta solução popular de rehospedagem cria um ambiente de emulação no Azure, permitindo que você migre rapidamente os aplicativos. Não é necessário reformatação.

## <a name="openframe-rehosting-environment"></a>Ambiente de rehospedagem OpenFrame

Configure um ambiente OpenFrame no Azure para desenvolvimento, demonstrações, testes ou cargas de trabalho de produção. Como a figura a seguir mostra, o OpenFrame inclui vários componentes que criam o ambiente de emulação do mainframe no Azure. Por exemplo, os serviços on-line OpenFrame substituem o middleware mainframe, como o IBM Customer Information Control System (CICS). OpenFrame Batch, com seu componente TJES, substitui o Subsistema de Entrada de Emprego (JES) do mainframe da IBM. 

![Processo de rehospedagem openframe](media/openframe-01.png)

> [!NOTE]
> Para executar o ambiente OpenFrame no Azure, você deve ter uma licença de produto válida ou licença de teste da TmaxSoft.

## <a name="openframe-components"></a>Componentes OpenFrame

Os seguintes componentes fazem parte do ambiente OpenFrame no Azure:

- **Ferramentas de migração,** incluindo o OFMiner, uma solução que analisa os ativos dos mainframes e, em seguida, os migra para o Azure.
- **Compiladores**, incluindo ofcobol, um compilador que interpreta os programas COBOL do mainframe; OFPLI, que interpreta os programas PL/I do mainframe; e OFASM, um compilador que interpreta os programas de montagem do mainframe.
- **Componentes front-end,** incluindo Java Enterprise User Solution (JEUS), um servidor de aplicativo web certificado para Java Enterprise Edition 6.OFGW e o componente gateway OpenFrame que fornece um ouvinte 3270.
- **Ambiente de aplicação.** OpenFrame Base é o middleware que gerencia todo o sistema. O OpenFrame Server Type C (OSC) substitui o middleware do mainframe e o IBM CICS.
- **Banco de dados relacional**, como Tibero (mostrado), Oracle Database, Microsoft SQL Server, IBM Db2 ou MySQL. Os aplicativos OpenFrame usam o protocolo ODBC (Open Database Connectivity, conectividade aberta do banco de dados) para se comunicar com o banco de dados.
- **Segurança** via TACF, um módulo de serviço que controla o acesso do usuário a sistemas e recursos. 
- **OFManager** é uma solução que fornece as funções de operação e gerenciamento do OpenFrame no ambiente web.

![Arquitetura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Instale o TmaxSoft OpenFrame no Azure](./install-openframe-azure.md)
