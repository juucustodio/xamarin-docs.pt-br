---
title: Gatilhos
description: Responder a alterações de interface do usuário com XAML
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 3b0cba75c8a62d571e82abb3bd2e27cac1e0d075
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="triggers"></a>Gatilhos

Gatilhos permitem expressar ações declarativamente em XAML que alteram a aparência de controles com base em eventos ou alterações de propriedade.

Você pode atribuir um gatilho diretamente a um controle ou adicioná-lo para um dicionário de recursos de nível de página ou no nível do aplicativo a ser aplicado a vários controles.

Há quatro tipos de gatilho:

* [Disparador de propriedade](#property) -ocorre quando uma propriedade em um controle é definida como um valor específico.

* [Gatilho dados](#data) - usa associação de dados para gatilho baseado nas propriedades de outro controle.

* [Gatilho de evento](#event) -ocorre quando ocorre um evento no controle.

* [Gatilho de várias](#multi) -permite que várias condições de gatilho a ser definido antes da ocorrência de uma ação.

<a name="property" />

## <a name="property-triggers"></a>Gatilhos de propriedade

Um gatilho simple pode ser expressos puramente em XAML, adicionando um `Trigger` elemento a um controle aciona a coleção.
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

As partes importantes de declaração do gatilho são:

* **TargetType** -o tipo de controle que o disparador é aplicado.

* **Propriedade** -a propriedade do controle que é monitorado.

* **Valor** -o valor, quando ela ocorre para a propriedade monitorada, que faz com que o gatilho ativar.

* **Setter** -uma coleção de `Setter` elementos podem ser adicionados e quando a condição do gatilho é atendida. Você deve especificar o `Property` e `Value` para definir.

* **EnterActions e ExitActions** (não mostrado) - são escritos em código e pode ser usado além (ou instead of) `Setter` elementos. Eles são [descrito abaixo](#enterexit).

### <a name="applying-a-trigger-using-a-style"></a>Aplicação de um gatilho usando um estilo

Gatilhos também podem ser adicionados a um `Style` declaração em um controle, em uma página ou um aplicativo `ResourceDictionary`. Este exemplo declara um estilo implícito (ie. nenhum `Key` é definido) que significa que se aplicam a todos `Entry` controles na página.

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

Os gatilhos de dados usar associação de dados para monitorar outro controle para fazer com que o `Setter`s for chamado. Em vez do `Property` atributo em um disparador de propriedade, defina o `Binding` atributo para monitorar o valor especificado.

O exemplo a seguir usa a sintaxe de associação de dados `{Binding Source={x:Reference entry}, Path=Text.Length}` que é como fazemos referência a propriedades do controle de outro. Quando o comprimento do `entry` for zero, o gatilho é ativado. Neste exemplo o gatilho desabilita o botão quando a entrada estiver vazia.

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

Dica: ao avaliar `Path=Text.Length` sempre fornecer um valor padrão para a propriedade de destino (por exemplo. `Text=""`) porque, caso contrário, será `null` e o gatilho não funcionará como esperado.

Além de especificar `Setter`s, você também pode fornecer [ `EnterActions` e `ExitActions` ](#enterexit).

<a name="event" />

## <a name="event-triggers"></a>Disparadores de evento

O `EventTrigger` elemento requer apenas um `Event` propriedade, como `"Clicked"` no exemplo a seguir.

```xaml
<EventTrigger Event="Clicked">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Observe que não há nenhum `Setter` elementos, mas em vez disso, uma referência a uma classe definida pelo `local:NumericValidationTriggerAction` que requer o `xmlns:local` para ser declarada na página do XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:WorkingWithTriggers;assembly=WorkingWithTriggers"
```

A própria classe implementa `TriggerAction` que significa que ele deve fornecer uma substituição para o `Invoke` método é chamado sempre que ocorre o evento de gatilho.

Uma implementação de ação do gatilho deve:

* Implementar genérica `TriggerAction<T>` classe com o parâmetro genérico correspondente com o tipo de controle que o gatilho será aplicado a. Você pode usar como superclasse `VisualElement` para ações de gatilho que funcionam com uma variedade de controles, ou especifique um tipo de controle de gravação `Entry`.

* Substituir o `Invoke` método - isso é chamado sempre que os critérios do gatilho forem atendidos.

* Opcionalmente, expõem as propriedades que podem ser definidas no XAML quando o gatilho é declarado (como `Anchor`, `Scale`, e `Length` neste exemplo).

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

As propriedades expostas pela ação do gatilho podem ser definidas na declaração do XAML da seguinte maneira:

```xaml
<EventTrigger Event="TextChanged">
    <local:NumericValidationTriggerAction />
</EventTrigger>
```

Tenha cuidado ao compartilhamento de gatilhos em uma `ResourceDictionary`, uma instância será compartilhada entre os controles para qualquer estado que está configurado de uma vez se aplicará a todos eles.

Observe que não têm suporte para os disparadores de evento `EnterActions` e `ExitActions` [descrito abaixo](#enterexit).

<a name="multi" />

## <a name="multi-triggers"></a>Vários gatilhos

Um `MultiTrigger` é semelhante a um `Trigger` ou `DataTrigger` exceto pode haver mais de uma condição. Todas as condições devem ser verdadeiras antes do `Setter`s são disparados.

Aqui está um exemplo de um gatilho de um botão que vincula a duas entradas diferentes (`email` e `phone`):

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

### <a name="building-a-require-all-multi-trigger"></a>Criar um gatilho de várias "exija que todos os"

O gatilho várias só atualiza seu controle quando todas as condições forem verdadeiras. Testando para "todos os tamanhos de campo são zero" (como uma página de logon em que todas as entradas devem ser concluídas) é complicado porque deseja que uma condição "onde Text.Length > 0", mas isso não pode ser expresso em XAML.

Isso pode ser feito com um `IValueConverter`. O código de conversor abaixo transforma o `Text.Length` de associação em um `bool` que indica se um campo está vazio ou não:


```csharp
public class MultiTriggerConverter : IValueConverter
{
    public object Convert(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        if ((int)value > 0) // length > 0 ?
            return true;            // some data has been entered
        else
            return false;           // input is empty
    }

    public object ConvertBack(object value, Type targetType,
        object parameter, CultureInfo culture)
    {
        throw new NotSupportedException ();
    }
}
```

Para usar esse conversor em um gatilho de várias, primeiro adicioná-lo ao dicionário de recursos da página (junto com um personalizado `xmlns:local` definição de namespace):

```xaml
<ResourceDictionary>
   <local:MultiTriggerConverter x:Key="dataHasBeenEntered" />
</ResourceDictionary>
```

O XAML é mostrado abaixo. Observe as seguintes diferenças do primeiro exemplo de gatilho múltipla:

* O botão tem `IsEnabled="false"` definido por padrão.
* As condições de gatilho várias usá-lo para ativar o `Text.Length` valor em um valor booleano.
* Quando todas as condições forem `true`, o setter torna o botão `IsEnabled` propriedade `true`.

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

Essas capturas de tela mostram a diferença entre os dois exemplos de gatilho de várias acima. Na parte superior das telas, texto de entrada em apenas um `Entry` é suficiente para habilitar o **salvar** botão.
Na parte inferior das telas, o **Login** permanece inativo até que os campos contêm dados.


![](triggers-images/multi-requireall.png "Exemplos multiTrigger")

<a name="enterexit" />

## <a name="enteractions-and-exitactions"></a>EnterActions e ExitActions

Outra maneira de implementar alterações quando ocorre um gatilho é adicionando `EnterActions` e `ExitActions` coleções e especificando `TriggerAction<T>` implementações.

Você pode fornecer *ambos* `EnterActions` e `ExitActions` , bem como `Setter`s em um disparador, mas lembre-se que o `Setter`s são chamadas imediatamente (não esperam a `EnterAction` ou `ExitAction` para Conclua). Como alternativa, você pode executar tudo no código e não usar `Setter`s em todos os.

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

Como sempre, quando uma classe é referenciada em XAML deve declarar um namespace como `xmlns:local` conforme mostrado aqui:

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

Observação: `EnterActions` e `ExitActions` são ignorados em **os disparadores de evento**.



## <a name="related-links"></a>Links relacionados

- [Exemplo de gatilhos](https://developer.xamarin.com/samples/WorkingWithTriggers)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)
