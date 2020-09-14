---
title: Rotação de certificado para o banco de dados do Azure para PostgreSQL servidor único
description: Saiba mais sobre as alterações futuras de alterações de certificado raiz que afetarão o banco de dados do Azure para PostgreSQL servidor único
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: fcd27e84f479fa71d204e7d5a210ed16642d202b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057165"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Noções básicas sobre as alterações na AC raiz alterar para o banco de dados do Azure para PostgreSQL servidor único

O banco de dados do Azure para PostgreSQL alterará o certificado raiz para o aplicativo/driver cliente habilitado com SSL, usado para [se conectar ao servidor de banco de dados](concepts-connectivity-architecture.md). O certificado raiz atualmente disponível está definido para expirar em 26 de outubro de 2020 (10/26/2020) como parte das práticas recomendadas de manutenção e segurança padrão. Este artigo fornece mais detalhes sobre as alterações futuras, os recursos que serão afetados e as etapas necessárias para garantir que seu aplicativo mantenha a conectividade com o servidor de banco de dados.

## <a name="what-update-is-going-to-happen"></a>Qual atualização vai acontecer?

Em alguns casos, os aplicativos usam um arquivo de certificado local gerado por meio de um arquivo de certificado de AC (autoridade de certificação) confiável para se conectar com segurança. Atualmente, os clientes podem usar apenas o certificado predefinido para se conectar a um servidor do banco de dados do Azure para PostgreSQL, que está localizado [aqui](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). No entanto, o [Fórum do navegador da AC (autoridade](https://cabforum.org/) de certificação) publicou recentemente relatórios de vários certificados emitidos por fornecedores de autoridade de certificação para não estar em conformidade.

De acordo com os requisitos de conformidade do setor, os fornecedores de CA começaram a revogar certificados de CA para CAs não compatíveis, exigindo que os servidores usem certificados emitidos por CAs compatíveis e assinados por certificados de autoridade de certificação dessas CAs em conformidade. Como o banco de dados do Azure para PostgreSQL atualmente usa um desses certificados não compatíveis, que aplicativos cliente usam para validar suas conexões SSL, precisamos garantir que as ações apropriadas sejam tomadas (descritas abaixo) para minimizar o impacto potencial nos servidores PostgreSQL.

O novo certificado será usado a partir de 26 de outubro de 2020 (10/26/2020). Se você usar a validação de autoridade de certificação ou a validação completa do certificado do servidor ao se conectar de um cliente PostgreSQL (sslmode = Verify-CA ou sslmode = Verify-Full), será necessário atualizar a configuração do aplicativo antes de 26 de outubro de 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Como fazer saber se meu banco de dados será afetado?

Todos os aplicativos que usam SSL/TLS e verificam se o certificado raiz precisa atualizar o certificado raiz para se conectar ao banco de dados do Azure para PostgreSQL. Se você não estiver usando o SSL/TLS atualmente (sslmode = Disable) ou se não estiver verificando o certificado do servidor (sslmode = Allow, sslmode = prefira ou sslmode = require), não haverá nenhum impacto na disponibilidade do aplicativo. Você pode verificar se o aplicativo cliente está tentando usar o modo SSL com a AC (autoridade de certificação) confiável predefinida [aqui](concepts-ssl-connection-security.md#enforcing-tls-connections).

Para evitar que a disponibilidade do aplicativo seja interrompida devido aos certificados serem revogados inesperadamente ou para atualizar um certificado, que foi revogado, consulte a seção [**"o que preciso fazer para manter a conectividade"**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>O que preciso fazer para manter a conectividade

Para evitar que a disponibilidade do aplicativo seja interrompida devido aos certificados serem revogados inesperadamente ou para atualizar um certificado, que foi revogado, siga as etapas abaixo:

*   Baixe o BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 root CA dos links abaixo:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Gere um repositório de certificados de autoridade de certificação combinado com os certificados **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** incluídos.
    *   Para usuários do Java (PostgreSQL JDBC) usando DefaultJavaSSLFactory, execute:

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Em seguida, substitua o arquivo de keystore original pelo novo gerado:
        *   System. SetProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. SetProperty ("javax. net. SSL. trustStorePassword", "senha");
        
    *   Para usuários do .NET (Npgsql) no Windows, certifique-se de que **Baltimore Cybertrust raiz** e **DigiCert raiz global G2** existam no repositório de certificados do Windows, autoridades de certificação raiz confiáveis. Se não houver nenhum certificado, importe o certificado ausente.

        ![Banco de dados do Azure para PostgreSQL certificado .net](media/overview/netconnecter-cert.png)

    *   Para usuários do .NET (Npgsql) no Linux usando SSL_CERT_DIR, verifique se **BaltimoreCyberTrustRoot** e **DigiCertGlobalRootG2** existem no diretório indicado por SSL_CERT_DIR. Se não houver nenhum certificado, crie o arquivo de certificado ausente.

    *   Para outros usuários cliente do PostgreSQL, você pode mesclar dois arquivos de certificado de autoridade de certificação como este formato abaixo

        </br>-----INICIAR-----DE CERTIFICADO
 </br>(CA1 raiz: BaltimoreCyberTrustRoot. CRT. pem)
 </br>----------DE CERTIFICADO FINAL
 </br>-----INICIAR-----DE CERTIFICADO
 </br>(CA2 raiz: DigiCertGlobalRootG2. CRT. pem)
 </br>----------DE CERTIFICADO FINAL

*   Substitua o arquivo PEM da autoridade de certificação raiz original pelo arquivo de autoridade de certificação raiz combinado e reinicie o aplicativo/cliente.
*   No futuro, após o novo certificado implantado no lado do servidor, você poderá alterar o arquivo PEM da autoridade de certificação para DigiCertGlobalRootG2. CRT. PEM.

## <a name="what-can-be-the-impact"></a>Qual pode ser o impacto?
Se você estiver usando o certificado raiz Baltimore CyberTrust para verificar a conexão SSL com o banco de dados do Azure para PostgreSQL, conforme documentado aqui, a disponibilidade do aplicativo poderá ser interrompida, pois o banco de dados não estará acessível. Dependendo do seu aplicativo, você pode receber uma variedade de mensagens de erro, incluindo, mas não se limitando a:
*   Certificado inválido/certificado revogado
*   A conexão atingiu o tempo limite
*   Erro se aplicável

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. se eu não estiver usando SSL/TLS, ainda precisarei atualizar a autoridade de certificação raiz?
Nenhuma ação será necessária se você não estiver usando SSL/TLS. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. se eu estiver usando SSL/TLS, preciso reiniciar meu servidor de banco de dados para atualizar a AC raiz?
Não, você não precisa reiniciar o servidor de banco de dados para começar a usar o novo certificado. Essa é uma alteração no lado do cliente e as conexões de entrada do cliente precisam usar o novo certificado para garantir que eles possam se conectar ao servidor de banco de dados.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. o que acontecerá se eu não atualizar o certificado raiz antes de 26 de outubro de 2020 (10/26/2020)?
Se você não atualizar o certificado raiz antes de 30 de novembro de 2020, seus aplicativos que se conectam via SSL/TLS e a verificação para o certificado raiz não poderão se comunicar com o servidor de banco de dados PostgreSQL e o aplicativo terá problemas de conectividade com o servidor de banco de dados PostgreSQL.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. preciso planejar um tempo de inatividade de manutenção para essa alteração?<BR>
Não. Como a alteração aqui só está no lado do cliente para se conectar ao servidor de banco de dados, não há nenhum tempo de inatividade de manutenção necessário aqui para essa alteração.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. e se eu não conseguir um tempo de inatividade agendado para essa alteração antes de 26 de outubro de 2020 (10/26/2020)?
Como os clientes usados para se conectar ao servidor precisam atualizar as informações do certificado, conforme descrito na seção corrigir [aqui](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), não precisamos de um tempo de inatividade para o servidor nesse caso.

###  <a name="6-if-i-create-a-new-server-after-nov-30th-will-i-be-impacted"></a>6. se eu criar um novo servidor após 30 de novembro, serei afetado?
Para servidores criados após 26 de outubro de 2020 (10/26/2020), você pode usar o certificado emitido recentemente para seus aplicativos se conectarem usando SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. com que frequência o Microsoft atualiza seus certificados ou qual é a política de expiração?
Esses certificados usados pelo banco de dados do Azure para PostgreSQL são fornecidos por autoridades de certificação (CA) confiáveis. Portanto, o suporte desses certificados no banco de dados do Azure para PostgreSQL está vinculado ao suporte desses certificados pela CA. No entanto, como nesse caso, pode haver bugs imprevistos nesses certificados predefinidos que precisam ser corrigidos no início.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. se eu estiver usando réplicas de leitura, preciso executar essa atualização somente no servidor mestre ou em todas as réplicas de leitura?
Como essa atualização é uma alteração no lado do cliente, se o cliente usado para ler dados do servidor de réplica, precisaremos aplicar as alterações para esses clientes também. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. temos uma consulta do lado do servidor para verificar se o SSL está sendo usado?
Para verificar se você está usando a conexão SSL para se conectar ao servidor, consulte [verificação de SSL](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="10-what-if-i-have-further-questions"></a>10. e se eu tiver mais perguntas?
Se você tiver dúvidas, obtenha respostas de especialistas da Comunidade no [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Se você tiver um plano de suporte e precisar de ajuda técnica,  [entre em contato conosco](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)