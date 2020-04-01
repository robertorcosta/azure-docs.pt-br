---
title: TLS - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Instruções e informações para configurar o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) e aplicativos associados para usar corretamente conexões TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422328"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure TLS no banco de dados Azure para PostgreSQL - Hyperscale (Citus)
As conexões de aplicativos cliente sao o nó coordenador de Hyperscale (Citus) requerem TLS (Transport Layer Security, segurança de camada de transporte), anteriormente conhecida como Secure Sockets Layer (SSL). A aplicação de conexões TLS entre seu servidor de banco de dados e seus aplicativos clientes ajuda a proteger contra ataques "man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="enforcing-tls-connections"></a>Aplicando conexões TLS
Para todos os servidores Azure Database para servidores PostgreSQL provisionados através do portal Azure, a aplicação das conexões TLS é habilitada por padrão. 

Da mesma forma, as strings de conexão pré-definidas nas configurações "Strings de conexão" em seu servidor no portal Azure incluem os parâmetros necessários para que os idiomas comuns se conectem ao servidor de banco de dados usando TLS. O parâmetro TLS varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Certifique-se de que seu aplicativo ou estrutura suporte conexões TLS
Alguns frameworks de aplicativos que usam PostgreSQL para seus serviços de banco de dados não permitem o TLS por padrão durante a instalação. Se o servidor PostgreSQL forçar as conexões TLS, mas o aplicativo não estiver configurado para TLS, o aplicativo pode não se conectar ao servidor de banco de dados. Consulte a documentação do seu aplicativo para saber como ativar conexões TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicativos que requerem verificação de certificado para conectividade TLS
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados Azure para PostgreSQL - Hyperscale (Citus) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Baixe o arquivo do certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conecte-se usando psql
O exemplo a seguir mostra como se conectar ao seu nó de coordenador de Hyperscale (Citus) usando o utilitário de linha de comando psql. Use `sslmode=verify-full` a configuração da seqüência de string de conexão para impor a verificação do certificado TLS. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

Abaixo está um exemplo da seqüência de conexão psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme se o `sslrootcert` valor passou para corresponder ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas
Aumente ainda mais a segurança com [as regras do Firewall no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
