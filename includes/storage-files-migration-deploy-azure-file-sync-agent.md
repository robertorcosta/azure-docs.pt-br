---
title: Implantando o agente de Sincronização de Arquivos do Azure
description: Implantando o agente de Sincronização de Arquivos do Azure. Um bloco de texto comum, compartilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209409"
---
Nesta seção, você instalará o agente de Sincronização de Arquivos do Azure no seu servidor do Windows.
O [Guia de implantação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que você precisa desativar a **segurança aprimorada do IE**. A segurança aprimorada do IE é uma medida de segurança que não é aplicável com Sincronização de Arquivos do Azure e desligá-la permite que você se autentique no Azure sem problemas.

Abra o PowerShell e instale os módulos do PowerShell necessários com os comandos a seguir. Certifique-se de instalar o módulo completo e o provedor do NuGet quando solicitado:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se você tiver problemas para acessar a Internet do seu servidor, agora é o momento de solucioná-los. Sincronização de Arquivos do Azure usa qualquer conexão de rede disponível com a Internet.
Também há suporte para exigir um servidor proxy para acessar a Internet. Você pode configurar um proxy de todo o computador agora ou especificar um proxy que apenas a sincronização de arquivos usará durante a instalação do agente.

Se isso significa que você precisa abrir seus firewalls para esse servidor, essa pode ser uma abordagem aceitável para você. No final da instalação do servidor, após o registro do servidor concluído, haverá um relatório de conectividade de rede mostrando as URLs exatas do ponto de extremidade no Azure, que a sincronização de arquivo precisa para se comunicar com a região que você selecionou. O relatório também informa o motivo pelo qual a comunicação é necessária. Você pode usar o relatório para bloquear os firewalls em todo esse servidor, para URLs específicas.

Você também pode seguir uma abordagem mais conservadora, na qual você não abre os firewalls, mas, em vez disso, limita o servidor a se comunicar com espaços de nome DNS de nível superior – há mais documentação e detalhes disponíveis no artigo [sincronização de arquivos do Azure proxy e configurações de firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) . Siga suas próprias práticas recomendadas de rede.

No final do assistente de *instalação* do servidor, um assistente de *registro* do servidor será exibido.
Registre o servidor no recurso do Azure do serviço de sincronização de armazenamento anteriormente.

Essas etapas são descritas mais detalhadamente no guia de implantação, incluindo os módulos do PowerShell acima que devem ser instalados primeiro: [instalação do sincronização de arquivos do Azure Agent](../articles/storage/files/storage-sync-files-deployment-guide.md).

O agente mais recente deve ser usado e pode ser baixado do centro de download da Microsoft: [sincronização de arquivos do Azure-Agent](https://aka.ms/AFS/agent "Download do agente de Sincronização de Arquivos do Azure").

Após uma instalação bem-sucedida e um registro de servidor, você pode verificar se concluiu com êxito esta etapa: Navegue até o recurso de serviço de sincronização de armazenamento no portal do Azure, em seguida, siga o menu à esquerda para "servidores registrados". Você verá o servidor listado aqui imediatamente.
