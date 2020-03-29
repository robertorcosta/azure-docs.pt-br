---
title: SSL - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Instruções e informações para configurar o Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) e aplicativos associados para usar corretamente conexões SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973978"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Configure ssl no banco de dados Azure para PostgreSQL - Hyperscale (Citus)
As conexões de aplicação do cliente ao nó coordenador de hyperscale (Citus) exigem ssl (Secure Sockets Layer, camada de soquetes seguros). A aplicação de conexões SSL entre seu servidor de banco de dados e seus aplicativos clientes ajuda a proteger contra ataques "man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="enforcing-ssl-connections"></a>Impor conexões SSL
Para todos os servidores Azure Database para servidores PostgreSQL provisionados através do portal Azure, a aplicação de conexões SSL é habilitada por padrão. 

Da mesma forma, cadeias de conexão previamente definidas nas configurações de "Cadeias de Conexão" em seu servidor no Portal do Azure incluem os parâmetros necessários para linguagens comuns a fim de se conectar ao seu servidor de banco de dados usando SSL. O parâmetro SSL varia de acordo com o conector, por exemplo "ssl=true" ou "sslmode=require" ou "sslmode=required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Verificar se o seu aplicativo ou sua estrutura oferece suporte a conexões SSL
Alguns frameworks de aplicativos que usam postgreSQL para seus serviços de banco de dados não permitem o SSL por padrão durante a instalação. Se o servidor PostgreSQL forçar as conexões SSL, mas o aplicativo não estiver configurado para SSL, o aplicativo pode não se conectar ao servidor de banco de dados. Confira a documentação de seu aplicativo para saber como habilitar conexões SSL.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplicativos que exigem a verificação de certificado para conectividade SSL
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados Azure para PostgreSQL - Hyperscale (Citus) está localizado em https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Baixe o arquivo do certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conecte-se usando psql
O exemplo a seguir mostra como se conectar ao seu nó de coordenador de Hyperscale (Citus) usando o utilitário de linha de comando psql. Use `sslmode=verify-full` a configuração da seqüência de string de conexão para impor a verificação do certificado SSL. Passe o caminho do `sslrootcert` arquivo de certificado local para o parâmetro.

Abaixo está um exemplo da seqüência de conexão psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme se o `sslrootcert` valor passou para corresponder ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas
Aumente ainda mais a segurança com [as regras do Firewall no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
