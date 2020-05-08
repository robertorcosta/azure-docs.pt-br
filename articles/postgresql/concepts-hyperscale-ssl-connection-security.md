---
title: TLS-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Instruções e informações para configurar o banco de dados do Azure para PostgreSQL-Citus (hiperescala) e aplicativos associados para usar corretamente as conexões TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580572"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Configurar o TLS no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
As conexões de aplicativo cliente com o nó de coordenador de hiperescala (Citus) exigem TLS (segurança de camada de transporte), anteriormente conhecido como protocolo SSL (SSL). A imposição de conexões TLS entre o servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques "Man-in-the-middle" criptografando o fluxo de dados entre o servidor e seu aplicativo.

## <a name="enforcing-tls-connections"></a>Impondo conexões TLS
Para todos os servidores do banco de dados do Azure para PostgreSQL provisionados por meio do portal do Azure, a imposição de conexões TLS é habilitada por padrão. 

Da mesma forma, cadeias de conexão predefinidas nas configurações de "cadeias de conexão" em seu servidor no portal do Azure incluem os parâmetros necessários para linguagens comuns para se conectar ao servidor de banco de dados usando TLS. O parâmetro TLS varia de acordo com o conector, por exemplo, "SSL = true" ou "sslmode = require" ou "sslmode = required" e outras variações.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Verifique se seu aplicativo ou estrutura dá suporte a conexões TLS
Algumas estruturas de aplicativo que usam o PostgreSQL para seus serviços de banco de dados não habilitam o TLS por padrão durante a instalação. Se o seu servidor PostgreSQL impõe conexões TLS, mas o aplicativo não está configurado para TLS, o aplicativo pode falhar ao se conectar ao seu servidor de banco de dados. Consulte a documentação do aplicativo para saber como habilitar conexões TLS.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplicativos que exigem verificação de certificado para conectividade TLS
Em alguns casos, os aplicativos exigem um arquivo de certificado local gerado de um arquivo de certificado (.cer) de uma Autoridade de Certificação (CA) confiável para se conectar com segurança. O certificado para se conectar a um banco de dados do Azure para PostgreSQL-Citus (hiperescala https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) está localizado em. Baixe o arquivo de certificado e salve-o em seu local preferido.

### <a name="connect-using-psql"></a>Conectar usando psql
O exemplo a seguir mostra como se conectar ao seu nó de coordenador de hiperescala (Citus) usando o utilitário de linha de comando psql. Use a `sslmode=verify-full` configuração cadeia de conexão para impor a verificação de certificado TLS. Passe o caminho do arquivo de certificado local `sslrootcert` para o parâmetro.

Abaixo está um exemplo da cadeia de conexão do psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Confirme se o valor passado para `sslrootcert` corresponde ao caminho do arquivo para o certificado que você salvou.

## <a name="next-steps"></a>Próximas etapas
Aumente ainda mais a segurança com [as regras de firewall no banco de dados do Azure para PostgreSQL-Citus (hiperescala)](concepts-hyperscale-firewall-rules.md).
