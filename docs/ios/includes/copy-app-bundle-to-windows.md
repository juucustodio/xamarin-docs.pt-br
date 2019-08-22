---
ms.openlocfilehash: 22a8542c0e829db8b889abc2c7fe5f5ab9d19ed4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527644"
---

Ao criar aplicativos iOS no Visual Studio e o agente de build do Mac, o pacote do .app não será copiado de novo no computador Windows. As ferramentas Xamarin para Visual Studio 7.4 adicionam uma nova propriedade `CopyAppBundle` que permite que builds de CI copiem pacotes do .app para o Windows.

Para usar essa funcionalidade, adicione a propriedade `CopyAppBundle` ao .csproj no grupo de propriedades para o qual você deseja aplicar essa funcionalidade. Por exemplo, o exemplo a seguir mostra como copiar o pacote do .app de volta para o computador Windows para um build de **Depuração** direcionado para o **iPhoneSimulator**:

```xml
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
    <CopyAppBundle>true</CopyAppBundle>
</PropertyGroup>
```
