---
title: Verbinden von Azure Defender für IoT mit Azure Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Daten aus Azure Defender (vormals Azure Security Center) für IoT mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631440"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Verknüpfen Ihrer Daten aus Azure Defender (vormals Azure Security Center) für IoT mit Azure Sentinel 


> [!IMPORTANT]
> Der Defender für IoT-Datenconnector befindet sich derzeit in der Public Preview-Phase. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem Defender für IoT-Connector können Sie alle Ihre Ereignisse aus Defender für IoT an Azure Sentinel streamen. 

Diese Integration ermöglicht Organisationen das schnelle Erkennen mehrstufiger Angriffe, die oft IT- und OT-Grenzen überschreiten. Darüber hinaus ermöglicht die Integration von Defender für IoT mit den SOAR-Funktionen (Security Orchestration, Automation and Response; Sicherheitsorchestrierung, Automatisierung und Reaktion) von Azure Sentinel eine automatisierte Reaktion und Prävention mithilfe von integrierten OT-optimierten Playbooks. 
## <a name="prerequisites"></a>Voraussetzungen

- **Lese-** und **Schreibberechtigungen** für den Arbeitsbereich, in dem Azure Sentinel bereitgestellt wird.
- **Defender für IoT** muss für Ihre relevanten IoT Hub-Instanzen **aktiviert** sein.
- Sie müssen über die Berechtigungen der Rolle **Mitwirkender** für das **Abonnement** verfügen, für das Sie eine Verbindung herstellen möchten.

## <a name="connect-to-defender-for-iot"></a>Verbinden mit Defender für IoT

1. Klicken Sie in Azure Sentinel auf **Data connectors** (Datenconnectors), und wählen Sie dann im Katalog **Defender für IoT** aus. Möglicherweise heißt diese Option noch „Azure Security Center für IoT“.

1. Klicken Sie unten im rechten Bereich auf **Connectorseite öffnen**. 

1. Klicken Sie neben jedem IoT Hub-Abonnement, dessen Warnungen und Gerätewarnungen Sie in Azure Sentinel streamen möchten, auf **Verbinden**. 
    - Wenn Defender für IoT nicht für mindestens eine IoT Hub-Instanz in einem Abonnement aktiviert ist, wird eine Fehlermeldung angezeigt. Aktivieren Sie in der IoT Hub-Instanz Defender für IoT, um diesen Fehler zu beheben.

1. Sie können entscheiden, ob die Warnungen von Defender für IoT automatisch Incidents in Azure Sentinel generieren sollen. Wählen Sie unter **Create incidents** (Incidents erstellen) **Aktivieren** aus, um die Standardanalyseregel zur automatischen Erstellung von Incidents aus den generierten Warnungen zu aktivieren. Diese Regel kann unter **Analytics** > **Active rules** (Analyse > Aktive Regeln) geändert oder bearbeitet werden.

> [!NOTE]
> Das Aktualisieren der **Abonnementliste** nach Verbindungsänderungen kann 10 Sekunden oder länger dauern. 

## <a name="log-analytics-alert-view"></a>Log Analytics-Warnungsansicht

So verwenden Sie das relevante Schema in Log Analytics, um die Azure Defender für IoT-Benachrichtigungen anzuzeigen:

1. Öffnen Sie **Logs** > **SecurityInsights** > **SecurityAlert**, oder suchen Sie nach **SecurityAlert**. 

2. Filtern Sie mithilfe des folgenden KQL-Filters, um nur die von Defender für IoT generierten Benachrichtigungen anzuzeigen:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Diensthinweise

Die Hubdaten stehen ungefähr 15 Minuten nach dem Herstellen einer Verbindung mit einem **Abonnement** in Azure Sentinel zur Verfügung.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Defender für IoT mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
