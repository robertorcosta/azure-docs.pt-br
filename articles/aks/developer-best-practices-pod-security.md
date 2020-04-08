---
title: Práticas recomendadas de segurança de pod
titleSuffix: Azure Kubernetes Service
description: Conheça as práticas recomendadas do desenvolvedor para saber como proteger os pods no serviço de Kubernetes do Azure (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804377"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas de segurança do pod no Serviço de Kubernetes do Azure (AKS)

Ao desenvolver e executar aplicativos no serviço de Kubernetes do Azure (AKS), a segurança de seus pods é uma consideração importante. Os aplicativos devem ser criados para a entidade de segurança do número mínimo de privilégios necessários. Manter os dados privados seguros é prioritário para clientes. Você não quer que as credenciais como cadeias de caracteres de conexão de banco de dados, chaves ou segredos e certificados expostos ao mundo externo, onde um ataque poderia tirar proveito desses segredos para fins mal-intencionados. Não os adicione ao seu código ou insira-os em suas imagens de contêiner. Essa abordagem criaria um risco de exposição e limitaria a capacidade de girar as credenciais uma vez que as imagens de contêiner precisam ser recriadas.

Este artigo de práticas recomendadas se concentra em como proteger pods em AKS. Você aprenderá como:

> [!div class="checklist"]
> * Usar o contexto de segurança do pod para limitar o acesso a serviços e processos ou elevação de privilégios
> * Autenticar com outros recursos do Azure usando identidades gerenciadas do pod
> * Solicitar e recuperar as credenciais de um cofre digital, como o Azure Key Vault

Você também pode ler as práticas recomendadas para [segurança do cluster][best-practices-cluster-security] e para [gerenciamento da imagem de contêiner][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Proteger o acesso de pod para recursos

**Diretrizes de práticas recomendadas** - executar como usuário ou grupo diferente e limitar o acesso para os processos de nó e serviços subjacentes, definir as configurações do contexto de segurança do pod. Atribua o menor número de privilégios necessários.

Para que seus aplicativos sejam executados corretamente, os pods devem ser executados como um grupo ou usuário definido e não como *raiz*. O `securityContext` para um pod ou contêiner permite que você defina configurações, como *runAsUser* ou *fsGroup* para presumir as permissões apropriadas. Somente atribua as permissões de usuário ou grupo necessárias e não use o contexto de segurança como um meio para admitir permissões adicionais. As configurações *runAsUser,* privilege escalation e outras configurações de recursos Linux estão disponíveis apenas em nós e pods Linux.

Ao executar como usuário não raiz, os contêineres não poderão associar às portas privilegiadas em 1024. Nesse cenário, os Serviços de Kubernetes podem ser usados para distinguir o fato de que um aplicativo é executado em uma porta específica.

Um contexto de segurança de pod também pode definir permissões para acessar os processos e serviços ou recursos adicionais. As seguintes definições de contexto de segurança comuns podem ser definidas:

* **allowPrivilegeEscalation** define se o pod pode assumir privilégios *raiz*. Projete seus aplicativos de forma que essa configuração seja sempre definida como *false*.
* **Os recursos do Linux** permitem que o pod acesse os processos de nó subjacente. Tome cuidado com a atribuição a esses recursos. Atribua o menor número de privilégios necessários. Para obter mais informações, consulte [Recursos do Linux][linux-capabilities].
* **Rótulos do SELinux** é um módulo de segurança de kernel do Linux que permite que você defina políticas de acesso para serviços, processos e acesso a sistema de arquivos. Novamente, atribua o menor número de privilégios necessários. Para obter mais informações, consulte [opções SELinux no Kubernetes][selinux-labels]

O manifesto YAML do pod de exemplo a seguir define as configurações de contexto de segurança a definir:

* O pod é executado com a ID de usuário *1000* e faz parte da ID do grupo *2000*
* Não é possível escalonar os privilégios para usar `root`
* Permite que os recursos do Linux acessem as interfaces de rede e o relógio (hardware) em tempo real do host

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Trabalhe com seu operador de cluster para determinar quais configurações de contexto de segurança são necessárias. Tente projetar seus aplicativos para minimizar o acesso que requer o pod e as permissões do pod. Há recursos de segurança adicionais para limitar o acesso usando o AppArmor e seccomp (computação segura) que pode ser implementado por operadores do cluster. Para obter mais informações, consulte [Proteger acesso do contêiner para recursos][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Exposição de credencial de limite

**Diretrizes de práticas recomendadas** - não defina as credenciais no código do aplicativo. Use identidades gerenciadas para recursos do Azure para permitir sua solicitação de acesso de pod para outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser usado para armazenar e recuperar as credenciais e chaves digitais. As identidades gerenciadas por pod destinam-se a ser usadas apenas com pods Linux e imagens de contêiner.

Para limitar o risco de credenciais que estão sendo expostas no código do aplicativo, evite o uso de credenciais compartilhadas ou fixas. As credenciais ou chaves não devem ser incluídas diretamente em seu código. Se essas credenciais são expostas, o aplicativo precisa ser atualizado e reimplantado. Uma abordagem melhor é dar aos pods sua própria identidade e maneira de se autenticar ou recuperar credenciais automaticamente de um cofre digital.

Os [seguintes projetos de código aberto aks associados][aks-associated-projects] permitem que você autentica automaticamente pods ou solicite credenciais e chaves de um cofre digital:

* Identidades gerenciadas para recursos do Azure e
* Driver de FlexVol do Azure Key Vault

Os projetos de código aberto associados da AKS não são suportados pelo suporte técnico do Azure. Eles são fornecidos para coletar feedbacks e bugs de nossa comunidade. Esses projetos não são recomendados para uso de produção.

### <a name="use-pod-managed-identities"></a>Usar identidades gerenciadas de pod

Uma identidade gerenciada para os recursos do Azure permite que um pod se autentique contra serviços do Azure que o suportam, como armazenamento ou SQL. O pod é atribuído a uma identidade do Azure Identity que permite que eles se autentiquem no Azure Active Directory e recebam um token digital. Esse token digital pode ser apresentado aos outros serviços do Azure que verificam se o pod está autorizado a acessar o serviço e executar as ações necessárias. Essa abordagem significa que não há segredos necessários para cadeias de caracteres de conexão de banco de dados, por exemplo. O fluxo de trabalho simplificado para identidade gerenciada de pod é mostrado no diagrama a seguir:

![Fluxo de trabalho simplificado para pod gerenciado de identidade no Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Com uma identidade gerenciada, seu código do aplicativo não precisa incluir as credenciais para acessar um serviço, como o Armazenamento do Microsoft Azure. Uma vez que cada pod é autenticado com sua própria identidade, então, você pode auditar e revisar o acesso. Se seu aplicativo se conecta com outros serviços do Azure, use identidades gerenciadas para reutilização de credenciais de limite e o risco de exposição.

Para obter mais informações sobre identidades de pod, consulte [Configurar um cluster AKS para usar identidades gerenciadas por pod e com seus aplicativos][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Usar o Azure Key Vault com FlexVol

Identidades de pod gerenciado funcionam muito bem para autenticar em relação a suporte de Serviços do Azure. Para seus próprios serviços ou aplicativos sem identidades gerenciadas para recursos do Azure, você ainda se autentica usando credenciais ou chaves. Um cofre digital pode ser usado para armazenar essas credenciais.

Quando os aplicativos precisam de uma credencial, se comunicam com o cofre digital, recuperam as credenciais mais recentes e, em seguida, conecte-se para o serviço necessário. O Azure Key Vault pode ser este cofre digital. O fluxo de trabalho simplificado para recuperar uma credencial de Cofre de Chaves do Azure usando identidades gerenciadas de pod é mostrado no diagrama a seguir:

![Identidade do fluxo de trabalho simplificado para recuperar uma credencial de Cofre de Chaves usando uma identidade gerenciada de pod](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Com o Azure Key Vault, você pode armazenar e regularmente girar segredos, como certificados, chaves de conta de armazenamento ou as credenciais. Você pode integrar o Azure Key Vault com um cluster AKS usando um FlexVolume. O driver FlexVolume permite que o cluster do AKS nativamente recupere credenciais do Cofre de Chaves e fornecê-las com segurança apenas para o pod solicitante. Trabalhe com seu operador de cluster para implantar o driver FlexVol de Cofre de Chaves para os nós do AKS. Você pode usar uma identidade gerenciada de pod para solicitar acesso ao Key Vault e recuperar as credenciais necessárias por meio do driver FlexVolume.

O Azure Key Vault com FlexVol destina-se a ser usado com aplicativos e serviços em execução em pods e nós Linux.

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentrou em como proteger seus pods. Para implementar algumas dessas áreas, confira os seguintes artigos:

* [Usar identidades gerenciadas para recursos do Azure com AKS][aad-pod-identity]
* [Integrar o Azure Key Vault ao AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
