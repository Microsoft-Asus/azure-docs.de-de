---
title: Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview
description: Leitfaden zum Ermitteln von Daten, Verbinden der Daten und Untersuchen der Daten in Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 7c6b25fd3615fa76bc76e6d360f4c76a21a9ad02
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97917979"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Entdecken, Verbinden und Untersuchen von Daten in Synapse mithilfe von Azure Purview 

> [!IMPORTANT]
> Die Integration zwischen Azure Synapse Analytics und Azure Purview befindet sich derzeit in der Vorschauphase. Wenn Sie Azure Purview in Synapse ausprobieren möchten, wenden Sie sich an einen Microsoft-Vertriebsmitarbeiter. 

In diesem Dokument erfahren Sie, welche Interaktionen Sie durchführen können, wenn Sie ein Azure Purview-Konto in Synapse registrieren. 

## <a name="prerequisites"></a>Voraussetzungen 

- [Azure Purview-Konto](../../purview/create-catalog-portal.md) 
- [Synapse-Arbeitsbereich](../quickstart-create-workspace.md) 
- [Verbinden eines Azure Purview-Kontos mit Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Verwenden von Azure Purview in Synapse 

Für die Verwendung von Azure Purview in Synapse müssen Sie Zugriff auf das Purview-Konto haben. Synapse übergibt Ihre Purview-Berechtigung. Wenn Sie z. B. über eine Rolle mit Kuratorberechtigung verfügen, können Sie von Azure Purview gescannte Metadaten bearbeiten. 

### <a name="data-discovery-search-datasets"></a>Datenermittlung: Durchsuchen von Datasets 

Um von Azure Purview registrierte und gescannte Daten zu ermitteln, können Sie die Suchleiste oben in der Mitte des Synapse-Arbeitsbereichs verwenden. Stellen Sie sicher, dass Sie Azure Purview auswählen, um nach allen ihren Organisationsdaten zu suchen. 

## <a name="azure-purview-actions"></a>Azure Purview-Aktionen 

In Synapse sind die folgenden Azure Purview-Features verfügbar: 
- **Übersicht** über die Metadaten 
- Anzeigen und Bearbeiten des **Schemas** der Metadaten mit Klassifizierungen, Glossarbegriffen, Datentypen und Beschreibungen 
- Anzeigen der **Herkunft**, um Abhängigkeiten zu verstehen und eine Auswirkungsanalyse durchzuführen. Weitere Informationen finden Sie unter [Herkunft](../../purview/catalog-lineage-user-guide.md).
- Anzeigen und Bearbeiten von **Kontakten**, um zu wissen, wer Besitzer oder Experte eines Datasets ist 
- **Verknüpft** erleichtert das Verständnis der hierarchischen Abhängigkeiten eines bestimmten Datasets. Diese Elemente der Benutzeroberfläche erleichtern das Durchsuchen der Datenhierarchie.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Aktionen, die Sie für Datasets mit Synapse-Ressourcen ausführen können 

### <a name="connect-data-to-synapse"></a>Verbinden von Daten mit Synapse 

- Sie können einen **neuen mit Synapse verknüpften Dienst** erstellen. Diese Aktion ist erforderlich, um Daten in Synapse zu kopieren oder in den Datenhub einzufügen (für unterstützte Datenquellen wie ADLSg2). 
- Für Objekte wie Dateien, Ordner oder Tabellen können Sie direkt ein **neues Integrationsdataset** erstellen und einen vorhandenen verknüpften Dienst nutzen, sofern er bereits erstellt wurde. 

Wir können noch nicht ableiten, ob ein verknüpfter Dienst oder ein verknüpftes Integrationsdataset vorhanden ist. 

###  <a name="develop-in-synapse"></a>Entwickeln in Synapse 

Sie können drei Aktionen ausführen: **Neues SQL-Skript**, **Neues Notebook** und **Neuer Datenfluss**. 

Mit **Neues SQL-Skript** können Sie je nach Typ der Unterstützung folgende Aktionen ausführen: 
- Anzeigen der obersten 100 Zeilen, um die Form der Daten zu verstehen 
- Erstellen einer externen Tabelle aus der Synapse-SQL-Datenbank 
- Laden der Daten in eine Synapse SQL-Datenbank 
 
Mit **Neues Notebook** können Sie folgende Aktionen ausführen: 
- Laden von Daten in einen Spark-Datenrahmen 
- Erstellen einer Spark-Tabelle (wenn Sie hierfür das Parquet-Format verwenden, wird auch eine Tabelle in einem serverlosen SQL-Pool erstellt) 
 
Mit **Neuer Datenfluss** können Sie ein Integrationsdataset erstellen, das als Quelle in einer Datenflusspipeline verwendet werden kann. Der Datenfluss ist eine Entwicklerfunktion ohne Code zum Ausführen von Datentransformationen. Weitere Informationen zur [Verwendung eines Datenflusses in Synapse](../quickstart-data-flow.md)

##  <a name="nextsteps"></a>Nächste Schritte 

- [Registrieren und Überprüfen von Azure Synapse Analytics](../../purview/register-scan-azure-synapse-analytics.md)
- [Suchen im Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)