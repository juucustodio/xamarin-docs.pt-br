---
title: watchOS complicações no Xamarin
description: Este documento descreve como trabalhar com o watchOS complicações no Xamarin. Ele discute como adicionar um complicativo, escrevendo uma complicação, modelos e fornece código de exemplo.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61225503"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS complicações no Xamarin

_watchOS permite aos desenvolvedores escrever a complicações personalizadas para mostradores de relógio_

Esta página explica os diferentes tipos de complicações disponíveis e como adicionar um complicativo para seu aplicativo do watchOS 3.

Observe que cada aplicativo watchOS só pode ter uma complicação.

Comece lendo [documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) para determinar se seu aplicativo é adequado para uma complicação. Há 5 `CLKComplicationFamily` tipos de exibição à sua escolha:

[![](complications-images/all-complications-sml.png "Os 5 tipos CLKComplicationFamily disponíveis: Pequeno circular, Modular pequeno, pequeno Modular de grande e utilitárias, utilitárias grande")](complications-images/all-complications.png#lightbox)

Aplicativos podem implementar apenas um estilo ou todas as cinco, dependendo dos dados que está sendo exibidos.
Você também pode dar suporte a tempo de viagem, fornecendo valores para futuros e/ou últimas vezes conforme o usuário virar o Crown Digital.

<a name="adding" />

## <a name="adding-a-complication"></a>Adicionando uma complicação

### <a name="configuration"></a>Configuração

Complicações podem ser adicionadas a um aplicativo de inspeção de variáveis durante a criação ou adicionadas manualmente a uma solução existente.

### <a name="add-new-project"></a>Adicione novo projeto...

O **adicionar novo projeto...**  assistente inclui uma caixa de seleção que criará automaticamente uma classe de controlador de complicação e configurar o **Info. plist** arquivo:

![](complications-images/file-new-project-sml.png "A caixa de seleção incluir complicação")

### <a name="existing-projects"></a>Projetos existentes

Para adicionar um complicativo a um projeto existente:

1. Criar um novo **ComplicationController.cs** arquivo de classe e implementar `CLKComplicationDataSource`.
2. Configurar o aplicativo **Info. plist** para expor a complicação e há suporte para quais famílias de complicação de identidade.

Essas etapas são descritas em mais detalhes abaixo.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

O seguinte C# modelo inclui os métodos mínimos necessários para implementar uma `CLKComplicationDataSource`.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

Siga as [escrevendo uma complicação](#writing) instruções para adicionar código para essa classe.

### <a name="infoplist"></a>Info.plist

A extensão de inspeção **Info. plist** arquivo deve especificar o nome da `CLKComplicationDataSource` e quais famílias de complicação que você deseja dar suporte:

[![](complications-images/complications-config-sml.png "Os tipos de família de complicação")](complications-images/complications-config.png#lightbox)

O **classe de fonte de dados** lista de entrada mostrará os nomes de classe que a subclasse `CLKComplicationDataSource` subclasse que inclui sua lógica de complicação.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Todas as funcionalidades de complicação é implementada em uma única classe, substituindo os métodos do `CLKComplicationDataSource` classe abstrata (que implementa o `ICLKComplicationDataSource` interface).

### <a name="required-methods"></a>Métodos necessários

Você deve implementar os seguintes métodos para a complicação executar:

- `GetPlaceholderTemplate` -A exibição estática usada durante a configuração ou quando o aplicativo não pode fornecer um valor de retorno.
- `GetCurrentTimelineEntry` – Calcule a exibição correta quando a complicação está em execução.
- `GetSupportedTimeTravelDirections` -Retorna as opções da `CLKComplicationTimeTravelDirections` , como `None`, `Forward`, `Backward`, ou `Forward | Backward`.

### <a name="privacy"></a>Privacidade

Complicações que exibem dados pessoais

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` Ou `HideOnLockScreen`

Se esse método retornar `HideOnLockScreen` e em seguida, a complicação mostrará a um ícone ou o nome do aplicativo (e não qualquer dado) quando o relógio está bloqueado.

### <a name="updates"></a>Atualizações

- `GetNextRequestedUpdateDate` – Retorne um tempo quando o sistema operacional lado deve consultar o aplicativo para exibir dados de complicação atualizado.

Você também pode forçar uma atualização de seu aplicativo iOS.

### <a name="supporting-time-travel"></a>Suporte a tempo de viagem

Suporte de viagem de tempo é opcional e é controlado pelo `GetSupportedTimeTravelDirections` método. Se ele retornar `Forward`, `Backward`, ou `Forward | Backward` e em seguida, você deve implementar os métodos a seguir

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Escrevendo uma complicação

Exibir variam de complicações de dados simples para imagem complicada e renderização de dados com suporte de tempo de viagem. O código a seguir mostra como criar uma complicação simple, o único modelo.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Código de exemplo

Este exemplo só oferece suporte a `UtilitarianLarge` modelo, portanto, só pode ser selecionado no mostradores de relógio específicos que dão suporte a esse tipo de complicação. Quando *selecionando* complicações em uma inspeção, ele exibe **COMPLICAÇÃO meu** e quando *executando* ele exibirá o texto **minuto _hora_**   (com uma parte do tempo).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Modelos de complicação

Há vários modelos diferentes para cada estilo de complicação.
O **anel** modelos permitem que você exiba um anel de progresso-style em torno de complicação, que pode ser usado para exibir graficamente o andamento ou algum outro valor.

[Documentos de CLKComplicationTemplate da Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Pequeno circular

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateCircularSmall`:

- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **SimpleImage** -exibem apenas uma única imagem pequena.
- **SimpleText** -exibem apenas um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, um acima do outro
- **StackText** -exibir duas linhas de texto.

### <a name="modular-small"></a>Modular pequeno

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -exibir uma pequena grade com valores de texto (2 linhas e 2 colunas).
- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **SimpleImage** -exibem apenas uma única imagem pequena.
- **SimpleText** -exibem apenas um pequeno trecho de texto.
- **StackImage** -exibir uma imagem e uma linha de texto, um acima do outro
- **StackText** -exibir duas linhas de texto.

### <a name="modular-large"></a>Modular grande

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateModularLarge`:

- **Colunas** -exibir uma grade de 3 linhas com 2 colunas, incluindo, opcionalmente, uma imagem à esquerda de cada linha.
- **StandardBody** -exibir uma cadeia de caracteres de cabeçalho em negrito, com duas linhas de texto sem formatação. O cabeçalho, pode exibir uma imagem à esquerda.
- **Tabela** -exibir uma cadeia de caracteres de cabeçalho em negrito, com uma grade de 2 x 2 de texto abaixo dela. O cabeçalho, pode exibir uma imagem à esquerda.
- **TallBody** -exibir uma cadeia de caracteres de cabeçalho em negrito, com uma maior fonte única linha de texto abaixo.

### <a name="utilitarian-small"></a>Pequeno utilitárias

Esses nomes de classe de modelo são prefixados com `CLKComplicationTemplateUtilitarianSmall`:

- **Simples** -exibe uma imagem e texto em uma única linha (o texto deve ser curto).
- **RingImage** -exibir uma única imagem, com um anel de progresso ao redor dele.
- **RingText** -exibir uma única linha de texto, com um anel de progresso ao redor dele.
- **Quadrado** -exibir uma imagem de quadrados (40px ou 44px quadrada para o 38 mm ou Apple Watch de 42 mm, respectivamente).

### <a name="utilitarian-large"></a>Utilitárias grande

Há apenas um modelo para esse estilo de complicação: `CLKComplicationTemplateUtilitarianLargeFlat`.
Ele exibe uma única imagem e texto, tudo em uma única linha.



## <a name="related-links"></a>Links relacionados

- [Documentos da Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vídeo WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
