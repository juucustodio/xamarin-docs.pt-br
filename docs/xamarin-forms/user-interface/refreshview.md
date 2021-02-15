---
title: Xamarin.Forms RefreshView
description: O Xamarin.Forms RefreshView é um controle de contêiner que fornece a funcionalidade de pull para atualizar para conteúdo rolável.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
- RefreshView
- Universal Windows Platform
ms.openlocfilehash: 470093465191897a56cd54a6edaf828afbf40e11
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372867"
---
# <a name="no-locxamarinforms-no-locrefreshview"></a>Xamarin.Forms RefreshView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para conteúdo rolável. Portanto, o filho de um `RefreshView` deve ser um controle rolável, como [`ScrollView`](xref:Xamarin.Forms.ScrollView) , [`CollectionView`](xref:Xamarin.Forms.CollectionView) ou [`ListView`](xref:Xamarin.Forms.ListView) .

`RefreshView` define as propriedades a seguir:

- `Command`, do tipo `ICommand` , que é executado quando uma atualização é disparada.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `IsRefreshing`, do tipo `bool` , que indica o estado atual do `RefreshView` .
- `RefreshColor`, do tipo `Color` , a cor do círculo de progresso que aparece durante a atualização.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!NOTE]
> No Universal Windows Platform , a direção de pull de um `RefreshView` pode ser definida com uma plataforma específica. Para obter mais informações, consulte [ RefreshView direção de pull](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-no-locrefreshview"></a>Criar um RefreshView

O exemplo a seguir mostra como criar uma instância de `RefreshView` em XAML:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

Um `RefreshView` também pode ser criado no código:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

Neste exemplo, o `RefreshView` fornece a funcionalidade de pull para atualizar para um [`ScrollView`](xref:Xamarin.Forms.ScrollView) cujo filho é um [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) . O `FlexLayout` usa um layout vinculável para gerar seu conteúdo ligando-se a uma coleção de itens e define a aparência de cada item com um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Para obter mais informações sobre layouts vinculáveis, consulte [layouts Xamarin.Forms vinculáveis no ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

O valor da `RefreshView.IsRefreshing` propriedade indica o estado atual do `RefreshView` . Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para o `true` . Quando a atualização for concluída, você deverá redefinir a propriedade para `false` .

Quando o usuário inicia uma atualização, o `ICommand` definido pela `Command` propriedade é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de um::: no-Loc (RefreshView)::: Atualizando dados, no iOS e no Android](refreshview-images/default-progress-circle.png "::: no-Loc (RefreshView)::: Atualizando dados")](refreshview-images/default-progress-circle-large.png#lightbox "::: no-Loc (RefreshView)::: Atualizando dados")

> [!NOTE]
> Definir manualmente a `IsRefreshing` propriedade como disparará `true` a visualização de atualização e executará o `ICommand` definido pela `Command` propriedade.

## <a name="no-locrefreshview-appearance"></a>RefreshView aparência

Além das propriedades `RefreshView` herdadas da [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, `RefreshView` também define a `RefreshColor` propriedade. Essa propriedade pode ser definida para definir a cor do círculo de progresso que aparece durante a atualização:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

A captura de tela a seguir mostra um `RefreshView` com o `RefreshColor` conjunto de propriedades:

[![Captura de tela de um::: no-Loc (RefreshView)::: com um círculo de progresso azul-petróleo, no iOS e no Android](refreshview-images/teal-progress-circle.png "::: no-Loc (RefreshView)::: com um círculo de progresso azul-petróleo")](refreshview-images/teal-progress-circle-large.png#lightbox "::: no-Loc (RefreshView)::: com um círculo de progresso azul-petróleo")

Além disso, a `BackgroundColor` propriedade pode ser definida como um [`Color`](xref:Xamarin.Forms.Color) que representa a cor do plano de fundo do círculo de progresso.

> [!NOTE]
> No iOS, a `BackgroundColor` propriedade define a cor do plano de fundo do `UIView` que contém o círculo de progresso.

## <a name="disable-a-no-locrefreshview"></a>Desabilitar um RefreshView

Um aplicativo pode inserir um estado em que o pull para atualizar não é uma operação válida. Nesses casos, o `RefreshView` pode ser desabilitado definindo sua `IsEnabled` propriedade como `false` . Isso impedirá que os usuários possam disparar pull para atualização.

Como alternativa, ao definir a `Command` propriedade, o `CanExecute` delegado de `ICommand` pode ser especificado para habilitar ou desabilitar o comando.

## <a name="related-links"></a>Links relacionados

- [RefreshView Nova](/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [Layouts vinculáveis em Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [RefreshView Direção específica da plataforma de pull](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)