---
title: RefreshView Xamarin. Forms
description: O RefreshView Xamarin. Forms é um controle de contêiner que fornece a funcionalidade de pull para atualizar para conteúdo rolável.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
ms.openlocfilehash: b53c58a5e859bf7752855c3954666a062261599d
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697419"
---
# <a name="xamarinforms-refreshview"></a>RefreshView Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)

O `RefreshView` é um controle de contêiner que fornece a funcionalidade de pull para atualizar para conteúdo rolável. Portanto, o filho de um `RefreshView` deve ser um controle rolável, como [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`CollectionView`](xref:Xamarin.Forms.CollectionView)ou [`ListView`](xref:Xamarin.Forms.ListView).

`RefreshView` define as seguintes propriedades:

- `Command`, do tipo `ICommand`, que é executado quando uma atualização é disparada.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `IsRefreshing`, do tipo `bool`, que indica o estado atual da `RefreshView`.
- `RefreshColor`, do tipo `Color`, a cor do círculo de progresso que aparece durante a atualização.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que elas podem ser destinos de associações de dados e estilizadas.

> [!NOTE]
> Na Plataforma Universal do Windows, a direção de pull de um `RefreshView` pode ser definida com uma plataforma específica. Para obter mais informações, consulte [direção de pull do RefreshView](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Criar um RefreshView

O exemplo a seguir mostra como criar uma instância de um `RefreshView` em XAML:

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

Neste exemplo, o `RefreshView` fornece a funcionalidade de pull para atualizar para um [`ScrollView`](xref:Xamarin.Forms.ScrollView) cujo filho é um [`FlexLayout`](xref:Xamarin.Forms.FlexLayout). O `FlexLayout` usa um layout vinculável para gerar seu conteúdo ligando-se a uma coleção de itens e define a aparência de cada item com um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obter mais informações sobre layouts vinculáveis, consulte [layouts vinculáveis no Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

O valor da propriedade `RefreshView.IsRefreshing` indica o estado atual da `RefreshView`. Quando uma atualização for disparada pelo usuário, essa propriedade passará automaticamente para `true`. Quando a atualização for concluída, você deverá redefinir a propriedade para `false`.

Quando o usuário inicia uma atualização, o `ICommand` definido pela propriedade `Command` é executado, o que deve atualizar os itens que estão sendo exibidos. Uma visualização de atualização é mostrada enquanto a atualização ocorre, que consiste em um círculo de progresso animado:

[![Captura de tela de um RefreshView atualizando dados, no iOS e no Android](refreshview-images/default-progress-circle.png "RefreshView atualizando dados")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView atualizando dados")

> [!NOTE]
> Definir manualmente a propriedade `IsRefreshing` como `true` disparará a visualização de atualização e executará o `ICommand` definido pela propriedade `Command`.

## <a name="refreshview-appearance"></a>RefreshView aparência

Além das propriedades que `RefreshView` herda da classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , `RefreshView` também define a propriedade `RefreshColor`. Essa propriedade pode ser definida para definir a cor do círculo de progresso que aparece durante a atualização:

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

A captura de tela a seguir mostra uma `RefreshView` com o conjunto de propriedades `RefreshColor`:

[![Captura de tela de um RefreshView com um círculo de progresso azul-petróleo, no iOS e no Android](refreshview-images/teal-progress-circle.png "RefreshView com um círculo de progresso azul-petróleo")](refreshview-images/teal-progress-circle-large.png#lightbox "RefreshView com um círculo de progresso azul-petróleo")

Além disso, a propriedade `BackgroundColor` pode ser definida como um [`Color`](xref:Xamarin.Forms.Color) que representa a cor do plano de fundo do círculo de progresso.

> [!NOTE]
> No iOS, a propriedade `BackgroundColor` define a cor do plano de fundo do `UIView` que contém o círculo de progresso.

## <a name="disable-a-refreshview"></a>Desabilitar um RefreshView

Um aplicativo pode inserir um estado em que o pull para atualizar não é uma operação válida. Nesses casos, o `RefreshView` pode ser desabilitado definindo sua propriedade `IsEnabled` como `false`. Isso impedirá que os usuários possam disparar pull para atualização.

Como alternativa, ao definir a propriedade `Command`, o delegado de `CanExecute` do `ICommand` pode ser especificado para habilitar ou desabilitar o comando.

## <a name="related-links"></a>Links relacionados

- [RefreshView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshview/)
- [Layouts vinculáveis no Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [Direção de recepção RefreshView específica da plataforma](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)
