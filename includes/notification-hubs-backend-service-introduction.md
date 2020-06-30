---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 98c9d83424e7d347bb6e1166a3b00304d39e0cce
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095100"
---
Um back-end de [API Web do ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) é usado para manipular o [registro do dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) para o cliente usando a melhor e mais recente abordagem de [Instalação](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#installations). O serviço também enviará notificações por push de maneira multiplataforma. 

Essas operações são manipuladas usando o [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Mais detalhes sobre a abordagem geral são fornecidos na documentação [Registrar usando o back-end do aplicativo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#registration-management-from-a-backend).
