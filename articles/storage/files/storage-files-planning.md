---
title: Planejando uma implantação de Arquivos do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar uma implantação de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76a96d36387f55889b65f16ea1ca6ec07359c377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502440"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planejando uma implantação de Arquivos do Azure
[Os arquivos do Azure](storage-files-introduction.md) podem ser implantados de duas maneiras principais: montando diretamente os compartilhamentos de arquivos Do Zure sem servidor ou arquivar os compartilhamentos de arquivos do Azure no local usando o Azure File Sync. Qual opção de implantação você escolhe muda as coisas que você precisa considerar como você planeja para sua implantação. 

- **Montagem direta de um compartilhamento de arquivos Azure**: Como o Azure Files fornece acesso ao SMB, você pode montar compartilhamentos de arquivos Do Zure no local ou na nuvem usando o cliente SMB padrão disponível no Windows, macOS e Linux. Como os compartilhamentos de arquivos do Azure não têm servidor, a implantação para cenários de produção não requer o gerenciamento de um servidor de arquivos ou dispositivo NAS. Isso significa que você não precisa aplicar patches de software ou trocar discos físicos. 

- **O compartilhamento de arquivos do Cache Azure no local com o Azure File Sync**: O Azure File Sync permite que você centralize os compartilhamentos de arquivos da sua organização em Arquivos Azure, mantendo a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos no local. O Azure File Sync transforma um Windows Server no local (ou na nuvem) em um cache rápido do seu compartilhamento de arquivos Azure. 

Este artigo aborda principalmente considerações de implantação para a implantação de um compartilhamento de arquivos Do Zure para ser montado diretamente por um cliente on-premises ou na nuvem. Para planejar uma implantação do Azure File Sync, consulte [Planejamento para uma implantação do Azure File Sync](storage-sync-files-planning.md).

## <a name="management-concepts"></a>Conceitos de gerenciamento
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Ao implantar compartilhamentos de arquivos do Azure em contas de armazenamento, recomendamos:

- Apenas a implantação de compartilhamentos de arquivos do Azure em contas de armazenamento com outros compartilhamentos de arquivos do Azure. Embora as contas de armazenamento GPv2 permitam que você tenha contas de armazenamento de propósito misto, uma vez que recursos de armazenamento, como compartilhamentos de arquivos do Azure e contêineres blob compartilham os limites da conta de armazenamento, a mistura de recursos em conjunto pode tornar mais difícil solucionar problemas problemas de desempenho mais tarde. 

- Prestando atenção às limitações de IOPS de uma conta de armazenamento ao implantar compartilhamentos de arquivos DoZure. Idealmente, você mapearia compartilhamentos de arquivos 1:1 com contas de armazenamento, no entanto, isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização quanto do Azure. Quando não for possível ter apenas um compartilhamento de arquivo implantado em uma conta de armazenamento, considere quais ações serão altamente ativas e quais ações serão menos ativas para garantir que as ações de arquivo mais quentes não sejam colocadas na mesma conta de armazenamento juntas.

- Apenas implante contas GPv2 e FileStorage e atualize contas de armazenamento GPv1 e clássicas quando encontrá-las em seu ambiente. 

## <a name="identity"></a>Identidade
Para acessar um compartilhamento de arquivos do Azure, o usuário do compartilhamento de arquivos deve ser autenticado e ter autorização para acessar o compartilhamento. Isso é feito com base na identidade do usuário que acessa o compartilhamento de arquivos. O Azure Files integra-se a três principais provedores de identidade:
- **Active Directory** (visualização) de propriedade do cliente: as contas de armazenamento do Azure podem ser acompanhadas de domínio a um diretório ativo do Windows Server de propriedade do cliente, assim como um servidor de arquivos do Windows Server ou um dispositivo NAS. O controlador de domínio do diretório ativo pode ser implantado no local, em uma VM azure ou até mesmo como uma VM em outro provedor de nuvem; Azure Files é agnóstico para onde seu DC está hospedado. Uma vez que uma conta de armazenamento é acompanhada por domínio, o usuário final pode montar um compartilhamento de arquivos com a conta de usuário com a que entrou em seu PC. A autenticação baseada em AD usa o protocolo de autenticação Kerberos.
- **Azure Active Directory Domain Services (Azure AD DS)**: O Azure AD DS fornece um Controlador de Domínio de Diretório Ativo gerenciado pela Microsoft que pode ser usado para recursos do Azure. O domínio que une sua conta de armazenamento ao Azure AD DS fornece benefícios semelhantes ao domínio que a une a um Active Directory de propriedade do cliente. Essa opção de implantação é mais útil para cenários de elevação e mudança de aplicativos que requerem permissões baseadas em Anúncios. Como o Azure AD DS fornece autenticação baseada em AD, essa opção também usa o protocolo de autenticação Kerberos.
- **Chave da conta de armazenamento do Azure**: os compartilhamentos de arquivos Do Azure também podem ser montados com uma chave de conta de armazenamento Azure. Para montar um compartilhamento de arquivos desta forma, o nome da conta de armazenamento é usado como nome de usuário e a chave da conta de armazenamento é usada como senha. Usar a chave da conta de armazenamento para montar o compartilhamento de arquivos do Azure é efetivamente uma operação de administrador, uma vez que o compartilhamento de arquivos montado terá permissões completas para todos os arquivos e pastas no compartilhamento, mesmo que eles tenham ACLs. Ao usar a chave da conta de armazenamento para montar sobre SMB, o protocolo de autenticação NTLMv2 é usado.

Para clientes que migram de servidores de arquivos locais ou criam novos compartilhamentos de arquivos em Arquivos Azure destinados a se comportar como servidores de arquivos Windows ou dispositivos NAS, o domínio que une sua conta de armazenamento ao **Active Directory de propriedade do cliente** é a opção recomendada. Para saber mais sobre o domínio que adere sua conta de armazenamento a um Active Directory de propriedade do cliente, consulte [a visão geral do Azure Files Active Directory](storage-files-active-directory-overview.md).

Se você pretende usar a chave da conta de armazenamento para acessar seus compartilhamentos de arquivos do Azure, recomendamos usar pontos finais de serviço conforme descrito na seção [Rede.](#networking)

## <a name="networking"></a>Rede
Os compartilhamentos de arquivos do Azure são acessíveis de qualquer lugar através do ponto final público da conta de armazenamento. Isso significa que solicitações autenticadas, como solicitações autorizadas pela identidade de logon de um usuário, podem se originar com segurança de dentro ou fora do Azure. Em muitos ambientes de clientes, uma montagem inicial do compartilhamento de arquivos Do Zure em sua estação de trabalho no local falhará, mesmo que as montagens das VMs do Azure tenham sucesso. A razão para isso é que muitas organizações e provedores de serviços de internet (ISPs) bloqueiam a porta que o SMB usa para se comunicar, porta 445. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para desbloquear o acesso ao compartilhamento de arquivos do Azure, você tem duas opções principais:

- Desbloquear porta 445 para a rede local da sua organização. Os compartilhamentos de arquivos do Azure só podem ser acessados externamente através do ponto final público usando protocolos seguros para a Internet, como o SMB 3.0 e a API FileREST. Esta é a maneira mais fácil de acessar o compartilhamento de arquivos do Azure no local, uma vez que não requer configuração avançada de rede além de alterar as regras de porta de saída da sua organização, no entanto, recomendamos que você remova versões herdadas e depreciadas do SMB protocolo, ou seja, SMB 1.0. Para saber como fazer isso, consulte [Protegendo o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) e protegendo o [Linux](storage-how-to-use-files-linux.md#securing-linux).

- Acesse os compartilhamentos de arquivos do Azure através de uma conexão ExpressRoute ou VPN. Quando você acessa o compartilhamento de arquivos do Azure através de um túnel de rede, você é capaz de montar o compartilhamento de arquivos do Azure como um compartilhamento de arquivos no local, uma vez que o tráfego de SMB não atravessa seu limite organizacional.   

Embora, do ponto de vista técnico, seja consideravelmente mais fácil montar suas ações de arquivos Do Azure através do ponto final público, esperamos que a maioria dos clientes opte por montar seus compartilhamentos de arquivos Azure através de uma conexão ExpressRoute ou VPN. Para fazer isso, você precisará configurar o seguinte para o seu ambiente:  

- **Túnel de rede usando ExpressRoute, Site-to-Site ou POINT-to-Site VPN**: O tunelamento em uma rede virtual permite acessar os compartilhamentos de arquivos do Azure a partir do local, mesmo que a porta 445 esteja bloqueada.
- **Pontos finais privados**: Os pontos finais privados dão à sua conta de armazenamento um endereço IP dedicado dentro do espaço de endereço da rede virtual. Isso permite o tunelamento de rede sem a necessidade de abrir redes no local até todas as faixas de endereços IP pertencentes aos clusters de armazenamento Do Zure. 
- **Encaminhamento de DNS**: Configure seu DNS no local para resolver o `storageaccount.file.core.windows.net` nome da sua conta de armazenamento (ou seja, para as regiões de nuvem pública) para resolver para o endereço IP de seus pontos finais privados.

Para planejar a rede associada à implantação de um compartilhamento de arquivos Azure, consulte [considerações de rede do Azure Files](storage-files-networking-overview.md).

## <a name="encryption"></a>Criptografia
O Azure Files suporta dois tipos diferentes de criptografia: criptografia em trânsito, que se relaciona com a criptografia usada ao montar/acessar o compartilhamento de arquivos do Azure e criptografia em repouso, que se relaciona com a forma como os dados são criptografados quando são armazenados em disco. 

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. Isso significa que quando você montar um compartilhamento de arquivo via SMB ou acessá-lo por meio do protocolo FileREST (por exemplo, por meio do portal do Azure, do PowerShell/CLI ou de SDKs do Azure), os Arquivos do Azure só permitirão a conexão se for feita com o SMB 3.0 e com criptografia ou HTTPS. Os clientes que não são compatíveis com SMB 3.0 ou os clientes que são compatíveis com SMB 3.0, mas não com a criptografia SMB, não poderão montar o compartilhamento de arquivo do Azure se a criptografia em trânsito estiver habilitada. Para obter mais informações sobre quais sistemas operacionais são compatíveis com o SMB 3.0 com criptografia, consulte nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, da CLI e dos SDKs são compatíveis com HTTPS.  

Você pode desabilitar a criptografia em trânsito para uma conta de armazenamento do Azure. Quando a criptografia é desativada, o Azure Files também permitirá smb 2.1, SMB 3.0 sem criptografia e chamadas de API FileREST não criptografadas por HTTP. O principal motivo para desabilitar a criptografia em trânsito é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux. Os Arquivos do Azure só permitem conexões SMB 2.1 na mesma região do Azure que o compartilhamento de arquivo do Azure. Um cliente SMB 2.1 fora da região do Azure do compartilhamento de arquivo do Azure, como local ou em uma região diferente do Azure, não será capaz de acessar o compartilhamento de arquivo.

Recomendamos fortemente garantir que a criptografia de dados em trânsito seja ativada.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Criptografia em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

Em geral, os recursos do Azure Files e a interoperabilidade com outros serviços são os mesmos entre compartilhamentos de arquivos premium e compartilhamentos de arquivos padrão, no entanto, existem algumas diferenças importantes:
- **Modelo de cobrança**
    - As ações de arquivos premium são cobradas usando um modelo de faturamento provisionado, o que significa que você paga por quanto de armazenamento você provisão, em vez de quanto armazenamento você realmente pede. 
    - As ações de arquivo padrão são cobradas usando um modelo pay-as-you-go, que inclui um custo base de armazenamento para quanto armazenamento você está realmente consumindo e, em seguida, um custo adicional de transação com base em como você usa o compartilhamento. Com as partes padrão de arquivo, sua conta aumentará se você usar (ler/gravar/montar) o compartilhamento de arquivos Azure mais.
- **Opções de redundância**
    - Os compartilhamentos de arquivos premium estão disponíveis apenas para armazenamento localmente redundante (LRS) e zona redundante (ZRS). 
    - Os compartilhamentos de arquivos padrão estão disponíveis para armazenamento localmente redundante, redundante de zona, geo-redundante (GRS) e geo-zona redundante (GZRS).
- **Tamanho máximo do compartilhamento de arquivos**
    - As ações de arquivos premium podem ser provisionadas para até 100 TiB sem qualquer trabalho adicional.
    - Por padrão, as ações de arquivo padrão podem se estender apenas até 5 TiB, embora o limite de compartilhamento possa ser aumentado para 100 TiB, optando pelo grande sinalizador de recurso de armazenamento *de compartilhamento de arquivos.* Os compartilhamentos de arquivos padrão só podem abranger até 100 TiB para contas de armazenamento redundantes localmente redundantes ou de zona. Para obter mais informações sobre o aumento do tamanho do compartilhamento de arquivos, consulte [Ativar e criar grandes compartilhamentos de arquivos](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share).
- **Disponibilidade regional**
    - Os compartilhamentos de arquivos premium não estão disponíveis em todas as regiões, e o suporte redundante de zona está disponível em um subconjunto menor de regiões. Para saber se as ações de arquivos premium estão disponíveis atualmente em sua região, consulte os [produtos disponíveis pela](https://azure.microsoft.com/global-infrastructure/services/?products=storage) página da região para o Azure. Para descobrir quais regiões suportam zRS, consulte [O suporte da Zona de Disponibilidade do Azure por região](../../availability-zones/az-overview.md#services-support-by-region). Para nos ajudar a priorizar novas regiões e recursos de nível premium, preencha esta [pesquisa](https://aka.ms/pfsfeedback).
    - Os compartilhamentos de arquivos padrão estão disponíveis em todas as regiões do Azure.
- O Azure Kubernetes Service (AKS) suporta ações de arquivos premium na versão 1.13 e posterior.

Uma vez que um compartilhamento de arquivo é criado como um prêmio ou um compartilhamento de arquivo padrão, você não pode convertê-lo automaticamente para o outro nível. Se você quiser mudar para o outro nível, você deve criar um novo compartilhamento de arquivos nesse nível e copiar manualmente os dados do seu compartilhamento original para o novo compartilhamento que você criou. Recomendamos `robocopy` o uso `rsync` para Windows ou para macOS e Linux para executar essa cópia.

### <a name="understanding-provisioning-for-premium-file-shares"></a>Entendendo o provisionamento de ações de arquivos premium
Os compartilhamentos de arquivos Premium são provisionados com base em uma taxa de GiB/IOPS/transferência fixa. Para cada GiB provisionado, o compartilhamento emitirá um IOPS e uma taxa de transferência de 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB, com um IOPS e uma taxa de transferência mínimos.

Com base no melhor esforço, todos os compartilhamentos poderão acumular até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Os novos compartilhamentos começam com o crédito de intermitência completa com base na capacidade provisionada.

As ações devem ser provisionadas em incrementos de 1 GiB. O tamanho mínimo é de 100 GiB, o próximo tamanho é de 101 GiB e assim por diante.

> [!TIP]
> IOPS de linha de base = 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de estouro = 3 * IOPS de linha de base. (Até um máximo de 100.000 IOPS).
>
> Taxa de saída = 60 MiB/s + 0,06 * GiB provisionado
>
> taxa de ingresss = 40 MiB/s + 0,04 * GiB provisionado

O tamanho das ações provisionadas é especificado por cota de ações. A cota de ações pode ser aumentada a qualquer momento, mas só pode ser diminuída após 24 horas desde o último aumento. Depois de esperar 24 horas sem um aumento de cota, você pode diminuir a cota de ações quantas vezes quiser, até que você aumente novamente. As alterações na escala IOPS/Throughput serão efetivas dentro de alguns minutos após a alteração de tamanho.

É possível diminuir o tamanho da sua parte provisionada abaixo do seu GiB usado. Se você fizer isso, você não perderá dados, mas, você ainda será cobrado pelo tamanho usado e receberá o desempenho (IOPS de linha de base, throughput e IOPS estourado) do compartilhamento provisionado, não do tamanho usado.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos de ações provisionados:

|Capacidade (GiB) | IOPS de linha de base | IOPS de estouro | Egresso (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1.024       | 1.024   | Até 3.072   | 122   | 81   |
|5.120       | 5.120   | Até 15.360  | 368   | 245   |
|10,240      | 10,240  | Até 30.720  | 675 | 450   |
|33,792      | 33,792  | Até 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Até 100.000 | 3.132 | 2,088   |
|102,400     | 100.000 | Até 100.000 | 6,204 | 4,136   |

> [!NOTE]
> O desempenho dos compartilhamentos de arquivos está sujeito aos limites da rede da máquina, largura de banda de rede disponível, tamanhos de IO, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com 8 tamanhos de IO de leitura/gravação de 8 KiB, uma única máquina virtual do Windows, *Standard F16s_v2,* conectada ao compartilhamento de arquivos premium sobre SMB poderia alcançar 20K ler IOPS e 15K gravar IOPS. Com os tamanhos de IO de leitura/gravação de 512 MiB, o mesmo VM poderia alcançar 1,1 GiB/s egresso e 370 MiB/s de throughput. Para obter a escala máxima de desempenho, espalhe a carga em várias VMs. Consulte o [guia de solução de problemas](storage-troubleshooting-files-performance.md) para alguns problemas de desempenho comuns e alternativas.

#### <a name="bursting"></a>Estourando
As ações de arquivos premium podem estourar seu IOPS até um fator de três. O estouro é automatizado e opera com base em um sistema de crédito. Estouro funciona com o melhor esforço e o limite de estouro não é uma garantia, as ações de arquivo podem *estourar até* o limite.

Os créditos se acumulam em um balde estourado sempre que o tráfego para o seu compartilhamento de arquivos está abaixo do IOPS da linha de base. Por exemplo, uma ação de 100 GiB tem 100 IOPS de linha de base. Se o tráfego real na ação foi de 40 IOPS para um intervalo específico de 1 segundo, então os 60 IOPS não utilizados são creditados a um balde estourado. Esses créditos serão usados mais tarde quando as operações excederem os IOPs de linha de base.

> [!TIP]
> Tamanho do balde de estouro = IOPS de linha de base * 2 * 3600.

Sempre que uma ação excede o IOPS de linha de base e tem créditos em um balde estourado, ele vai estourar. As ações podem continuar a estourar enquanto os créditos permanecerem, embora as ações menores que 50 TiB só fiquem no limite de estouro por até uma hora. Ações maiores que 50 TiB podem tecnicamente exceder esse limite de uma hora, até duas horas, mas, isso se baseia no número de créditos estourados acumulados. Cada IO além da linha de base do IOPS consome um crédito e uma vez que todos os créditos são consumidos, a ação retornaria ao IOPS de linha de base.

Os créditos de ações têm três estados:

- Acumulando, quando o compartilhamento de arquivos está usando menos do que o IOPS de linha de base.
- Em declínio, quando a parte do arquivo está estourando.
- Permanecendo constante, quando não há créditos ou IOPS de linha de base estão em uso.

Novas ações de arquivo começam com o número total de créditos em seu balde estourado. Os créditos de estouro não serão acumulados se o IOPS de ação cair abaixo do IOPS de linha de base devido ao estrangulamento pelo servidor.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Permitir que as ações de arquivo padrão abrangem até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migração
Em muitos casos, você não estará estabelecendo um novo compartilhamento de arquivos líquido para sua organização, mas, em vez disso, migrando um compartilhamento de arquivos existente de um servidor de arquivos local ou dispositivo NAS para Arquivos Azure. Existem muitas ferramentas, fornecidas tanto pela Microsoft quanto por terceiros, para fazer uma migração para um compartilhamento de arquivos, mas elas podem ser divididas em duas categorias:

- **Ferramentas que mantêm atributos do sistema de arquivos, como ACLs e carimbos de tempo:**
    - **[Azure File Sync](storage-sync-files-planning.md)**: Azure File Sync pode ser usado como um método para ingerir dados em um compartilhamento de arquivos Azure, mesmo quando a implantação final desejada não é para manter uma presença no local. O Azure File Sync pode ser instalado no local nas implantações existentes do Windows Server 2012 R2, Windows Server 2016 e Windows Server 2019. Uma vantagem de usar o Azure File Sync como mecanismo de ingestão é que os usuários finais podem continuar a usar o compartilhamento de arquivos existente no local; o corte para o compartilhamento de arquivos Do Zure pode ocorrer depois que todos os dados terminarem de fazer upload em segundo plano.
    - **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: Robocopy é uma ferramenta de cópia bem conhecida que é navegada com Windows e Windows Server. Robocopy pode ser usado para transferir dados para arquivos do Azure montando o compartilhamento de arquivos localmente e, em seguida, usando a localização montada como o destino no comando Robocopy.

- **Ferramentas que não mantêm atributos do sistema de arquivos:**
    - **Data Box**: Data Box fornece um mecanismo de transferência de dados off-line para dados de navios físicos no Azure. Este método foi projetado para aumentar o throughput e salvar a largura de banda, mas atualmente não suporta atributos do sistema de arquivos, como carimbos de tempo e ACLs.
    - **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: o AzCopy é um utilitário de linha de comando projetado para copiar dados de e para os Arquivos do Azure e o Armazenamento de Blobs do Azure, usando comandos simples com o desempenho ideal.

## <a name="next-steps"></a>Próximas etapas
* [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando Arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
