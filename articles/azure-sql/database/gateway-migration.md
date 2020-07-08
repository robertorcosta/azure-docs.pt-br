---
title: Aviso de migração de tráfego de gateway
description: O artigo fornece um aviso aos usuários sobre a migração de endereços IP do gateway do banco de dados SQL do Azure
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: e9bf1f06b1ec1f99da1ce653b4bc72f4638ba451
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86084946"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego do banco de dados SQL do Azure para gateways mais recentes
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

À medida que a infraestrutura do Azure melhora, a Microsoft atualizará periodicamente o hardware para garantir que forneceremos a melhor experiência possível para o cliente. Nos próximos meses, planejamos adicionar gateways criados em gerações de hardware mais recentes, migrar o tráfego para eles e eventualmente descomissionar os gateways criados em um hardware mais antigo em algumas regiões.  

Os clientes serão notificados por email e na portal do Azure bem antes de qualquer alteração nos gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela [endereços IP do gateway do banco de dados SQL do Azure](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Atualizações de status

# <a name="in-progress"></a>[Em Andamento](#tab/in-progress-ip)

### <a name="august-2020"></a>Agosto de 2020

Novos gateways do SQL estão sendo adicionados às seguintes regiões:

- Leste da Austrália: 13.70.112.9
- Centro do Canadá: 52.246.152.0, 20.38.144.1 
- Oeste dos EUA 2:40.78.240.8

Esses gateways do SQL devem começar a aceitar o tráfego do cliente em 10 de agosto de 2020. 

# <a name="completed"></a>[Concluído](#tab/completed-ip)

As seguintes migrações de gateway estão concluídas: 

### <a name="october-2019"></a>Outubro de 2019
- Sul do Brasil
- Oeste dos EUA
- Europa Ocidental
- Leste dos EUA
- Centro dos EUA
- Sudeste da Ásia
- Centro-Sul dos Estados Unidos
- Norte da Europa
- Centro-Norte dos EUA
- Oeste do Japão
- Leste do Japão
- Leste dos EUA 2
- Leste da Ásia

---

## <a name="impact-of-this-change"></a>Impacto dessa alteração

A migração de tráfego pode alterar o endereço IP público que o DNS resolve para seu banco de dados no banco de dados SQL do Azure.
Você poderá ser afetado se:

- O endereço IP embutido em código para qualquer gateway específico em seu firewall local
- Ter qualquer sub-rede usando Microsoft. SQL como um ponto de extremidade de serviço, mas não pode se comunicar com os endereços IP do gateway
- Usar a [configuração com redundância de zona](high-availability-sla.md#zone-redundant-configuration) para seu banco de dados

Você não será afetado se tiver:

- Redirecionamento como a política de conexão
- Conexões com o banco de dados SQL de dentro do Azure e usando marcas de serviço
- As conexões feitas usando as versões com suporte do driver JDBC para SQL Server não terão impacto. Para obter suporte para versões JDBC, consulte [baixar o Microsoft JDBC Driver para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se você for afetado

Recomendamos que você permita o tráfego de saída para endereços IP para todos os [endereços IP de gateway](connectivity-architecture.md#gateway-ip-addresses) na região na porta TCP 1433 e o intervalo de porta 11000-11999. Essa recomendação é aplicável aos clientes que se conectam do local e também aos que se conectam por meio de pontos de extremidade de serviço. Para obter mais informações sobre intervalos de portas, consulte [política de conexão](connectivity-architecture.md#connection-policy).

As conexões feitas de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4,0 podem falhar na validação do certificado. Versões inferiores do Microsoft JDBC dependem do nome comum (CN) no campo assunto do certificado. A mitigação é garantir que a propriedade hostNameInCertificate esteja definida como *. database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [conectando-se com criptografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a atenuação acima não funcionar, entre em arquivo uma solicitação de suporte para o banco de dados SQL ou o SQL Instância Gerenciada usando a seguinte URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [arquitetura de conectividade do SQL do Azure](connectivity-architecture.md)
