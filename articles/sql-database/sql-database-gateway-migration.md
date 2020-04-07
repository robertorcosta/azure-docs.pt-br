---
title: Aviso de migração de tráfego de gateway
description: Artigo fornece aviso aos usuários sobre a migração de endereços IP do Azure SQL Database Gateways
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757073"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migração de tráfego do Banco de Dados Azure SQL para gateways mais novos

À medida que a infra-estrutura do Azure melhora, a Microsoft atualizará periodicamente o hardware para garantir que forneçamos a melhor experiência possível ao cliente. Nos próximos meses, planejamos adicionar Gateways construídos sobre as novas gerações de hardware, migrar o tráfego para eles e, eventualmente, desativar gateways construídos em hardware sustado em algumas regiões.  

Os clientes serão notificados por e-mail e no portal Azure bem antes de qualquer alteração para Gateways disponíveis em cada região. As informações mais atualizadas serão mantidas na tabela de endereços IP do [gateway gateway Do Banco de Dados Azure SQL.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Impacto dessa mudança

A primeira rodada de migração de tráfego para gateways mais novos está marcada para 14 de outubro de **2019** nas seguintes regiões:
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

A migração de tráfego mudará o endereço IP público que o DNS resolve para o seu Banco de Dados SQL.
Você será impactado se tiver:
- Codificado rígido o endereço IP para qualquer Gateway em particular no firewall local
- Quaisquer sub-redes que usam o Microsoft.SQL como ponto final de serviço, mas não podem se comunicar com os endereços IP do Gateway

Você não será impactado se tiver:
- Redirecionamento como política de conexão
- Conexões ao Banco de Dados SQL de dentro do Azure e usando tags de serviço
- As conexões feitas usando versões suportadas do JDBC Driver for SQL Server não verão nenhum impacto. Para versões JDBC suportadas, consulte [Baixar o Driver Microsoft JDBC para SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>O que fazer se for afetado

Recomendamos que você permita tráfego de saída para endereços IP para todos os [endereços IP do gateway de banco de dados Azure SQL](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) na região na porta TCP 1433 e na faixa de porta 11000-11999. Essa recomendação é aplicável aos clientes que se conectam a partir de locais e também àqueles que se conectam via Service Endpoints. Para obter mais informações sobre as faixas de porta, consulte [A diretiva Conexão](sql-database-connectivity-architecture.md#connection-policy).

Conexões feitas a partir de aplicativos usando o Microsoft JDBC Driver abaixo da versão 4.0 podem falhar na validação do certificado. Versões inferiores do Microsoft JDBC dependem do Nome Comum (CN) no campo Assunto do certificado. A mitigação é para garantir que a propriedade hostNameInCertificate esteja definida como *.database.windows.net. Para obter mais informações sobre como definir a propriedade hostNameInCertificate, consulte [Conectando com criptografia](/sql/connect/jdbc/connecting-with-ssl-encryption).

Se a mitigação acima não funcionar, faça uma solicitação de suporte para o Banco de Dados SQL usando a seguinte URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a arquitetura de conectividade SQL do Azure](sql-database-connectivity-architecture.md)
