---
title: Link privado para a API do Azure para FHIR
description: Este artigo descreve como configurar um ponto de extremidade privado para a API do Azure para serviços FHIRs
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: 121ddcc013fbeef29c6d22cfbd273cfd3b8d3d98
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558847"
---
# <a name="configure-private-link"></a>Configurar link privado

> [!IMPORTANT]
> Essa versão preliminar está na versão prévia pública, e é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho em produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O link privado permite que você acesse a API do Azure para FHIR em um ponto de extremidade privado, uma interface de rede que conecta você de forma privada e segura usando um endereço IP privado de sua rede virtual. Com o link privado, você pode acessar nossos serviços com segurança de sua vnet como um serviço de terceiros sem precisar passar por um DNS público. Este artigo explica como criar, testar e gerenciar seu ponto de extremidade privado para a API do Azure para FHIR.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um ponto de extremidade privado, há alguns recursos do Azure que você precisará criar primeiro:

- Grupo de recursos – o grupo de recursos do Azure que conterá a rede virtual e o ponto de extremidade privado.
- API do Azure para FHIR – o recurso FHIR que você gostaria de colocar por trás de um ponto de extremidade privado.
- Rede virtual – a VNet à qual os serviços de cliente e o ponto de extremidade privado serão conectados.

Para obter mais informações, confira a [documentação do link privado](https://docs.microsoft.com/azure/private-link/).

## <a name="disable-public-network-access"></a>Desabilitar o acesso de redes públicas

A criação de um ponto de extremidade privado para o recurso FHIR não desabilita automaticamente o tráfego público para ele. Para fazer isso, você precisará atualizar seu recurso FHIR para definir uma nova propriedade "acesso público" de "Enabled" para "Disabled". Tenha cuidado ao desabilitar o acesso à rede pública, pois todas as solicitações para o serviço FHIR que não estão vindo de um ponto de extremidade privado configurado corretamente serão negadas. Somente o tráfego dos pontos de extremidade privados será permitido.

![Desabilitar o acesso à rede pública](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Criar ponto de extremidade privado

Para criar um ponto de extremidade privado, um desenvolvedor com permissões de RBAC no recurso FHIR pode usar portal do Azure, [Azure PowerShell](https://docs.microsoft.com/azure/private-link/create-private-endpoint-powershell)ou [CLI do Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli). Este artigo orienta você pelas etapas sobre como usar portal do Azure. O uso de portal do Azure é recomendado, pois automatiza a criação e a configuração da zona de DNS privado. Você pode fazer referência ao [link privado início rápido guias](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal) para obter mais detalhes.

Há duas maneiras de criar um ponto de extremidade privado. O fluxo de aprovação automática permite que um usuário que tem permissões de RBAC no recurso FHIR crie um ponto de extremidade privado sem a necessidade de aprovação. O fluxo de aprovação manual permite que um usuário sem permissões no recurso FHIR solicite que um ponto de extremidade privado seja aprovado pelos proprietários do recurso FHIR.

### <a name="auto-approval"></a>Aprovação automática

Verifique se a região do novo ponto de extremidade privado é a mesma da região da sua rede virtual. A região para o recurso FHIR pode ser diferente.

![Guia noções básicas do portal do Azure](media/private-link/private-link-portal2.png)

Para tipo de recurso, pesquise e selecione "Microsoft. HealthcareApis/Services". Para recurso, selecione o recurso FHIR. Para o subrecurso de destino, selecione "fhir".

![portal do Azure guia de recursos](media/private-link/private-link-portal1.png)

Se você não tiver uma configuração de zona de DNS privado existente, selecione "(New) privatelink. azurehealthcareapis. com". Se você já tiver sua zona de DNS privado configurada, poderá selecioná-la na lista. Ele deve estar no formato "privatelink.azurehealthcareapis.com".

![Guia de configuração do portal do Azure](media/private-link/private-link-portal3.png)

Depois que a implantação for concluída, você poderá voltar para a guia "conexões de ponto de extremidade particulares", na qual você verá "aprovado" como o estado de conexão.

### <a name="manual-approval"></a>Aprovação manual

Para aprovação manual, selecione a segunda opção em recurso, "conectar-se a um recurso do Azure por ID de recurso ou alias". Para o subrecurso de destino, insira "fhir" como em aprovação automática.

![Aprovação manual](media/private-link/private-link-manual.png)

Depois que a implantação for concluída, você poderá voltar para a guia "conexões de ponto de extremidade particulares", na qual você pode aprovar, rejeitar ou remover sua conexão.

![Opções](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Ponto de extremidade particular de teste

Para certificar-se de que o servidor FHIR não está recebendo tráfego público depois de desabilitar o acesso à rede pública, tente acessar o ponto de extremidade/Metadata para o servidor do seu computador. Você deve receber um 403 Proibido. Observe que pode levar até 5 minutos após a atualização do sinalizador de acesso à rede pública antes que o tráfego público seja bloqueado.

Para garantir que seu ponto de extremidade privado possa enviar tráfego para o servidor:

1. Crie uma VM que esteja conectada à rede virtual e à sub-rede em que seu ponto de extremidade privado está configurado. Para garantir que o tráfego da VM esteja usando apenas a rede privada, você pode desabilitar o tráfego de Internet de saída por meio da regra NSG.
2. RDP na VM.
3. Tente pressionar o ponto de extremidade/Metadata do seu servidor FHIR da VM, você deverá receber a declaração de capacidade como uma resposta.

## <a name="manage-private-endpoint"></a>Gerenciar o ponto de extremidade privado

### <a name="view"></a>Visualizar

Pontos de extremidade privados e a NIC associada são visíveis no portal do Azure do grupo de recursos em que foram criados.

![Exibir em recursos](media/private-link/private-link-view.png)

### <a name="delete"></a>Excluir

Os pontos de extremidade privados só podem ser excluídos de portal do Azure por meio da folha de visão geral (como abaixo) ou por meio da opção Excluir na guia "conexões de ponto de extremidade privadas" da rede (visualização). Clicar no botão excluir excluirá o ponto de extremidade privado e a NIC associada. Se você excluir todos os pontos de extremidade privados para o recurso FHIR e o acesso à rede pública estiver desabilitado, nenhuma solicitação irá torná-lo ao servidor FHIR. Todos os pontos de extremidade privados devem ser excluídos do recurso FHIR para que o recurso FHIR possa ser excluído ou movido.

![Excluir ponto de extremidade privado](media/private-link/private-link-delete.png)
