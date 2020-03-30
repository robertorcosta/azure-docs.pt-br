---
title: Problemas de autenticação no Azure HDInsight
description: Problemas de autenticação no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896133"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemas de autenticação no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Em clusters seguros apoiados pelo Azure Data Lake (Gen1 ou Gen2), quando os usuários de domínio fazem login nos serviços de cluster através do HDI Gateway (como fazer login no portal Apache Ambari), o HDI Gateway tentará obter um token OAuth do Azure Active Directory (Azure AD) primeiro , e, em seguida, obter um bilhete Kerberos de Azure AD DS. A autenticação pode falhar em qualquer um desses estágios. Este artigo visa depurar algumas dessas questões.

Quando a autenticação falhar, você será solicitado para obter credenciais. Se você cancelar esta caixa de diálogo, a mensagem de erro será impressa. Aqui estão algumas das mensagens de erro comuns:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant ou unauthorized_client, 50126

### <a name="issue"></a>Problema

O login falha para usuários federados com código de erro 50126 (o login é bem sucedido para usuários de nuvem). A mensagem de erro é semelhante a:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

O código de erro Azure AD `AllowCloudPasswordValidation` 50126 significa que a política não foi definida pelo inquilino.

### <a name="resolution"></a>Resolução

O administrador da empresa do inquilino Azure AD deve permitir que o Azure AD use hashes de senha para usuários com backup do ADFS.  Aplique `AllowCloudPasswordValidationPolicy` o conforme mostrado no artigo [Use Enterprise Security Package no HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant ou unauthorized_client, 50034

### <a name="issue"></a>Problema

O login falha com o código de erro 50034. A mensagem de erro é semelhante a:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

O nome do usuário está incorreto (não existe). O usuário não está usando o mesmo nome de usuário que é usado no portal Azure.

### <a name="resolution"></a>Resolução

Use o mesmo nome de usuário que funciona nesse portal.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

A conta de usuário está bloqueada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Muitos assinam tentativas com uma senha incorreta.

### <a name="resolution"></a>Resolução

Espere por 30 minutos ou mais, pare quaisquer aplicativos que possam estar tentando autenticar.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant ou unauthorized_client, 50053

### <a name="issue"></a>Problema

Senha expirada, código de erro 50053. A mensagem de erro é semelhante a:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

A senha está expirada.

### <a name="resolution"></a>Resolução

Altere a senha no portal Azure (no seu sistema local) e aguarde 30 minutos para a sincronização para atualizar.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Receber mensagem de `interaction_required`erro .

### <a name="cause"></a>Causa

A política de acesso condicional ou MFA está sendo aplicada ao usuário. Como a autenticação interativa ainda não é compatível, o usuário ou o cluster precisa estar isento de acesso de MFA/condicional. Se você optar por isentar o cluster (política de isenção baseada em endereço IP), certifique-se de que o AD `ServiceEndpoints` esteja habilitado para esse vnet.

### <a name="resolution"></a>Resolução

Use a política de acesso condicional e isenta os clusters HDInisght do MFA, conforme mostrado no [Configure um cluster HDInsight com o Enterprise Security Package usando o Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Inscreva-se negado

### <a name="issue"></a>Problema

O login é negado.

### <a name="cause"></a>Causa

Para chegar a este estágio, sua autenticação OAuth não é um problema, mas a autenticação kerberos é. Se este cluster for apoiado pelo ADLS, o sinal de OAuth foi bem sucedido antes que Kerberos auth seja tentado. Em clusters WASB, o sinal de OAuth não é tentado. Pode haver muitas razões para a falha do Kerberos - como hashes de senha estão fora de sincronia, conta de usuário bloqueada no Azure AD DS, e assim por diante. Os hashes de senha sincronizam somente quando o usuário muda de senha. Quando você cria a ocorrência azure AD DS, ela começará a sincronizar senhas que são alteradas após a criação. Ele não sincronizar retroativamente senhas que foram definidas antes de seu início.

### <a name="resolution"></a>Resolução

Se você acha que as senhas podem não estar em sincronia, tente alterar a senha e espere alguns minutos para sincronizar.

Tente ssh em um Você precisará tentar autenticar (kinit) usando as mesmas credenciais de usuário, a partir de uma máquina que está juntada ao domínio. SSH na cabeça / nó de borda com um usuário local e, em seguida, executar kinit.

---

## <a name="kinit-fails"></a>kinit falha

### <a name="issue"></a>Problema

Kinit falha.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Para que o kinit tenha `sAMAccountName` sucesso, você precisa saber o seu (este é o nome da conta curta sem o reino). `sAMAccountName`é geralmente o prefixo `bob@contoso.com`da conta (como bob in ). Para alguns usuários, poderia ser diferente. Você precisará da capacidade de navegar / pesquisar `sAMAccountName`o diretório para aprender o seu .

Maneiras `sAMAccountName`de encontrar:

* Se você pode fazer login no Ambari usando o admin Ambari local, veja a lista de usuários.

* Se você tiver uma [máquina de windows com um domínio,](../../active-directory-domain-services/manage-domain.md)você pode usar as ferramentas padrão do Windows AD para navegar. Isso requer uma conta de trabalho no domínio.

* A partir do nó da cabeça, você pode usar comandos SAMBA para pesquisar. Isso requer uma sessão de Kerberos válida (kinit bem sucedido). pesquisa de anúncios líquidos "(userPrincipalName=bob*)"

    Os resultados da pesquisa /navegação devem mostrar o atributo. `sAMAccountName` Além disso, você pode olhar `pwdLastSet` `badPasswordTime`para `userPrincipalName` outros atributos como , , etc. para ver se essas propriedades correspondem ao que você espera.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit falha com falha de pré-autenticação

### <a name="issue"></a>Problema

Kinit falha `Preauthentication` com o fracasso.

### <a name="cause"></a>Causa

Nome de usuário ou senha incorreto.

### <a name="resolution"></a>Resolução

Verifique seu nome de usuário e senha. Verifique também se há outras propriedades descritas acima. Para habilitar a depuração `export KRB5_TRACE=/tmp/krb.log` verbose, execute-a a partir da sessão antes de tentar kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Falha no comando Job / HDFS devido ao TokenNotFoundException

### <a name="issue"></a>Problema

O comando Job /HDFS falha devido a `TokenNotFoundException`.

### <a name="cause"></a>Causa

O token de acesso OAuth necessário não foi encontrado para o trabalho / comando para ter sucesso. O driver ADLS /ABFS tentará recuperar o token de acesso OAuth do serviço de credencial antes de fazer solicitações de armazenamento. Este token é registrado quando você faz login no portal Ambari usando o mesmo usuário.

### <a name="resolution"></a>Resolução

Certifique-se de que você fez login com sucesso no portal Ambari uma vez através do nome de usuário cuja identidade é usada para executar o trabalho.

---

## <a name="error-fetching-access-token"></a>Erro ao buscar token de acesso

### <a name="issue"></a>Problema

O usuário `Error fetching access token`recebe mensagem de erro .

### <a name="cause"></a>Causa

Esse erro ocorre intermitentemente quando os usuários tentam acessar o ADLS Gen2 usando ACLs e o token Kerberos expirou.

### <a name="resolution"></a>Resolução

* Para o Azure Data Lake Storage Gen1, limpe o cache do navegador e faça login no Ambari novamente.

* Para o Azure Data Lake `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Storage Gen2, execute para o usuário que o usuário está tentando fazer login como

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
