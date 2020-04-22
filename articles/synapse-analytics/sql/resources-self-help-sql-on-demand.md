---
title: Autoajuda do SQL sob demanda (versão prévia)
description: Esta seção contém informações que podem ajudar você a solucionar problemas com o SQL sob demanda (versão prévia).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421190"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Autoajuda do SQL sob demanda (versão prévia)

Este artigo contém informações sobre como solucionar os problemas mais frequentes com o SQL sob demanda (versão prévia) no Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>O SQL sob demanda está esmaecido no Synapse Studio

Se o Synapse Studio não puder estabelecer uma conexão com o SQL sob demanda, você observará que o SQL sob demanda está esmaecido ou mostra o status "Offline". Normalmente, esse problema ocorre quando um dos seguintes casos acontece:

1) A rede impede a comunicação com o back-end do Azure Synapse. O caso mais frequente é o bloqueio da porta 1443. Para fazer com que o SQL sob demanda funcione, desbloqueie essa porta. Outros problemas também podem impedir que o SQL sob demanda funcione; [acesse o guia de solução de problemas completo para obter mais informações](../troubleshoot/troubleshoot-synapse-studio.md).
2) Você não tem permissões para fazer logon no SQL sob demanda. Para obter acesso, um dos administradores do workspace do Azure Synapse deverá adicionar você à função de administrador do workspace ou do SQL. [Acesse o guia completo sobre controle de acesso para obter mais informações](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>A consulta falha porque o arquivo não pode ser aberto

Se a consulta falhar com o erro 'O arquivo não pode ser aberto porque ele não existe ou é usado por outro processo' e você tiver certeza de que o arquivo existe e não é usado por outro processo, o SQL sob demanda não poderá acessar o arquivo. Esse problema geralmente ocorre porque a sua identidade do Azure Active Directory não tem direitos para acessar o arquivo. Por padrão, o SQL sob demanda tenta acessar o arquivo usando a sua identidade do Azure Active Directory. Para resolver esse problema, você precisa ter os direitos adequados para acessar o arquivo. A maneira mais fácil é conceder a função 'Colaborador de Dados de Blob de Armazenamento' a você mesmo na conta de armazenamento que está tentando consultar. [Acesse o guia completo sobre controle de acesso para armazenamento do Azure Active Directory para obter mais informações](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A consulta falha porque não pode ser executada devido às restrições de recursos atuais 

Se a consulta falhar com a mensagem de erro 'Esta consulta não pode ser executada devido às restrições de recurso atuais', o SQL OD não poderá executá-la no momento devido a restrições de recursos: 

- Use tipos de dados de tamanhos razoáveis. Além disso, especifique o esquema de arquivos Parquet para colunas de cadeia de caracteres, pois elas serão VARCHAR(8000) por padrão. 

- Se a consulta for direcionada a arquivos CSV, considere a possibilidade de [criar estatísticas](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Acesse [Melhores práticas de desempenho do SQL sob demanda](best-practices-sql-on-demand.md) para otimizar a consulta.  

## <a name="next-steps"></a>Próximas etapas

Examine os seguintes artigos para saber mais sobre como usar o SQL sob demanda:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)

- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)

- [Consultar arquivos específicos](query-specific-files.md)

- [Consultar arquivos Parquet](query-parquet-files.md)

- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)

- [Consultar arquivos JSON](query-json-files.md)

- [Criar e usar exibições](create-use-views.md)

- [Criar e usar tabelas externas](create-use-external-tables.md)

- [Armazenar os resultados da consulta no armazenamento](create-external-table-as-select.md)
