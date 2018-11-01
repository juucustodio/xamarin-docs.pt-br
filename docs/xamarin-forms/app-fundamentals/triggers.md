---
title: Gatilhos do xamarin. Forms
description: Este artigo explica como usar gatilhos de xamarin. Forms para responder a alterações na interface do usuário com XAML. Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: e9ec9288e2b8ea991ef8d41f9b601d0897631b9d
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675205"
---
# <a name="xamarinforms-triggers"></a>Gatilhos do xamarin. Forms

Os gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência dos controles com base em eventos ou alterações de propriedade.

Você pode atribuir um gatilho diretamente a um controle ou adicioná-lo para um dicionário de recursos de nível de página ou aplicativo a ser aplicado a vários controles.

Há quatro tipos de gatilho:

* [Gatilho de propriedade](#property) -ocorre quando uma propriedade em um controle é definida como um valor específico.

* [Gatilho de dados](#data) - usa associação de dados para disparar com base nas propriedades de outro controle.

* [Gatilho de evento](#event) -ocorre quando ocorre um evento do controle.

* [Gatilho de várias](#multi) -permite que várias condições de gatilho a ser definido antes que uma ação ocorra.

<a name="property" />

## <a name="property-triggers"></a>Gatilhos de propriedade

Um gatilho simples pode ser expressos puramente em XAML, adicionando um `Trigger` elemento a um controle aciona a coleção.
Este exemplo mostra um gatilho que altera um `Entry` cor do plano de fundo quando ele recebe o foco:

```xaml
<Entry Placeholder="enter name">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
             Property="IsFocused" Value="True">
            <Setter Property="BackgroundColor" Value="Yellow" />
        </Trigger>
    </Entry.Triggers>
</Entry>
```

As partes importantes da declaração do gatilho são:

* **TargetType** -o que o gatilho se aplica ao tipo de controle.

* **Propriedade** -a propriedade no controle que é monitorado.

* **Valor** -o valor, quando ele ocorre para a propriedade monitorada, que faz com que o gatilho ativar.

* **Setter** -uma coleção de `Setter` elementos podem ser adicionados e quando a condição do gatilho é atendida. Você deve especificar o `Property` e `Value` para definir.

* **EnterActions e ExitActions** (não mostrado) – são escritos em código e pode ser usado além (ou instead of) `Setter` elementos. Eles são [descrito abaixo](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Aplicação de um gatilho usando um estilo

Gatilhos também podem ser adicionados a um `Style` declaração em um controle, em uma página ou um aplicativo `ResourceDictionary`. Este exemplo declara um estilo implícito (ie. não há `Key` está definido) que significa que se aplicam a todos `Entry` controles na página.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style TargetType="Entry">
                        <Style.Triggers>
                <Trigger TargetType="Entry"
                         Property="IsFocused" Value="True">
                    <Setter Property="BackgroundColor" Value="Yellow" />
                </Trigger>
            </Style.Triggers>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>
```

<a name="data" />

## <a name="data-triggers"></a>Gatilhos de dados

Gatilhos de dados usam a ligação de dados para monitorar a outro controle para fazer com que o `Setter`s seja chamado. Em vez do `Property` de atributo em um gatilho de propriedade, defina o `Binding` atributo para monitorar o valor especificado.

O exemplo a seguir usa a sintaxe de associação de dados `{Binding Source={x:Reference entry}, Path=Text.Length}`
o que é como nos referimos a propriedades do controle para outro. Quando o comprimento do `entry` for zero, o gatilho é ativado. Neste exemplo o gatilho desabilita o botão quando a entrada estiver vazia.

```xaml
<!-- the x:Name is referenced below in DataTrigger-->
<!-- tip: make sure to set the Text="" (or some other default) -->
<Entry x:Name="entry"
       Text=""
       Placeholder="required field" />

<Button x:Name="button" Text="Save"
        FontSize="Large"
        HorizontalOptions="Center">
    <Button.Triggers>
        <DataTrigger TargetType="Button"
                     Binding="{Binding Source={x:Reference entry},
                                       Path=Text.Length}"
                     Value="0">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </Button.Triggers>
</Button>
```

Dica: ao avaliar `Path=Text.Length` sempre fornecer um valor padrão para a propriedade de destino (por exemplo. `Text=""`) pois caso contrário, ela será `null` e o gatilho não funcionará como esperado.

Além de especificar `Setter`s, você também pode fornecer [ `EnterActions` e `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Gatilhos de evento

O `EventTrigger` elemento requer apenas um `Event` propriedade, tal como `"Clicked"` no exemplo a seguir.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Observe que há nenhum `Setter` elementos, mas em vez disso, uma referência a uma classe definida pelo `local:NumericValidationTriggerAction` que requer o `xmlns:local` para ser declarada na página do XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

A classe em si implementa `TriggerAction` que significa que ele deve fornecer uma substituição para o `Invoke` método é chamado sempre que ocorre o evento de gatilho.

Uma implementação de ação de gatilho deve:

* Implementar o genérico `TriggerAction<T>` classe, com o parâmetro genérico correspondente com o tipo de controle que o gatilho será aplicado a. Você pode usar como superclassificar `VisualElement` gravar ações de gatilho que funcionam com uma variedade de controles, ou especificam um tipo de controle, como `Entry`.

* Substituir o `Invoke` método – isso é chamado sempre que os critérios do gatilho forem atendidos.

* Outra opção é expor propriedades que podem ser definidas no XAML quando o gatilho é declarado (como `Anchor`, `Scale`, e `Length` neste exemplo).

```csharp
public class NumericValidationTriggerAction : TriggerAction<Entry>
{
    protected override void Invoke (Entry entry)
    {
        double result;
        bool isValid = Double.TryParse (entry.Text, out result);
        entry.TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

As propriedades expostas pela ação de gatilho podem ser definidas na declaração de XAML da seguinte maneira:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenha cuidado ao compartilhar disparadores em uma `ResourceDictionary`, uma instância será compartilhada entre os controles para que qualquer estado que está configurado de uma vez se aplicará a todos eles.

Observe que não têm suporte para gatilhos de evento `EnterActions` e `ExitActions` [descrita abaixo](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Vários gatilhos

Um `MultiTrigger` é semelhante a um `Trigger` ou `DataTrigger` , exceto que pode haver mais de uma condição. Todas as condições devem ser verdadeiras antes do `Setter`s são disparados.

Aqui está um exemplo de um gatilho para um botão que vincula a duas entradas diferentes (`email` e `phone`):

```xaml
<MultiTrigger TargetType="Button">
    <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference email},
                                   Path=Text.Length}"
                               Value="0" />
        <BindingCondition Binding="{Binding Source={x:Reference phone},
                                   Path=Text.Length}"
                               Value="0" />
    </MultiTrigger.Conditions>

  <Setter Property="IsEnabled" Value="False" />
    <!-- multiple Setter elements are allowed -->
</MultiTrigger>
```

O `Conditions` coleção também pode conter `PropertyCondition` elementos como este:

```xaml
<PropertyCondition Property="Text" Value="OK" />
```

### <a name="building-a-require-all-multi-trigger"></a>Criar um gatilho de vários "necessitar de todos os"

O gatilho multi apenas atualiza seu controle quando todas as condições forem verdadeiras. Teste para "todos os tamanhos de campo são zero" (como uma página de logon onde todas as entradas devem ser concluídas) é complicado porque você deseja que uma condição "onde Text.Length > 0", mas isso não pode ser expressos em XAML.

Isso pode ser feito com um `IValueConverter`. O código de conversor abaixo transformações de `Text.Length` associação em um `bool` que indica se um campo está vazio ou não:

```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;            // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Para usar esse conversor em um gatilho de várias, primeiro o adicione ao dicionário de recursos da página (junto com um personalizado `xmlns:local` definição de namespace):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

O XAML é mostrado abaixo. Observe as seguintes diferenças do primeiro exemplo de gatilho de múltipla:

* O botão tem `IsEnabled="false"` definido por padrão.
* As condições de gatilho multi usam o conversor para transformar a `Text.Length` de valor em um `boolean`.
* Quando todas as condições forem `true`, o botão faz com que o setter `IsEnabled` propriedade `true`.

```xaml
<Entry x:Name="user" Text="" Placeholder="user name" />

<Entry x:Name="pwd" Text="" Placeholder="password" />

<Button x:Name="loginButton" Text="Login"
        FontSize="Large"
        HorizontalOptions="Center"
        IsEnabled="false">
  <Button.Triggers>
    <MultiTrigger TargetType="Button">
      <MultiTrigger.Conditions>
        <BindingCondition Binding="{Binding Source={x:Reference user},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
        <BindingCondition Binding="{Binding Source={x:Reference pwd},
                              Path=Text.Length,
                              Converter={StaticResource dataHasBeenEntered}}"
                          Value="true" />
      </MultiTrigger.Conditions>
      <Setter Property="IsEnabled" Value="True" />
    </MultiTrigger>
  </Button.Triggers>
</Button>
```

Essas capturas de tela mostram a diferença entre os dois exemplos de gatilho de várias acima. Na parte superior das telas, texto de entrada em apenas um `Entry` é o suficiente para habilitar o **salvar** botão.
Na parte inferior das telas, o **Login** botão permanece inativo até que ambos os campos contêm dados.

![](triggers-images/multi-requireall.png "Exemplos multiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions e ExitActions

Outra maneira de implementar alterações quando ocorre um gatilho é adicionando `EnterActions` e `ExitActions` coleções e especificando `TriggerAction<T>` implementações.

Você pode fornecer *ambos* `EnterActions` e `ExitActions` , bem como `Setter`s em um gatilho, mas lembre-se que o `Setter`s são chamados imediatamente (eles não aguardarem que o `EnterAction` ou `ExitAction` para Conclua). Como alternativa, você pode executar tudo no código e não usar `Setter`s nada.

```xaml
<Entry Placeholder="enter job title">
    <Entry.Triggers>
        <Trigger TargetType="Entry"
                 Property="Entry.IsFocused" Value="True">
            <Trigger.EnterActions>
                <local:FadeTriggerAction StartsFrom="0"" />
            </Trigger.EnterActions>

            <Trigger.ExitActions>
                <local:FadeTriggerAction StartsFrom="1" />
            </Trigger.ExitActions>
                        <!-- You can use both Enter/Exit and Setter together if required -->
        </Trigger>
    </Entry.Triggers>
</Entry>
```

Como sempre, quando uma classe é referenciada no XAML você deve declarar um namespace como `xmlns:local` conforme mostrado aqui:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

O `FadeTriggerAction` código é mostrado abaixo:

```csharp
public class FadeTriggerAction : TriggerAction<VisualElement>
{
    public FadeTriggerAction() {}

    public int StartsFrom { set; get; }

    protected override void Invoke (VisualElement visual)
    {
            visual.Animate("", new Animation( (d)=>{
                var val = StartsFrom==1 ? d : 1-d;
                visual.BackgroundColor = Color.FromRgb(1, val, 1);

            }),
            length:1000, // milliseconds
            easing: Easing.Linear);
    }
}
```

Observação: `EnterActions` e `ExitActions` são ignorados em **gatilhos de evento**.



## <a name="related-links"></a>Links relacionados

- [Exemplo de gatilhos](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentação da API de xamarin. Forms](xref:Xamarin.Forms.TriggerAction`1)
