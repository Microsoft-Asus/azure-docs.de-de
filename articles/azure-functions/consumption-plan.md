---
title: Hosting des Azure Functions-Verbrauchstarifs
description: Erfahren Sie, wie Sie mit dem Hosting des Azure Function-Verbrauchstarifs Ihren Code in einer Umgebung ausführen können, die dynamisch skaliert wird, wobei Sie nur für die während der Ausführung genutzten Ressourcen bezahlen.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: c0619def4687935cd9e403563966b35b84f13c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937524"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Hosting des Azure Functions-Verbrauchstarifs

Bei Verwendung des Verbrauchstarifs werden Instanzen des Azure Functions-Hosts dynamisch basierend auf der Anzahl der eingehenden Ereignisse hinzugefügt und entfernt. Der Verbrauchstarif ist die vollständig <em>serverlose</em> Hostingoption für Azure Functions.

## <a name="benefits"></a>Vorteile

Der Verbrauchstarif wird automatisch skaliert, auch in Zeiten hoher Auslastung. Wenn Funktionen in einem Verbrauchstarif ausgeführt werden, werden Ihnen nur dann Computeressourcen berechnet, wenn Ihre Funktionen ausgeführt werden. In einem Verbrauchsplan tritt für eine Funktionsausführung nach einem konfigurierbaren Zeitraum ein Timeout auf.

Einen Vergleich des Verbrauchstarifs mit anderen Plänen und Hostingtypen finden Sie unter [Funktionsskalierung und Hostingoptionen](functions-scale.md).

## <a name="billing"></a>Abrechnung

Die Abrechnung erfolgt auf der Grundlage der Anzahl von Ausführungen, der Ausführungszeit und des verwendeten Arbeitsspeichers. Die Nutzung wird für alle Funktionen innerhalb einer Funktions-App aggregiert. Weitere Informationen finden Sie unter [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

Weitere Informationen zum Schätzen der Kosten bei Ausführung in einem Verbrauchstarif finden Sie unter [Grundlegendes zu Kosten des Verbrauchstarifs](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Erstellen Sie einer Funktions-App in einem Verbrauchstarif

Wenn Sie eine Funktions-App im Azure-Portal erstellen, gilt standardmäßig der Verbrauchstarif. Wenn Sie APIs verwenden, um Ihre Funktions-App zu erstellen, müssen Sie nicht zuerst einen App Service-Plan erstellen, wie es bei Premium- und Dedicated-Tarifen der Fall ist.

Verwenden Sie die folgenden Links, um zu erfahren, wie Sie eine serverlose Funktions-App in einem Verbrauchstarif erstellen, entweder programmgesteuert oder im Azure-Portal:

+ [Azure-Befehlszeilenschnittstelle](./scripts/functions-cli-create-serverless.md)
+ [Azure portal](functions-create-first-azure-function.md)
+ [Azure Resource Manager-Vorlage](functions-create-first-function-resource-manager.md)

Sie können Funktions-Apps auch in einem Verbrauchstarif erstellen, wenn Sie ein Functions-Projekt aus [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) oder [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure) veröffentlichen.

## <a name="multiple-apps-in-the-same-plan"></a>Mehrere Apps im gleichen Tarif

Funktions-Apps in derselben Region können demselben Verbrauchsplan zugewiesen werden. Das Ausführen mehrerer Apps im selben Verbrauchsplan hat keine Nachteile oder Auswirkungen. Das Zuweisen mehrerer Apps zum selben Verbrauchsplan hat keine Auswirkungen auf die Stabilität, Skalierbarkeit oder Zuverlässigkeit der einzelnen Apps.

## <a name="next-steps"></a>Nächste Schritte

+ [Azure Functions-Hostingoptionen](functions-scale.md)
+ [Ereignisgesteuerte Skalierung in Azure Functions](event-driven-scaling.md)
