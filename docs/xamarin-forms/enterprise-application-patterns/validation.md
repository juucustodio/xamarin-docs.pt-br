---
title: Validação em aplicativos empresariais
description: Este capítulo explica como o aplicativo móvel eShopOnContainers executa a validação da entrada do usuário. Isso inclui a especificação de regras de validação, o disparo da validação e a exibição de erros de validação.
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70760160"
---
# <a name="validation-in-enterprise-apps"></a>Validação em aplicativos empresariais

Qualquer aplicativo que aceite entrada de usuários deve garantir que a entrada seja válida. Um aplicativo poderia, por exemplo, verificar se há entradas que contenham apenas caracteres em um intervalo específico, tem um determinado comprimento ou corresponde a um formato específico. Sem a validação, um usuário pode fornecer dados que causam falha no aplicativo. A validação impõe regras de negócio e impede que um invasor insira dados mal-intencionados.

No contexto do padrão Model-View-ViewModel (MVVM), um modelo de exibição ou modelo geralmente será necessário para executar a validação de dados e sinalizar quaisquer erros de validação para o modo de exibição para que o usuário possa corrigi-los. O aplicativo móvel eShopOnContainers executa a validação síncrona do lado do cliente das propriedades do modelo de exibição e notifica o usuário sobre quaisquer erros de validação, destacando o controle que contém os dados inválidos e exibindo as mensagens de erro que informam ao usuário de por que os dados são inválidos. A Figura 6-1 mostra as classes envolvidas na execução da validação no aplicativo móvel eShopOnContainers.

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**Figura 6-1**: classes de validação no aplicativo móvel eShopOnContainers

As propriedades do modelo de exibição que exigem validação são do tipo `ValidatableObject<T>` e cada instância de `ValidatableObject<T>` tem regras de validação adicionadas à sua propriedade `Validations`. A validação é invocada do modelo de exibição chamando o método `Validate` da instância `ValidatableObject<T>`, que recupera as regras de validação e as executa na propriedade `ValidatableObject<T>` `Value`. Todos os erros de validação são colocados na propriedade `Errors` da instância `ValidatableObject<T>`, e a propriedade `IsValid` da instância `ValidatableObject<T>` é atualizada para indicar se a validação foi bem-sucedida ou falhou.

A notificação de alteração de propriedade é fornecida pela classe `ExtendedBindableObject` e, portanto, um controle de [`Entry`](xref:Xamarin.Forms.Entry) pode ser associado à propriedade `IsValid` da instância `ValidatableObject<T>` na classe do modelo de exibição para ser notificado de se os dados inseridos são válidos ou não.

## <a name="specifying-validation-rules"></a>Especificando regras de validação

As regras de validação são especificadas pela criação de uma classe derivada da interface `IValidationRule<T>`, que é mostrada no exemplo de código a seguir:

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

Essa interface especifica que uma classe de regra de validação deve fornecer um método de `Check` `boolean` que é usado para executar a validação necessária e uma propriedade `ValidationMessage` cujo valor é a mensagem de erro de validação que será exibida se a validação falhar.

O exemplo de código a seguir mostra a `IsNotNullOrEmptyRule<T>` regra de validação, que é usada para executar a validação do nome de usuário e senha inseridos pelo usuário no `LoginView` ao usar serviços fictícios no aplicativo móvel eShopOnContainers:

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

O método `Check` retorna um `boolean` indicando se o argumento de valor é `null`, vazio ou se consiste apenas em caracteres de espaço em branco.

Embora não seja usado pelo aplicativo móvel eShopOnContainers, o exemplo de código a seguir mostra uma regra de validação para validar endereços de email:

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

O método `Check` retorna um `boolean` indicando se o argumento de valor é um endereço de email válido ou não. Isso é obtido pesquisando o argumento de valor da primeira ocorrência do padrão de expressão regular especificado no construtor de `Regex`. Se o padrão de expressão regular foi encontrado na cadeia de caracteres de entrada pode ser determinado verificando o valor da propriedade `Success` do objeto de `Match`.

> [!NOTE]
> Às vezes, a validação da propriedade pode envolver propriedades dependentes. Um exemplo de propriedades dependentes é quando o conjunto de valores válidos para a propriedade A depende do valor específico que foi definido na propriedade B. Para verificar se o valor da propriedade A é um dos valores permitidos envolveria a recuperação do valor da propriedade B. Além disso, quando o valor da propriedade B for alterado, a propriedade A deverá ser revalidada.

## <a name="adding-validation-rules-to-a-property"></a>Adicionando regras de validação a uma propriedade

No aplicativo móvel eShopOnContainers, exiba as propriedades do modelo que exigem validação são declaradas como sendo do tipo `ValidatableObject<T>`, em que `T` é o tipo dos dados a serem validados. O exemplo de código a seguir mostra um exemplo de duas propriedades desse tipo:

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

Para que a validação ocorra, as regras de validação devem ser adicionadas à coleção de `Validations` de cada instância de `ValidatableObject<T>`, conforme demonstrado no exemplo de código a seguir:

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

Esse método adiciona a regra de validação de `IsNotNullOrEmptyRule<T>` à coleção de `Validations` de cada instância de `ValidatableObject<T>`, especificando valores para a propriedade `ValidationMessage` da regra de validação, que especifica a mensagem de erro de validação que será exibida se a validação falhar.

## <a name="triggering-validation"></a>Gatilho de validação

A abordagem de validação usada no aplicativo móvel eShopOnContainers pode disparar manualmente a validação de uma propriedade e acionar automaticamente a validação quando uma propriedade é alterada.

### <a name="triggering-validation-manually"></a>Acionando a validação manualmente

A validação pode ser acionada manualmente para uma propriedade de modelo de exibição. Por exemplo, isso ocorre no aplicativo móvel eShopOnContainers quando o usuário toca no botão de **logon** na `LoginView`, ao usar serviços fictícios. O delegado de comando chama o método `MockSignInAsync` no `LoginViewModel`, que invoca a validação executando o método `Validate`, que é mostrado no seguinte exemplo de código:

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

O método `Validate` executa a validação do nome de usuário e a senha inseridos pelo usuário no `LoginView`, invocando o método Validate em cada instância de `ValidatableObject<T>`. O exemplo de código a seguir mostra o método Validate da classe `ValidatableObject<T>`:

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

Esse método limpa a coleção de `Errors` e, em seguida, recupera as regras de validação que foram adicionadas à coleção de `Validations` do objeto. O método de `Check` para cada regra de validação recuperada é executado e o valor da propriedade `ValidationMessage` para qualquer regra de validação que não valida os dados é adicionado à coleção de `Errors` da instância de `ValidatableObject<T>`. Por fim, a propriedade `IsValid` é definida, e seu valor é retornado para o método de chamada, indicando se a validação foi bem-sucedida ou falhou.

### <a name="triggering-validation-when-properties-change"></a>Acionando a validação quando as propriedades são alteradas

A validação também pode ser disparada sempre que uma propriedade associada for alterada. Por exemplo, quando uma associação bidirecional no `LoginView` define a propriedade `UserName` ou `Password`, a validação é disparada. O exemplo de código a seguir demonstra como isso ocorre:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

O controle de [`Entry`](xref:Xamarin.Forms.Entry) associa-se à propriedade `UserName.Value` da instância de `ValidatableObject<T>` e a coleção de `Behaviors` do controle tem uma instância de `EventToCommandBehavior` adicionada a ela. Esse comportamento executa o `ValidateUserNameCommand` em resposta ao acionamento do evento [`TextChanged`] no `Entry`, que é gerado quando o texto no `Entry` é alterado. Por sua vez, o `ValidateUserNameCommand` delegado executa o método `ValidateUserName`, que executa o método `Validate` na instância `ValidatableObject<T>`. Portanto, toda vez que o usuário insere um caractere no controle de `Entry` para o nome de usuário, a validação dos dados inseridos é executada.

Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>Exibindo erros de validação

O aplicativo móvel eShopOnContainers notifica o usuário sobre quaisquer erros de validação, realçando o controle que contém os dados inválidos com uma linha vermelha e exibindo uma mensagem de erro informando ao usuário por que os dados são inválidos abaixo do controle que contém o dados inválidos. Quando os dados inválidos são corrigidos, a linha é alterada para preto e a mensagem de erro é removida. A Figura 6-2 mostra o LoginView no aplicativo móvel eShopOnContainers quando erros de validação estão presentes.

![](validation-images/validation-login.png "Displaying validation errors during login")

**Figura 6-2:** Exibindo erros de validação durante o logon

### <a name="highlighting-a-control-that-contains-invalid-data"></a>Realçando um controle que contém dados inválidos

O comportamento `LineColorBehavior` anexado é usado para realçar [`Entry`](xref:Xamarin.Forms.Entry) controles em que ocorreram erros de validação. O exemplo de código a seguir mostra como o comportamento de `LineColorBehavior` anexado é anexado a um controle de `Entry`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

O controle de [`Entry`](xref:Xamarin.Forms.Entry) consome um estilo explícito, que é mostrado no exemplo de código a seguir:

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

Esse estilo define as propriedades `ApplyLineColor` e `LineColor` anexadas do comportamento `LineColorBehavior` anexado no controle de [`Entry`](xref:Xamarin.Forms.Entry) . Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

Quando o valor da propriedade anexada `ApplyLineColor` é definido ou alterado, o comportamento `LineColorBehavior` anexado executa o método `OnApplyLineColorChanged`, que é mostrado no seguinte exemplo de código:

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

Os parâmetros para esse método fornecem a instância do controle ao qual o comportamento está anexado e os valores novos e antigos da propriedade `ApplyLineColor` anexada. A classe `EntryLineColorEffect` é adicionada à coleção de [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle se a propriedade `ApplyLineColor` anexada for `true`, caso contrário, ela será removida da coleção de `Effects` do controle. Para obter mais informações sobre comportamentos, consulte [implementando comportamentos](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors).

A `EntryLineColorEffect` subclasses a classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) e é mostrada no exemplo de código a seguir:

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

A classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) representa um efeito independente da plataforma que encapsula um efeito interno específico da plataforma. Isso simplifica o processo de remoção do efeito, porque não há nenhum acesso de tempo de compilação às informações de tipo para um efeito específico da plataforma. O `EntryLineColorEffect` chama o construtor da classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva especificada em cada classe de efeito específica da plataforma.

O exemplo de código a seguir mostra a implementação de `eShopOnContainers.EntryLineColorEffect` para iOS:

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

O método `OnAttached` recupera o controle nativo para o controle de [`Entry`](xref:Xamarin.Forms.Entry) do Xamarin. Forms e atualiza a cor da linha chamando o método `UpdateLineColor`. A substituição de `OnElementPropertyChanged` responderá às alterações de propriedade vinculáveis no controle de `Entry` atualizando a cor da linha se a propriedade `LineColor` anexada for alterada ou a propriedade [`Height`](xref:Xamarin.Forms.VisualElement.Height) da `Entry` for alterada. Para obter mais informações sobre efeitos, confira [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Quando dados válidos são inseridos no controle de [`Entry`](xref:Xamarin.Forms.Entry) , ele aplicará uma linha preta na parte inferior do controle, para indicar que não há nenhum erro de validação. A Figura 6-3 mostra um exemplo disso.

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**Figura 6-3**: linha preta que indica nenhum erro de validação

O controle de [`Entry`](xref:Xamarin.Forms.Entry) também tem um [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) adicionado à sua coleção de [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) . O exemplo de código a seguir mostra o `DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

Esse [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) monitora a propriedade `UserName.IsValid` e, se o valor for `false`, ele executará o [`Setter`](xref:Xamarin.Forms.Setter), que altera a propriedade `LineColor` anexada do comportamento `LineColorBehavior` anexado para vermelho. A Figura 6-4 mostra um exemplo disso.

![](validation-images/validation-redline.png "Red line indicating validation error")

**Figura 6-4**: linha vermelha indicando erro de validação

A linha no controle de [`Entry`](xref:Xamarin.Forms.Entry) permanecerá vermelha enquanto os dados inseridos forem inválidos, caso contrário, ele será alterado para preto para indicar que os dados inseridos são válidos.

Para obter mais informações sobre gatilhos, consulte [gatilhos](~/xamarin-forms/app-fundamentals/triggers.md).

### <a name="displaying-error-messages"></a>Exibindo mensagens de erro

A interface do usuário exibe mensagens de erro de validação em controles de rótulo abaixo de cada controle cujos dados falharam na validação. O exemplo de código a seguir mostra o [`Label`](xref:Xamarin.Forms.Label) que exibe uma mensagem de erro de validação se o usuário não tiver inserido um nome de usuário válido:

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

Cada [`Label`](xref:Xamarin.Forms.Label) é associado à propriedade `Errors` do objeto de modelo de exibição que está sendo validado. A propriedade `Errors` é fornecida pela classe `ValidatableObject<T>` e é do tipo `List<string>`. Como a propriedade `Errors` pode conter vários erros de validação, a instância `FirstValidationErrorConverter` é usada para recuperar o primeiro erro da coleção para exibição.

## <a name="summary"></a>Resumo

O aplicativo móvel eShopOnContainers executa a validação síncrona do lado do cliente das propriedades do modelo de exibição e notifica o usuário sobre quaisquer erros de validação, destacando o controle que contém os dados inválidos e exibindo as mensagens de erro que informam ao usuário Por que os dados são inválidos.

As propriedades do modelo de exibição que exigem validação são do tipo `ValidatableObject<T>` e cada instância de `ValidatableObject<T>` tem regras de validação adicionadas à sua propriedade `Validations`. A validação é invocada do modelo de exibição chamando o método `Validate` da instância `ValidatableObject<T>`, que recupera as regras de validação e as executa na propriedade `ValidatableObject<T>` `Value`. Todos os erros de validação são colocados na propriedade `Errors` do `ValidatableObject<T>`instance, e a propriedade `IsValid` da instância `ValidatableObject<T>` é atualizada para indicar se a validação foi bem-sucedida ou falhou.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
