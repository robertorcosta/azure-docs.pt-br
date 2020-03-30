---
title: Como solucionar problemas com as falhas de tempo de execução do Azure Data Lake Analytics U-SQL
description: Aprenda a solucionar problemas de falhas de tempo de execução do U-SQL.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648448"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Saiba como solucionar problemas de tempo de execução do U-SQL devido a alterações de tempo de execução

O tempo de execução do Azure Data Lake U-SQL, incluindo o compilador, o otimizador e o gerenciador de empregos, é o que processa o seu código U-SQL.

## <a name="choosing-your-u-sql-runtime-version"></a>Escolhendo sua versão em tempo de execução Do U-SQL

Quando você envia trabalhos U-SQL do Visual Studio, do ADL SDK ou do portal Azure Data Lake Analytics, seu trabalho usará o tempo de execução padrão disponível no momento. Novas versões do tempo de execução do U-SQL são lançadas regularmente e incluem atualizações menores e correções de segurança.

Você também pode escolher uma versão de tempo de execução personalizada; ou porque você quer experimentar uma nova atualização, precisa ficar em uma versão mais antiga de um tempo de execução, ou foi fornecido com um hotfix para um problema relatado onde você não pode esperar pela nova atualização regular.

> [!CAUTION]
> Escolher um tempo de execução diferente do padrão tem o potencial de quebrar seus empregos u-SQL. Use essas outras versões apenas para testes.

Em casos raros, o Microsoft Support pode fixar uma versão diferente de um tempo de execução como padrão para sua conta. Por favor, certifique-se de reverter este pino o mais rápido possível. Se você permanecer preso a essa versão, ela expirará em alguma data posterior.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Monitorando seus trabalhos Versão em tempo de execução U-SQL

Você pode ver o histórico de qual versão em tempo de execução seus trabalhos anteriores usaram no histórico de trabalho da sua conta através do navegador de trabalho do Visual Studio ou do histórico de trabalho do portal Azure.

1. No portal do Azure, acesse sua conta do Data Lake Analytics.
2. Selecione **Exibir todos os trabalhos**. Uma lista de todos os trabalhos ativos e recentemente concluídos na conta aparece.
3. Opcionalmente, clique em **Filtrar** para ajudá-lo a localizar os trabalhos por valores de **Intervalo de Tempo**, **Nome do Trabalho** e **Autor**.
4. Você pode ver o tempo de execução usado nos trabalhos concluídos.

![Exibindo a versão em tempo de execução de um trabalho passado](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

As versões de tempo de execução disponíveis mudam com o tempo. O tempo de execução padrão é sempre chamado de "padrão" e mantemos pelo menos o tempo de execução anterior disponível por algum tempo, bem como disponibilizamos tempos de execução especiais por uma variedade de razões. Os tempos de execução explicitamente nomeados geralmente seguem o seguinte formato (itálicos são usados para peças variáveis e [] indica peças opcionais):

release_YYYYMMDD_adl_buildno[_modifier]

Por exemplo, release_20190318_adl_3394512_2 significa que a segunda versão da build 3394512 do lançamento em tempo de execução de 18 de março de 2019 e release_20190318_adl_3394512_private significa uma compilação privada da mesma versão. Nota: A data está relacionada com quando o último check-in foi feito para esse lançamento e não necessariamente a data oficial de lançamento.

A seguir estão as versões em tempo de execução disponíveis no momento.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 o padrão atual
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Solução de problemas Problemas De solucionar problemas de versão em tempo de execução do U-SQL

Existem dois possíveis problemas de versão em tempo de execução que você pode encontrar:

1. Um script ou algum código de usuário está mudando o comportamento de uma versão para outra. Tais alterações de quebra são normalmente comunicadas com antecedência com a publicação de notas de lançamento. Se você encontrar uma mudança tão grande, entre em contato com o Microsoft Support para relatar esse comportamento de quebra (caso ainda não tenha sido documentado) e envie seus trabalhos contra a versão em tempo de execução mais antiga.

2. Você tem usado um tempo de execução não padrão, explicitamente ou implicitamente, quando ele foi fixado em sua conta, e esse tempo de execução foi removido após algum tempo. Se você encontrar tempos de execução perdidos, atualize seus scripts para executar com o tempo de execução padrão atual. Se precisar de tempo adicional, entre em contato com o Suporte da Microsoft

## <a name="see-also"></a>Confira também

- [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md)
- [Gerencie o Azure Data Lake Analytics usando o portal Azure](data-lake-analytics-manage-use-portal.md)
- [Monitoramento no Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
