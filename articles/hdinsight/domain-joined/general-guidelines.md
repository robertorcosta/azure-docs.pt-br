---
title: Diretrizes gerais de segurança corporativa no Azure HDInsight
description: Algumas práticas recomendadas que devem facilitar a implantação e o gerenciamento do Pacote de Segurança Corporativa.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463200"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Informações e diretrizes gerais de segurança corporativa no Azure HDInsight

Ao implantar um cluster HDInsight seguro, existem algumas práticas recomendadas que devem facilitar a implantação e o gerenciamento de clusters. Algumas informações gerais e diretrizes são discutidas aqui.

## <a name="use-of-secure-cluster"></a>Uso de cluster seguro

### <a name="recommended"></a>Recomendadas

* O cluster será usado por vários usuários ao mesmo tempo.
* Os usuários têm diferentes níveis de acesso aos mesmos dados.

### <a name="not-necessary"></a>Não é necessário

* Você vai executar apenas trabalhos automatizados (como uma única conta de usuário), um cluster padrão é bom o suficiente.
* Você pode fazer a importação de dados usando um cluster padrão e usar a mesma conta de armazenamento em um cluster seguro diferente, onde os usuários podem executar trabalhos de análise.

## <a name="use-of-local-account"></a>Uso de conta local

* Se você usar uma conta de usuário compartilhada ou uma conta local, então será difícil identificar quem usou a conta para alterar a configuração ou serviço.
* O uso de contas locais é problemático quando os usuários não fazem mais parte da organização.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Políticas

* Por padrão, ranger usa **Deny** como política.

* Quando o acesso aos dados é feito através de um serviço onde a autorização é ativada:
  * O plugin de autorização da Ranger é invocado e dado o contexto da solicitação.
  * O Ranger aplica as políticas configuradas para o serviço. Se as políticas do Ranger falharem, a verificação de acesso será adiada para o sistema de arquivos. Alguns serviços como mapReduce só verificase se o arquivo/pasta está sendo de propriedade do mesmo usuário que está enviando a solicitação. Serviços como hive, verificar se a correspondência`rwx`de propriedade ou permissões apropriadas do sistema de arquivos ( ).

* Para hive, além de ter as permissões para fazer Criar / `rwx`Atualizar / Excluir permissões, o usuário deve ter permissões no diretório no armazenamento e todos os subdiretórios.

* As políticas podem ser aplicadas a grupos (preferíveis) em vez de indivíduos.

* O autorizador ranger avaliará todas as apólices da Ranger para esse serviço para cada solicitação. Essa avaliação pode ter um impacto no tempo necessário para aceitar o trabalho ou consulta.

### <a name="storage-access"></a>Acesso de armazenamento

* Se o tipo de armazenamento for WASB, então nenhum token OAuth está envolvido.
* Se o Ranger tiver realizado a autorização, o acesso ao armazenamento acontecerá usando a Identidade Gerenciada.
* Se o Ranger não executou nenhuma autorização, então o acesso de armazenamento acontece usando o token OAuth do usuário.

### <a name="hierarchical-name-space"></a>Espaço hierárquico de nomes

Quando o espaço hierárquico do nome não estiver habilitado:

* Não há permissões herdadas.
* A única permissão do sistema de arquivos que funciona é a função **Storage Data XXXX** RBAC, a ser atribuída ao usuário diretamente no portal Azure.

### <a name="default-hdfs-permissions"></a>Permissões Padrão hdfs

* Por padrão, os usuários não **/** têm acesso à pasta no HDFS (eles precisam estar na função de proprietário do blob de armazenamento para ter acesso ao sucesso).
* Para o diretório de encenação para mapreduce e outros, um `sticky _wx` diretório específico do usuário é criado e fornece permissões. Os usuários podem criar arquivos e pastas por baixo, mas não podem olhar para outros itens.

### <a name="url-auth"></a>URL auth

Se o url auth estiver habilitado:

* A configuração conterá quais prefixos estão cobertos `adl://`na url auth (like ).
* Se o acesso for para esta url, então ranger verificará se o usuário está na lista de permitir.
* Ranger não vai verificar nenhuma das apólices de grãos finos.

## <a name="resource-groups"></a>Grupos de recursos

Use um novo grupo de recursos para cada cluster para que você possa distinguir entre recursos de cluster.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSGs, firewalls e gateway interno

* Use os NSGs (Network Security Groups, grupos de segurança de rede) para bloquear redes virtuais.
* Use firewall para lidar com políticas de acesso de saída.
* Use o gateway interno que não está aberto à internet pública.

## <a name="azure-active-directory"></a>Azure Active Directory

[O Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft.

### <a name="policies"></a>Políticas

* Desativar a política de acesso condicional usando a diretiva baseada em endereço IP. Isso requer que os pontos finais de serviço sejam ativados nos VNETs onde os clusters são implantados. Se você usar um serviço externo para MFA (algo diferente do AAD), a diretiva baseada em endereço IP não funcionará

* `AllowCloudPasswordValidation`a política é necessária para os usuários federados. Uma vez que o HDInsight usa o nome de usuário /senha diretamente para obter tokens do Azure AD, essa diretiva deve ser habilitada para todos os usuários federados.

* Habilite os pontos finais do serviço se você precisar de um desvio de acesso condicional usando IPs confiáveis.

### <a name="groups"></a>Grupos

* Sempre implante clusters com um grupo.
* Use o Azure AD para gerenciar membros de grupo (mais fácil do que tentar gerenciar os serviços individuais no cluster).

### <a name="user-accounts"></a>Contas de usuário

* Use uma conta de usuário única para cada cenário. Por exemplo, use uma conta para importação, use outra para consulta ou outros trabalhos de processamento.
* Use políticas ranger baseadas em grupo em vez de políticas individuais.
* Tenha um plano de como gerenciar usuários que não deveriam ter mais acesso a clusters.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[O Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) fornece serviços de domínio gerenciados, como participação em domínio, política de grupo, protocolo de acesso a diretórioleve (LDAP) e autenticação Kerberos / NTLM totalmente compatível com o Windows Server Active Directory.

O Azure AD DS é necessário para que clusters seguros se juntem a um domínio.
O HDInsight não pode depender de controladores de domínio on-premise ou controladores de domínio personalizados, pois introduz muitos pontos de falha, compartilhamento de credenciais, permissões de DNS e assim por diante. Para obter mais informações, consulte [as perguntas frequentes sobre o Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS instância

* Crie a instância `.onmicrosoft.com domain`com o . Dessa forma, não haverá vários servidores DNS servindo o domínio.
* Crie um certificado auto-assinado para o LDAPS e carregue-o no Azure AD DS.
* Use uma rede virtual peered para implantar clusters (quando você tiver uma série de equipes implantando clusters HDInsight ESP, isso será útil). Isso garante que você não precisa abrir portas (NSGs) na rede virtual com controlador de domínio.
* Configure o DNS para a rede virtual corretamente (o nome de domínio Azure AD DS deve ser resolvido sem entradas de arquivos hosts).
* Se você estiver restringindo o tráfego de saída, certifique-se de ter lido o suporte ao [firewall no HDInsight](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Propriedades sincronizadas do Azure AD para o Azure AD DS

* A conexão Azure AD sincroniza-se a partir do local para o Azure AD.
* Azure AD DS sincroniza do Azure AD.

O Azure AD DS sincroniza objetos do Azure AD periodicamente. A lâmina Azure AD DS no portal Azure exibe o status de sincronização. Durante cada etapa de sincronização, propriedades únicas podem entrar em conflito e renomeadas. Preste atenção ao mapeamento de propriedades do Azure AD para o Azure AD DS.

Para obter mais informações, consulte [a população do Azure AD UserPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)e [como funciona a sincronização do Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Sincronização de hash de senha

* As senhas são sincronizadas de forma diferente de outros tipos de objetos. Apenas hashes de senha não reversíveis são sincronizados no Azure AD e no Azure AD DS
* O AD on-premise para o Azure ad deve ser habilitado através do AD Connect
* A sincronização Azure AD para Azure AD DS é automática (as latências têm menos de 20 minutos).
* Os hashes de senha são sincronizados somente quando há uma senha alterada. Quando você habilita a sincronização de hash de senha, todas as senhas existentes não são sincronizadas automaticamente à medida que são armazenadas de forma irreversível. Quando você altera a senha, os hashes de senha são sincronizados.

### <a name="computer-objects-location"></a>Localização de objetos de computador

Cada cluster está associado a um único OU. Um usuário interno é provisionado na OU. Todos os nós são domínios unidos no mesmo OU.

### <a name="active-directory-administrative-tools"></a>Ferramentas administrativas do Diretório Ativo

Para obter etapas sobre como instalar as ferramentas administrativas do Active Directory em uma VM do Windows Server, consulte [Instalar ferramentas de gerenciamento](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Solução de problemas

### <a name="cluster-creation-fails-repeatedly"></a>A criação de clusters falha repetidamente

Razões mais comuns:

* A configuração do DNS não está correta, a junta de domínio dos nós de cluster falha.
* Os NSGs são muito restritivos, impedindo a adesão de domínios.
* Identidade Gerenciada não tem permissões suficientes.
* O nome do cluster não é único nos seis primeiros caracteres (seja com outro cluster ao vivo ou com um cluster excluído).

## <a name="next-steps"></a>Próximas etapas

* [Configurações do pacote de segurança corporativa com serviços de domínio de diretório ativo do Azure no HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Sincronize os usuários do Azure Active Directory em um cluster HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
