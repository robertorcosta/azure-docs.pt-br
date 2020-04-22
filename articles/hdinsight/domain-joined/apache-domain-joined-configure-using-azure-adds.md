---
title: Segurança corporativa com Azure AD DS - Azure HDInsight
description: Saiba como configurar e configurar um cluster hdinsight enterprise security package usando o Azure Active Directory Domain Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 04/17/2020
ms.openlocfilehash: c045378b6e69a9bb1b696d3390dadf84a50bd3b7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687285"
---
# <a name="enterprise-security-package-configurations-with-azure-active-directory-domain-services-in-hdinsight"></a>Configurações do pacote de segurança corporativa com serviços de domínio de diretório ativo do Azure no HDInsight

Os clusters Enterprise Security Package (ESP) fornecem acesso a vários usuários nos clusters Azure HDInsight. Os clusters HDInsight com ESP estão conectados a um domínio. Essa conexão permite que os usuários de domínio usem suas credenciais de domínio para autenticar com os clusters e executar trabalhos de big data.

Neste artigo, você aprende como configurar um cluster HDInsight com o ESP usando o Azure Active Directory Domain Services (Azure AD DS).

> [!NOTE]  
> O ESP está geralmente disponível no HDInsight 3.6 e 4.0 para esses tipos de cluster: Apache Spark, Interactive, Hadoop e HBase. ESP para o tipo de cluster Apache Kafka está em visualização apenas com suporte de melhor esforço. Os clusters ESP criados antes da data ESP GA (1 º de outubro de 2018) não são suportados.

## <a name="enable-azure-ad-ds"></a>Habilitar o Azure AD DS

> [!NOTE]  
> Apenas os administradores inquilinos têm os privilégios de habilitar o Azure AD DS. Se o armazenamento em cluster for Azure Data Lake Storage Gen1 ou Gen2, você deve desativar a Autenticação Multifatorial do Azure apenas para usuários que precisarão acessar o cluster usando a autenticação básica do Kerberos.
>
> Você pode usar [IPs confiáveis](../../active-directory/authentication/howto-mfa-mfasettings.md#trusted-ips) ou [Acesso Condicional](../../active-directory/conditional-access/overview.md) para desativar a autenticação multifatorial para usuários específicos *apenas* quando eles estiverem acessando a gama IP para a rede virtual do cluster HDInsight. Se você estiver usando o Conditional Access, certifique-se de que o ponto final do serviço active directory esteja ativado na rede virtual HDInsight.
>
> Se o armazenamento em cluster for armazenamento Azure Blob, não desabilite a Autenticação Multifatorial.

Habilitar o Azure AD DS é um pré-requisito antes de criar um cluster HDInsight com ESP. Para obter mais informações, consulte [Ativar os serviços de domínio do diretório ativo do Azure usando o portal Azure](../../active-directory-domain-services/tutorial-create-instance.md).

Quando o Azure AD DS está ativado, todos os usuários e objetos começam a sincronizar do Azure Active Directory (Azure AD) para o Azure AD DS por padrão. A duração da operação de sincronização depende do número de objetos no Azure AD. A sincronização pode levar alguns dias para centenas de milhares de objetos.

O nome de domínio que você usa com o Azure AD DS deve ser de 39 caracteres ou menos, para trabalhar com o HDInsight.

Você pode optar por sincronizar apenas os grupos que precisam de acesso aos clusters HDInsight. Essa opção de sincronizar apenas determinados grupos é chamada de *sincronização com escopo*. Para obter instruções, [consulte Configure a sincronização escopo do Azure AD para o domínio gerenciado](../../active-directory-domain-services/scoped-synchronization.md).

Quando estiver habilitando o LDAP seguro, coloque o nome de domínio no nome do assunto. E o nome alternativo do assunto no certificado. Se o nome de domínio for *contoso100.onmicrosoft.com,* certifique-se de que o nome exato existe no nome do assunto do certificado e no nome alternativo do assunto. Para obter mais informações, consulte [Configurar LDAP seguro para um domínio gerenciado do Azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md).

O exemplo a seguir cria um certificado auto-assinado. O nome *de* domínio `Subject` contoso100.onmicrosoft.com está `DnsName` tanto no (nome do assunto) quanto no (nome alternativo do assunto).

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.onmicrosoft.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.onmicrosoft.com, contoso100.onmicrosoft.com
```

## <a name="check-azure-ad-ds-health-status"></a>Verifique o estado de saúde do Azure AD DS

Exibir o status de saúde dos Serviços de Domínio do Diretório Ativo do Azure selecionando **Saúde** na categoria **Gerenciar.** Certifique-se de que o status do Azure AD DS está verde (em execução) e a sincronização está completa.

![Azure AD DS saúde](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-health.png)

## <a name="create-and-authorize-a-managed-identity"></a>Criar e autorizar uma identidade gerenciada

Use uma *identidade gerenciada atribuída pelo usuário* para simplificar as operações seguras de serviços de domínio. Quando você atribui a função **de contribuinte de serviços de domínio hdinsight** à identidade gerenciada, ele pode ler, criar, modificar e excluir operações de serviços de domínio.

Certas operações de serviços de domínio, como a criação de UOs e diretores de serviços, são necessárias para o HDInsight Enterprise Security Package. Você pode criar identidades gerenciadas em qualquer assinatura. Para obter mais informações sobre identidades gerenciadas em geral, consulte [Identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [Identidades gerenciadas no Azure HDInsight](../hdinsight-managed-identities.md).

Para configurar clusters ESP, crie uma identidade gerenciada atribuída pelo usuário se você ainda não tiver uma. Veja [`Create, list, delete, or assign a role to a user-assigned managed identity by using the Azure portal`](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

Em seguida, atribua a função **de contribuinte de serviços de domínio hdinsight** à identidade gerenciada no **controle de acesso** para Azure AD DS. Você precisa de privilégios de administrador azure AD DS para fazer essa atribuição de função.

![Controle de acesso do Azure Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-configure-managed-identity.png)

Atribuir a função **de contribuinte de serviços de domínio HDInsight** garante que essa identidade tenha acesso adequado (`on behalf of`) para fazer operações de serviços de domínio no domínio Azure AD DS. Essas operações incluem a criação e exclusão de OUs.

Depois que a identidade gerenciada é dada a função, o administrador Azure AD DS gerencia quem a usa. Primeiro, o admin seleciona a identidade gerenciada no portal. Em seguida, seleciona **o Controle de Acesso (IAM)** em Visão **Geral**. O administrador atribui a função **Operador de Identidade Gerenciada** aos usuários ou grupos que desejam criar clusters ESP.

Por exemplo, o administrador do Azure AD DS pode atribuir essa função ao grupo **MarketingTeam** para a identidade gerenciada pelo **SJMSI.** Um exemplo é mostrado na imagem a seguir. Essa atribuição garante que as pessoas certas na organização possam usar a identidade gerenciada para criar clusters ESP.

![Atribuição da Função de Operador de Identidade Gerenciada do HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-managed-identity-operator-role-assignment.png)

## <a name="network-considerations"></a>Considerações de rede

> [!NOTE]  
> O Azure AD DS deve ser implantado em uma rede virtual baseada no Azure Resource Manager. Redes virtuais clássicas não são suportadas para o Azure AD DS. Para obter mais informações, consulte [Ativar os serviços de domínio do diretório ativo do Azure usando o portal Azure](../../active-directory-domain-services/tutorial-create-instance-advanced.md#create-and-configure-the-virtual-network).

Habilite o Azure AD DS. Em seguida, um servidor DNS (Domain Name System, sistema de nomes de domínio) local é executado nas máquinas virtuais do Active Directory (VMs). Configure sua rede virtual Azure AD DS para usar esses servidores DNS personalizados. Para localizar os endereços IP certos, selecione **Propriedades** na categoria **Gerenciar** e procure em ENDEREÇO IP NA **REDE VIRTUAL**.

![Localizar endereços IP para servidores DNS locais](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-dns1.png)

Alterar a configuração dos servidores DNS na rede virtual Azure AD DS. Para usar esses IPs **personalizados,** selecione servidores DNS na categoria **Configurações.** Em seguida, selecione a opção **Personalizado,** digite o primeiro endereço IP na caixa de texto e **selecione Salvar**. Adicione mais endereços IP usando os mesmos passos.

![Atualizando a configuração de DNS de rede virtual](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-vnet-configuration.png)

É mais fácil colocar a instância do Azure AD DS e o cluster HDInsight na mesma rede virtual do Azure. Se você planeja usar diferentes redes virtuais, você deve observar essas redes virtuais para que o controlador de domínio seja visível para as VMs HDInsight. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Depois que as redes virtuais forem acompanhadas, configure a rede virtual HDInsight para usar um servidor DNS personalizado. E digite os IPs privados Azure AD DS como endereços do servidor DNS. Quando ambas as redes virtuais usarem os mesmos servidores DNS, seu nome de domínio personalizado será resolvido no IP certo e será acessível a partir do HDInsight. Por exemplo, se o `contoso.com`nome de domínio `ping contoso.com` for , então após esta etapa, deve resolver para o IP AD DS azure certo.

![Configurando servidores DNS personalizados para uma rede virtual peered](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-aadds-peered-vnet-configuration.png)

Se você estiver usando as regras do Grupo de Segurança de Rede (NSG) em sua sub-rede HDInsight, você deve permitir os [IPs necessários](../hdinsight-management-ip-addresses.md) para tráfego de entrada e saída.

Para testar sua configuração de rede, junte uma VM do Windows à rede/sub-rede virtual HDInsight e pinge o nome de domínio. (Ele deve resolver para um IP.) Execute **ldp.exe** para acessar o domínio Azure AD DS. Em seguida, junte este VM do Windows ao domínio para confirmar se todas as chamadas RPC necessárias são bem sucedidas entre o cliente e o servidor.

Use **o nslookup** para confirmar o acesso da rede à sua conta de armazenamento. Ou qualquer banco de dados externo que você possa usar (por exemplo, metastore externo da Colmeia ou Ranger DB). Certifique-se de que as [portas necessárias](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers) são permitidas nas regras de NSG da sub-rede Azure AD DS, se um NSG proteger o Azure AD DS. Se a junção de domínio deste VM do Windows for bem sucedida, então você pode continuar até o próximo passo e criar clusters ESP.

## <a name="create-an-hdinsight-cluster-with-esp"></a>Crie um cluster HDInsight com ESP

Depois de configurar as etapas anteriores corretamente, o próximo passo é criar o cluster HDInsight com o ESP ativado. Ao criar um cluster HDInsight, você pode ativar o Enterprise Security Package na guia **Segurança + rede.** Para obter um modelo de Gerenciador de Recursos do Azure para implantação, use a experiência do portal uma vez. Em seguida, baixe o modelo pré-preenchido na página **Revisar + criar** para reutilização futura.

Você também pode habilitar o recurso [HDInsight ID Broker](identity-broker.md) durante a criação de clusters. O recurso ID Broker permite que você faça login no Ambari usando a Autenticação Multifatorial e obtenha os bilhetes Kerberos necessários sem precisar de hashes de senha no Azure AD DS.

> [!NOTE]  
> Os primeiros seis caracteres dos nomes de cluster do ESP devem ser exclusivos em seu ambiente. Por exemplo, se você tiver vários clusters ESP em diferentes redes virtuais, escolha uma convenção de nomeação que garanta que os seis primeiros caracteres nos nomes de cluster sejam únicos.

![Validação de domínio para o pacote de segurança corporativa Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp.png)

Depois de habilitar o ESP, as configurações erradas comuns relacionadas ao Azure AD DS são automaticamente detectadas e validadas. Depois de corrigir esses erros, você pode continuar com o próximo passo.

![Azure HDInsight Enterprise Security Package falhou na validação do domínio](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-esp-error.png)

Quando você cria um cluster HDInsight com ESP, você deve fornecer os seguintes parâmetros:

* **Usuário de administrador de**cluster : Escolha um administrador para o cluster a partir da instância AD DS sincronizada do Azure. Esta conta de domínio já deve estar sincronizada e disponível no Azure AD DS.

* **Grupos de acesso de**cluster : Os grupos de segurança cujos usuários você deseja sincronizar e ter acesso ao cluster devem estar disponíveis no Azure AD DS. Um exemplo é o grupo HiveUsers. Para obter mais informações, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* **URL LDAPS**: `ldaps://contoso.com:636`Um exemplo é .

A identidade gerenciada criada pode ser escolhida a partir da lista de sossegado para baixo de **identidade atribuída pelo Usuário** quando você estiver criando um novo cluster.

![Azure HDInsight ESP Active Directory Domain Services gerenciado identidade](./media/apache-domain-joined-configure-using-azure-adds/azure-portal-cluster-security-networking-identity.png).

## <a name="next-steps"></a>Próximas etapas

* Para configurar as políticas do Hive e executar as consultas do Apache Hive, consulte [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar clusters HDInsight com ESP, consulte [Usar SSH com Apache Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
